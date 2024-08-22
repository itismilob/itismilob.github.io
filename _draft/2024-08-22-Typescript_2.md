---
layout: post
title:Typescript 2
tags:
  - Info
comments: true
---


## Call Signatures
> 함수 위에 마우스를 올렸을 때 **함수를 어떻게 호출해야 하는지** 보여준다.
> 매개변수와 반환값의 타입을 추론해서 보여준다.
> 함수의 타입을 만들면 해당 타입이 call signature가 된다.

```typescript
type Add = (a:number, b:number) => number;

// 함수 add의 call signature는 타입 Add가 된다.
const add:Add = (a, b) => a + b;
```




---


## 오버로딩 (Overloading)
> 오버로딩은 함수가 여러개의 call signature를 가지는 함수를 말한다.
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

매개변수의 개수가 다른 경우 옵셔널 타입으로 지정해준다.
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
> 다양한 타입을 사용할 수 있게 만드는 방법이다.

```typescript
type SuperPrint = {
	(arr: number[]):void
	(arr: boolean[]):void
}

const superPrint:SuperPrint = (arr) => {
	arr.forEach(i => console.log(i));
}
```




---

## 제네릭 (Generics)

---
