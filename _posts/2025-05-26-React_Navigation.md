---
layout: post
title: React Navigation
tags:
  - React_Native
  - React_Navigation
  - GooApp
description: React Native 라우팅을 위한 Navigation 라이브러리
image: /images/logos/react_logo.webp
comments: true
---


## React Navigation

[React Navigation](https://reactnavigation.org/)

React Navigation은 React Native에서 화면 이동(네비게이션)을 쉽게 구현할 수 있도록 도와주는 라이브러리다.
화면 이동, Tab, Drawer, Modal 메뉴, 웹 페이지의 URL params처럼 데이터를 전달하는 기능 등을 지원한다.

## 네비게이터 종류

- Stack Navigator : 한 화면씩 쌓이는 방식
- Tab Navigator : 하단 탭 메뉴로 화면 전환
- Drawer Navigator : 옆에서 슬라이드해 나오는 메뉴 (햄버거 메뉴)
- Nested Navigator : 하나의 화면에 여러 네비게이터를 중첩해 사용 (Tab + Stack 등)
- Modal : 모달을 띄우는 네비게이터 옵션

## 패키지

- `@react-navigation/native` : 핵심 라이브러리 - 네비게이션 컨텍스트 제공 (필수)
- `@react-navigation/native-stack` : 네이티브 느낌의 스택 네비게이터
- `@react-navigation/bottom-tabs` : 탭 네비게이터
- `@react-navigation/drawer` : 드로어 네비게이터
- `@react-navigation/elements` : 네비게이션에 필요한 UI 컴포넌트를 모아놓은 패키지

## Static vs Dynamic

React Navigation에는 두가지 사용 방식 Static/Dynamic이 있다.

| 구분      | Static API                   | Dynamic API            |
| --------- | --------------------------- | ---------------------- |
| 선언 방식 | 객체 기반                   | JSX 기반               |
| 특징      | 선언적, 구조 고정           | 유연함, 동적 처리 가능 |
| 버전      | v7부터 추가                 | 기존부터 사용          |
| 목적      | 정적인 네비게이션 트리 명시 | 동적인 렌더링 유연성   |

### 장단점

| 구분 | Static API                                | Dynamic API                              |
| ---- | ---------------------------------------- | ---------------------------------------- |
| 장점 | 구조가 한눈에 보여 유지보수 쉬움         | JSX 문법을 사용해 컴포넌트처럼 조작 가능 |
|      | 타입 추론이 강력함                       | 조건부 렌더링 가능                       |
|      | 딥 링크 자동 생성                        |                                          |
|      | 정적 분석 (Typescript, Lint) 등이 유리함 |                                          |
|      |                                          |                                          |
| 단점 | 조건부 렌더링 등 동적인 사용이 어려움    | 타입 추론 제한적                         |
|      |                                          | 복잡해지면 구조 파악이 어려움            |

React Navigation에서 추천하는 기본적인 방식은 Static 방식이고 동적 구성이 필요하다면 Dynamic 방식을 사용하면 된다.

---

## Tutorial

[React Navigation - Getting started](https://reactnavigation.org/docs/getting-started)

### 설치 및 네이티브 세팅

React Navigation 설치 : `npm install @react-navigation/native`
추가 유틸리티 설치 : `npm install react-native-screens react-native-safe-area-context`

---

[네이티브 세팅](https://reactnavigation.org/docs/getting-started/#installing-dependencies-into-a-bare-react-native-project)

IOS : `npx pod-install ios`
Android : `android/app/src/main/java/<your package name>/` 수정 



### Stack Navigator

`createNativeStackNavigator` : Stack Navigator 생성 (Drawer, Tab 등 사용 가능)
`createStaticNavigation` : Navigator를 가져와 앱에 랜더링하는 컴포넌트를 반환, 앱에서 한 번만 호출

```ts  
import { createStaticNavigation } from '@react-navigation/native';  
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from './screens/HomeScreen';

// Stack Navigator 생성
const RootStack = createNativeStackNavigator({
	screens: {
		Home: HomeScreen,
	}
});

// 네비게이션 컴포넌트 생성
const Navigation = createStaticNavigation(RootStack);

export default function App(){
	return <Navigation />
}
```

#### 화면 이동

- `navigation.navigate('Details')` : 해당 스크린으로 **이동**, 이미 스택에 있으면 **기존 화면으로 이동** (중복 방지)
- `navigation.push('Details')` : 해당 스크린을 **새로 하나 더 스택에 쌓음** (중복 가능)
- `navigation.goBack()` : 뒤로가기, 히스토리 pop
- `navigation.popTo('Home')` : 히스토리에 Home 화면으로 이동
- `navigation.popToTop()` : 최상위로 이동

```tsx
// 화면 이동에 담을 props 데이터의 타입을 지정
export type RootStackParams = {
	Home: undefined; //param
	Details: undefined; //param
};

export default function HomeScreen(){
	// 네비게이션 생성
	// 네비게이션에서 전달할 props 데이터 타입을 지정, 타입을 사용하는 화면 명시
	type NavigationProp = NativeStackNavigationProp<RootStackProps, 'Home'>;
	const navigation = useNavigation<NavigationProp>();
	
	return <Pressable onPress={()=>{
		navigation.navigate('Details');
	}}>
		Go Details!
	</Pressable>
}

```



---

#### Params 데이터 전달

Params : React Navigation을 통해서 데이터를 전달한다. (웹에서의 URL Params처럼 작동)
Props : JSX 컴포넌트를 통해서 데이터를 전달한다.

React Navigation에서 화면의 Props로 Params 데이터인 `route`와 `navigation` 데이터를 넣어준다.

```tsx
// 화면 이동에 담을 props 데이터의 타입을 지정
export type RootStackParams = {
	Home: undefined;
	Details: { pageID: string };
};

// 해당 화면 컴포넌트의 props 타입 설정
type DetailsScreenProps = NativeStackScreenProps<RootStackParams, 'Details'>;

// prop 으로 param route를 받아서 데이터 사용
function DetailsScreen({ route }: DetailsScreenProps) {
	// 네비게이션 생성
	type NavigationProp = NativeStackNavigationProp<RootStackParams, 'Details'>;
	const navigation = useNavigation<NavigationProp>();

	// route 에서 데이터 꺼내서 사용
	const { pageID } = route.params;

	return <Text>{pageID}</Text>
}

// Stack Navigator에서 Props 기본값 설정
const RootStack = createNativeStackNavigator<RootStackParams>({
	screens: {
		Details: { 
			screen: DetailsScreen,
			// Props 기본값 설정
			initialParams: { pageID: '29809' } 
		},
	},
});
```

1. `navigation.navigate('RouteName', {params object})` : navigation 메소드에 param 전달
2. `navigation.popTo('BeforeScreen', {params object})` : popTo를 통해 이전 화면에 param을 전달
3. `route.params` : 전달된 param 불러옴
4. `navigation.setParams({params object})` : 현재 화면의 param 데이터 변경

중첩 화면(Nested navigator)에서 스크린을 명시해서 데이터 전달
```tsx
// More 화면으로 이동, More 화면 안의 Settings 화면에 데이터 전달
navigation.navigate('More', {
  screen: 'Settings',
  params: { user: 'jane' },
})
```

추가 규칙
- 너무 많은 데이터를 담아서는 안된다.
- Webpage의 URL과 마찬가지로 ID, sort, keywords 등의 간단한 정보만 전달한다.

---

### Nested Navigator - 중첩 화면

네비게이터 안에 네비게이터를 넣어서 사용할 수 있다. (일반 컴포넌트와 동일)
자식 네비게이터에서 Navigation Actions를 버블업 해서 부모 네비게이터의 Action을 사용할 수 있다.

- Stack Navigator
	- Home screen
	- Drawer Navigator
		- User screen
		- Setting screen

Drawer Navigator 의 화면에서는 Stack Navigator의 메소드 `push`, `replace` 등을 사용 가능하다.
반대로 Stack Navigator의 화면에서는 `openDrawer`, `closeDrawer` 등의 Drawer Navigator의 메소드를 사용 불가능하다. 

---

만약 부모 네비게이터에서 자식의 액션을 사용하려면 `dispatch`를 사용해야 한다.
dispatch : navigation state(상태)를 업데이트 한다.

`navigation.dispatch(DrawerActions.openDrawer())`

---

### Navigation Lifecycle

화면을 이동해도 기존의 화면이 Unmount 되지 않고 계속 위로 쌓인다. (화면이 추가되어도 이전 화면의 cleanup 함수가 실행되지 않음) 뒤로가기로 이전 화면에 돌아오면 남아있던 화면이 계속 보여진다.

> **Home** (Home Mount) -> **Details** (Details Mount, Home remain) -> **Home** (Details Unmount)

유저가 특정 화면에 들어오고 나가는 걸 확인하기 위해서 `focus`, `blur` 이벤트를 확인한다.

```tsx
useEffect(() => {  
	// 화면에 들어올 때 호출
	const unsubscribe = navigation.addListener('focus', () => {  
		console.log('ProfileScreen focused');  
	});
  
	return unsubscribe;  
}, [navigation]);  

useEffect(() => {  
	// 화면에서 나갈 때 호출
	const unsubscribe = navigation.addListener('blur', () => {  
		console.log('ProfileScreen blurred');  
	});  
	  
	return unsubscribe;  
}, [navigation]);
```

\+ useEffect의 의존성으로 navigation 객체를 넣어야 네비게이션 이동시 변화를 반영할 수 있다.

---

### Modal

화면을 모달로 띄우기 위해서는 네비게이터 설정에서 화면의 `presentation` 옵션으로 `modal`을 설정해준다.
만약 배경이 투명한 모달을 사용하고 싶다면 `transparentModal`로 설정하면 된다.

```ts
const RootStack = createStackNavigator({
	Modal: {
		screen: ModalScreen,
		options: {
			presentation: 'modal'
		}
	}
})
```