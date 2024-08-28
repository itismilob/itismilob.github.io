---
layout: post
title: Typescript 3
tags:
  - Typescript
summary: 객체 지향 프로그래밍 (Object-Oriented-Programing)
comments: true
---

## Class

> 여러개의 Object를 쉽게 만들기 위한 Class라는 설계도를 작성해 사용한다.

```typescript
class User{
	constructor(
		private name:string,
		private age:number
	){
		//Typescript에서는 this로 값을 연결해주지 않아도 자동으로 만들어준다.
	}
}
```

메소드 : 클래스 안에 구현된 함수

인스턴스 instence : 클래스로 만들어진 오브젝트를 인스턴스 라고 부른다.

### 접근 제한자 (Access modifier)

public : 모든 접근을 허용한다. (기본값)
protected : 자신과 자식 클래스에서만 접근이 가능하다.
private : 자신을 제외한 자식 클래스나, 인스턴스에서 접근할 수 없도록 막는다.

| **접근 가능성**      | **Public** | **Protected** | **Private** |
| ---------------- | ------ | --------- | ------- |
| 클래스 내부      | O      | O         | O       |
| 자식 클래스 내부 | O      | O         | X       |
| 클래스 인스턴스  | O      | X         | X       |


```typescript
class User{
	constructor(
		public name:string,
		protected age:number,
		private id:number
	){}
}

class UserA extends User{
	constructor(name, age, id){
		super(name, age, id);
		console.log(this.name); // 참조 가능
		console.log(this.age); // 참조 가능
		console.log(this.id); // 참조 불가능
	}
}

const a = new UserA("aaa", 20, 393920);

console.log(a.name); // 참조 가능
console.log(a.age); // 참조 불가능
console.log(a.id); // 참조 불가능

```

## Abstract class

## Interface

## Polymorphism