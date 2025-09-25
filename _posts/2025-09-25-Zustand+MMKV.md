---
layout: post
title: Zustand + MMKV 연동하기
tags:
  - GooApp
  - Zustand
  - MMKV
description: Zustand Persist 미들웨어를 사용해 MMKV와 연동하기
image: /images/logos/gooapp_logo.png
comments: true
---

GooApp을 제작하면서 사용한 로컬 스토리지인 [MMKV storage (react-native-mmkv)](https://github.com/mrousavy/react-native-mmkv)와 상태관리 모듈인 Zustand를 연동해 화면 랜더링시 로컬 저장 데이터를 자동으로 Zustand 상태와 동기화 해주는 Persist 미들웨어의 사용법을 정리했다.

### 문제 상황

Zustand와 MMKV를 따로 사용하면서 Zustand 상태와 MMKV의 로컬 데이터를 일일이 연동시켜줘야 하는 불편함이 있었다.
첫 랜더링시 로컬 데이터를 불러와 Zustand에 저장하고, 앱 실행 과정에서 데이터가 바뀌면 그 때마다 Zustand 상태 변경과 직접 로컬에 반영하는 과정을 반복해야 했다.

```ts
const createUser = async () => {
  const newUser = await userDataAPI.createUser(); // 유저 생성

  // 데이터를 두 번씩 저장하는 번거로움 발생
  setLocalUserData(newUser); // MMKV 데이터 저장
  setUser(newUser); // Zustand 상태 저장
};

useEffect(() => {
  // 직접 데이터를 불러와야 함
  const currentUser = getLocalUserData();
  if (currentUser) {
    setUser(currentUser);
  } else {
    createUser();
  }
}, []);
```

### 시도한 방법

MMKV에 데이터를 저장할 때 JSON 파싱이 필요했기 때문에 간단한 사용을 위해서 데이터 저장하고 불러오는 함수를 따로 만들어 사용해왔다.

```ts
// User 데이터를 저장하는 함수
export function setLocalUserData(userData: UserDataType) {
  const dataString = JSON.stringify(userData);
  mmkvStorage.set('userData', dataString);
}
// User 데이터를 불러오는 함수
export function getLocalUserData(): UserDataType | undefined {
  const dataString = mmkvStorage.getString('userData');
  if (dataString) return JSON.parse(dataString) as UserDataType;
}
```

상태가 변경되는 것을 감지해 자동 저장을 하는 커스텀 훅을 만들어 사용하는 생각도 해봤지만 근본적으로 달라지는건 없으니 마음에 들지 않았다.
리서칭을 하다가 Zustand Persist 미들웨어를 발견했고 MMKV에서도 지원한다는것을 알게되었다.

### 해결 방법

MMKV 문서에 나와있는 Zustand persist 연결 방법을 찾아서 연결했다.

#### 연결을 위한 Custom Storage 생성

- 기존의 `mmkvStorage`를 `createJSONStorage`로 감싸 JSON 데이터로 사용할 수 있도록 만들어줬다.
- `createJSONStorage` : 로컬 스토리지 데이터를 JSON 형태로 직렬화/역직렬화해서 Zustand Persist 미들웨어가 사용할 수 있게 변환하는 함수
- 인자로 데이터를 관리하는 객체 `StateStorage ({ setItem, getItem, removeItem })`를 반환하는 함수를 받는다.

> [Zustand \#persist-middleware](https://github.com/pmndrs/zustand?tab=readme-ov-file#persist-middleware) 문서에서는 `createJSONStorage`를 persist 미들웨어 안에서 사용하지만 `localStorage`는 persist로 연결해서만 사용하기 때문에 미리 감싸주었다.

```ts
// 기존에 사용하던 기본 로컬 저장소
export const mmkvStorage = new MMKV({
  id: 'STORAGE ID',
  encryptionKey: MMKV_KEY,
});

// MMKV를 Zustand persist로 연결하기 위한 Custom Storage 객체
export const localStorage = createJSONStorage(() => ({
  setItem: (name, value) => {
    return mmkvStorage.set(name, value);
  },
  getItem: (name) => {
    const value = mmkvStorage.getString(name);
    return value ?? null;
  },
  removeItem: (name) => {
    return mmkvStorage.delete(name);
  },
}));
```

#### Persist 미들웨어로 연결한 스토어 생성

persist 미들웨어를 사용해 각각 데이터 별로 상태 스토어를 만든다.

- 인자로 `스토어 생성 함수`, `저장소 설정 객체`를 넣어준다.
- 스토어 생성 함수 : 기본적인 Zustand 스토어를 만드는 생성 함수
- 저장소 설정 객체 : 로컬 저장소 연결을 위한 정보 객체
  - `name` : MMKV에 저장되는 키를 말함 → 해당 키를 통해서 JSON 데이터가 저장됨
  - `storage` : 위에서 만든 Custom Storage

```ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { localStorage } from './mmkvStorage';
import { UserDataType } from './dataTypes';

type UserStoreType = {
  user: UserDataType;
  setUser: (state: UserDataType) => void;
};

const userLocalStore = create<UserStoreType>()(
  persist(
    (set) => ({
      user: { _id: undefined, nickname: '', tag: 0, rank: 0, topScore: 0 },
      setUser: (state) => set({ user: state }),
    }),
    {
      name: 'userStore',
      storage: localStorage,
    }
  )
);
```

#### 스토어를 불러와 사용 + useShallow

기존 Zustand 스토어를 사용하듯이 생성한 스토어를 사용하면 자동으로 데이터를 불러오고 동기화시켜준다.

```ts
import userLocalStore from '@/stores/userStore';

const user = userLocalStore((state) => state.user);
```

`useShallow`를 사용하면 여러개의 상태를 한번에 불러올 수 있다.

```ts
import { useShallow } from 'zustand/react/shallow';

const [user, setUser] = userLocalStore(
  useShallow((state) => [state.user, state.setUser])
);
```

## 배운점

이미 있는 기능을 다시 만드는 것은 시간낭비고 제작자가 만든 기능이 훨씬 사용성이 좋을 수 있기 때문에 내 생각대로 작업하기 전에 라이브러리 제작자가 의도한 기능이 있는지 먼저 확인하는게 좋을 것 같다.

## 참고

- [MMKV storage (react-native-mmkv)](https://github.com/mrousavy/react-native-mmkv)
- [Zustand - Persisting store data](https://zustand.docs.pmnd.rs/integrations/persisting-store-data)
- [MMKV persist-middleware wrapper](https://github.com/mrousavy/react-native-mmkv/blob/v3/docs/WRAPPER_ZUSTAND_PERSIST_MIDDLEWARE.md)
- [Zustand - useShallow [velog@ony\_]](https://velog.io/@ony_/zustand-%EB%A0%8C%EB%8D%94%EB%A7%81-%EC%B5%9C%EC%A0%81%ED%99%94-shallow-%EB%8C%80%EC%8B%A0-useShallow-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

---
