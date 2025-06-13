---
layout: post
title: GooApp - 디자인 넣는 작업에 발생한 문제들
tags:
  - GooApp
  - Tailwindcss
  - 디자인
description: 디자인을 넣으며 어떤 문제가 있었는가?
image: /images/logos/tailwind_logo.png
comments: true
---

> 이 글은 디자인을 적용하면서 실제로 앱에서 UI를 구현할 때 마주한 3가지 문제를 정리했다.  
> React Native + TailWind(NativeWind) 환경에서 발생한 스타일링 이슈와 실용적인 해결 방법에 대해 공유한다.

크게 3가지 문제가 있었다.

1. 동적으로 클래스 변경이 안 되는 문제
2. 폰트 크기에 따른 레이아웃 깨짐
3. 스크롤 디자인 문제

각각 문제 상황 → 시도했던 방법 → 최종 해결책 순서로 정리한다.

## 1. Tailwind의 동적 스타일링 적용

> 컴포넌트마다 배경색 등 스타일이 바뀌는 상황에서 Tailwind에 동적으로 적용한 클래스가 적용되지 않았다.

버튼의 색상 코드만 클래스에 `bg-${color}`로 넣었지만 Tailwind가 인식 하지 못해서 적용되지 않았다.
해결 방법은 `tailwind.config.js`에서 필요한 색상을 정의하거나, 변수 조각이 아닌 완성된 변수로 관리하는 방식이었다.

### ❌ 안 되는 상황

- 색상 코드만 동적으로 넣어줬다.
- Tailwind는 빌드 타임에 정적으로 탐지되는 클래스명만 인식해서 적용한다.

```tsx
export default button(){
	const green = '#00FF00';
	<View className={`bg-[${green}] flex-1 items-center justify-center`}/>
}
```

---

### ✅ 해결한 코드

- 클래스 전체를 상수로 선언하고 넣어줬다.
- 반복되는 스타일 전체를 선언해 사용이 가능하다.

```tsx
export default button(){
	const greenBg = 'bg-[#00FF00] flex-1 items-center justify-center';
	return <View className={greenBg}/>
}
```

---

### ✅ `tailwind.config.js`에 색상을 선언하는 방법

- `tailwind.config.js`에 `colors: { default-green: '#00FF00' }`처럼 선언해두면 `bg-default-green`처럼 바로 사용 가능하다.

```ts
module.exports = {
  theme: {
    extend: {
      colors: {
        'default-green': '#00FF00',
      },
    },
  },
};
```

```tsx
export default button(){
	return <View className='bg-default-green flex-1 items-center justify-center'/>
}
```

---

## 2. 폰트 문제

### 폰트 하단 짤림

> 폰트 크기 때문에 글자가 짤리는 문제가 생겼다.

![](/images/posts/gooapp/nickname.png)

`Pretendard` 폰트를 사용하면서 폰트가 커졌을 때 `g`, `y`, `j` 같은 문자 하단에서 잘렸고, 여백 설정이 필요했다.
해결을 위해 텍스트 크기에 맞춰 `leading(line-height)`을 조정했다.

---

### 글자 비율 문제

> 랭킹 리스트에서 닉네임이 차지하는 비율이 너무 넓어서 줄바꿈이 일어나는 문제가 있었다.

리스트의 배치와 글자 크기를 변경해서 해결했다.

- `랭킹`은 최대한 왼쪽으로 밀기
- `닉네임`은 왼쪽 정렬로 `랭킹`에 붙임
- `점수`는 오른쪽 정렬

## 스크롤 디자인 구현

> 기록과 랭킹 화면에서 리스트 스크롤이 사용자 정보를 덮고 위로 올라와야 했다.

![](/images/posts/gooapp/rank1.png)
![](/images/posts/gooapp/rank2.png)

### 구현

- 상단 헤더 컴포넌트에 `absolute`를 적용해서 **위치 점유를 없앴다.**
- `ScrollView`에는 `flex-1`을 적용해 **스크롤이 화면 전체를 뒤덮게** 했다.
- 스크롤 내부 `View` 에 헤더 컴포넌트의 높이 `margin-top`을 적용해 **리스트가 아래에서 시작**하도록 했다.
- 마지막으로 해당`View` 에 `min-h-screen`을 적용해서, **리스트 내용이 비어있어도 스크롤이 화면 끝까지 닿도록** 처리했다.

### 구현한 코드

```tsx
export default function Rank(){
	return (
		{/* 유저 랭킹, 최고점수 표시 */}
		<View className="absolute h-header">...</View>
		{/* 랭킹 상위 100명 표시 */}
		<ScrollView className="flex-1">
			<View className="mt-header min-h-screen">
				{/* 랭킹 리스트 컴포넌트들 */}
			</View>
		</ScrollView>
	)
}
```

## 마무리

Tailwind는 빠르고 직관적인 스타일링 도구지만, React Native에서 사용할 땐 몇 가지 제약을 이해하고 대응하는 것이 중요했다.  
이번 경험을 통해 Tailwind의 동작 방식, 폰트와 레이아웃의 관계, 스크롤 UI 구성법을 더 깊이 이해하게 되었다.
