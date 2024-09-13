---
layout: post
title: Typescript 2
tags:
  - Typescript
description: 함수에서 타입을 사용하는 방법
image: /images/typescript_logo.webp
comments: true
---



## Functions

> 함수에서 타입을 사용하는 방법이다.
> 
> 매개변수와 반환값의 타입을 정의하고 사용할 수 있다.

---

### Call Signatures

> 함수 위에 마우스를 올렸을 때 **함수를 어떻게 호출해야 하는지** 보여준다.
> 
> 매개변수와 반환값의 타입을 추론해서 보여준다.
> 
> 함수의 타입을 만들면 해당 타입이 call signature가 된다.

```typescript
type Add = (a:number, b:number) => number;

// 함수 add의 call signature는 타입 Add가 된다.
const add:Add = (a, b) => a + b;
```




---


### 오버로딩 (Overloading)

> 오버로딩은 함수가 여러개의 call signature를 가지는 함수를 말한다.
> 
> 각각의 타입을 검사해서 분기 처리 해줘야 한다.

```typescript
type Add = {
	(a:number, b:number) : number
	(a:number, b:string) : number
}

// b: string | number
const add:Add = (a, b) => {
	if(typeof b === 'string') return a;
	return a + b;
}
```

---

> 매개변수의 개수가 다른 경우 옵셔널 타입으로 지정해준다.

```typescript
type Add = {
	(a:number, b:number) : number
	(a:number, b:number, c:number) : number
}

// c는 옵셔널 타입
const add:Add = (a, b, c?:number) => {
	if(c) return a + b + c;
	return a + b;
}
```


---


## 다양성 (Polymorphism)

> 함수가 다양한 타입을 사용할 수 있게 만드는 방법이다.
> 
> 사용하려는 일일이 타입을 선언해주어야 하고, 정의되지 않은 타입은 오류가 발생한다.

```typescript
type SuperPrint = {
	(arr:number[]) : void
	(arr:boolean[]) : void
}

const superPrint:SuperPrint = (arr) => {
	arr.forEach(i => console.log(i));
}

superPrint([1,2,3]);
superPrint([true, false, true]);
// number, boolean 이외의 배열은 오류 발생
superPrint(["a", "b", "c"]);
superPrint([1, false, "a"]);
```


---

### 제네릭 (Generics)

> 타입의 placeholder로, 들어올 값의 타입을 모를 때 타입을 추론해서 사용할 수 있게 해준다.
> 
> 다양한 타입들을 직접 선언하지 않고 사용하는 방법이다.
> 
> 제네릭에 요청해서 필요한 call signature를 생성한다.

\+ **any**를 사용하면 타입을 보호해주지 않지만 제네릭은 만들어진 call signature의 타입 보호를 받는다.

```typescript
type SuperPrint = {
	// 다양한 타입을 담는 제네릭 타입을 선언한다. (대문자로 시작)
	<TypePlaceholder>(arr:TypePlaceholder[]) : TypePlaceholder
}

const superPrint:SuperPrint = (arr) => arr[0]

// 자동으로 타입을 추론해서 제네릭 타입을 변환해준다.
const a = superPrint([1,2,3]);
const b = superPrint([true, false, true]);
const c = superPrint(["a", "b", "c"]);
// superPrint: <number | string | boolean>(arr:(number | string | boolean)[]) => number | string | boolean
const d = superPrint([1, false, "a"]);
```


---


> 제네릭의 타입을 여러개로 설정해서 사용할 수 있다.


```typescript
type SuperPrint = {
	// 각각의 변수 타입을 다르게 지정한다.
	<T, M>(a:T[], b:M) : T
}

const superPrint:SuperPrint = (a, b) => arr[0]

const a = superPrint([1, 2, 3], "a");
```

---

> 제네릭의 타입을 직접 선택해서 타입을 고정시킬 수 있다.

```typescript
type Player<E> = {
	name:string
	extra:E
}

// extra의 타입은 {favFood:string}로 고정된다.
const foo:Player<{favFood:string}> = {
	name:"foo",
	extra:{
		favFood:"burger"
	}
}

const a:Array<number> = [1,2,3,4]
const b:number[] = [1,2,3,4]
```


---






