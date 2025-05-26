---
layout: post
title: React Native MMKV
tags:
  - React_Native
description: React Native에서 로컬 데이터 저장하기
image: /images/logos/react_logo.webp
comments: true
---
## MMKV
[react-native-mmkv ->](https://github.com/mrousavy/react-native-mmkv)

ReactNative를 위한 로컬 저장소
WeChat앱의 저장소로 쓰이는 [Tencent/MMKV](https://github.com/Tencent/MMKV)를 기반으로 만들어졌다.

- C++을 기반으로 만들어져 속도가 매우 빠르다.
- 동기적으로 작동해서 프로미스를 사용하지 않는다!
- 암호화 (secure storage)를 지원한다.
- 저장소 위치를 지정할 수 있다.

---

## 시작하기

`npm install react-native-mmkv`

재사용 가능한 MMKV의 인스턴스를 생성해서 사용한다.
기본 MMKV 스토리지 아이디로 인스턴스가 생성된다. storage ID : `mmkv.default`

```ts
import {MMKV} from 'react-native-mmkv';

export const storage = new MMKV();
```

### 커스텀 스토리지

커스텀 스토리지 아이디를 통해 커스텀 스토리지를 생성한다.

```ts
import {MMKV, Mode} from 'react-native-mmkv';

export const storage = new MMKV({  
  id: `user-${userId}-storage`,
  path: `${USER_DIRECTORY}/storage`,
  encryptionKey: 'hunter2',
  mode: Mode.MULTI_PROCESS,
  readOnly: false
});
```

- id : 스토리지 인스턴스의 ID
- path : 스토리지의 루트 위치. 기본으로는 `$(Documents)/mmkv/~` 위치로 저장된다.
- encryptionKey : 암호/복호화 키. 기본으로 일반 텍스트로 저장, 암호화를 하지 않는다.
- mode : MMKV 프로세스의 동작. `MULTI_PROCESS`로 설정하면 외부 앱에서 데이터를 수정할 수 있다고 본다.
- readOnly : 읽기 전용 모드

앱을 다시시작할 때 항상 같은 키로 저장소를 초기화 해야 데이터를 읽을 수 있다.
암호화 키는 Android Keystore, IOS Keychain 등을 사용

---

## 사용하기

저장할 수 있는 데이터 타입은 **String, Number, Boolean** 3가지가 있다.

### Set

데이터 저장하기 (저장할 때는 타입 명시 X)

```ts
storage.set('user.name', 'Marc')
storage.set('user.age', 21)
storage.set('is-mmkv-fast-asf', true)
```

### Get

데이터 불러오기 (특정 타입을 명시해 불러와야 한다.)

```ts
const username = storage.getString('user.name') // 'Marc'
const age = storage.getNumber('user.age') // 21
const isMmkvFastAsf = storage.getBoolean('is-mmkv-fast-asf') // true
```

### Hooks

React Hook과 연결된 방식으로 사용한다.
`[불러올 State 변수, 저장하는 함수]`

```ts
const [username, setUsername] = useMMKVString('user.name')
const [age, setAge] = useMMKVNumber('user.age')
const [isMmkvFastAsf, setIsMmkvFastAf] = useMMKVBoolean('is-mmkv-fast-asf')
```

### Keys

키를 통해서 값을 확인하고 삭제할 수 있다.

```ts
// 특정 키가 있는지 확인
const hasUsername = storage.contains('user.name')

// 모든 키 가져오기
const keys = storage.getAllKeys() // ['user.name', 'user.age', 'is-mmkv-fast-asf']

// 특정 키와 연결된 값 삭제
storage.delete('user.name')

// 모든 키/데이터 삭제
storage.clearAll()
```

### Objects

Object형 데이터를 저장하기 위해 JSON.stringify을 사용해 String으로 변환해서 저장한다.
불러올 때도 JSON.parse로 변환해 사용한다.

```ts
const user = {
  username: 'Marc',
  age: 21
}

// JSON string으로 변환
storage.set('user', JSON.stringify(user))

// string 값을 불러오며 다시 오브젝트로 변환
const jsonUser = storage.getString('user') // { 'username': 'Marc', 'age': 21 }
const userObject = JSON.parse(jsonUser)
```

### Encryption

이미 존제하는 스토리지의 암호 재설정 및 암호 제거
보안 정책 변경, 키 교체 등에 사용함

```ts
// 모든 값을 프라이빗 키로 암호화
storage.recrypt('hunter2')

// 암호 삭제
storage.recrypt(undefined)
```

### Buffers

성능과 압축이 필요할 때 Buffer를 사용하지만 주로 JSON 문자열을 사용해 저장한다.

ArrayBuffer : 원시 바이너리 데이터 버퍼를 표현한다.
`ArrayBuffer(버퍼 크기)`

Uint8Array : TypedArray로 8비트의 부호 없는 정수 배열이다.
길이, 배열, TypedArray, ArrayBuffer 등으로 만들 수 있다.

TypedArray : 타입이 있는 배열, 모든 원소는 동일한 타입이고 만들면 길이를 수정할 수 없다.
배열의 읽기/쓰기를 위해서 사용한다.

```ts
// 3 크기의 ArrayBuffer 생성
const buffer = new ArrayBuffer(3)
// ArrayBuffer로 Uint8Array 생성
const dataWriter = new Uint8Array(buffer)
// dataWriter를 사용하여 buffer 값 작성
dataWriter[0] = 1
dataWriter[1] = 100
dataWriter[2] = 255
// 스토리지에 데이터 저장
storage.set('someToken', buffer)

//---------
const buffer = storage.getBuffer('someToken')
console.log(buffer) // [1, 100, 255]
```

---

### Size

스토리지 크기 확인

```ts
// 스토리지 크기를 byte로 가져옴
const size = storage.size
if (size >= 4096) {
  // 사용하지 않는 키와 캐시를 삭제함
  storage.trim()
}
```

## Zustand와 연동해서 사용

[Zustand persist-middleware ->](https://github.com/mrousavy/react-native-mmkv/blob/main/docs/WRAPPER_ZUSTAND_PERSIST_MIDDLEWARE.md)
Zustand와 연동해서 하나의 storage로 편하게 관리할 수 있다.

```ts
import { StateStorage } from 'zustand/middleware'
import { MMKV } from 'react-native-mmkv'

const storage = new MMKV()

const zustandStorage: StateStorage = {
  setItem: (name, value) => {
    return storage.set(name, value)
  },
  getItem: (name) => {
    const value = storage.getString(name)
    return value ?? null
  },
  removeItem: (name) => {
    return storage.delete(name)
  },
}
```