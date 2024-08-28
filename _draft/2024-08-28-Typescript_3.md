---
layout: post
title: Typescript 3
tags:
  - Typescript
summary: 객체 지향 프로그래밍 (Object-Oriented-Programing)
comments: true
---

## 클래스 (Class)

> Typescript의 클래스에는 타입과 접근 제한자 등이 추가되었다.
> **메소드(Method)** : 클래스 안에 구현된 함수를 말한다.
> **인스턴스(Instance)** : 클래스로 만들어진 오브젝트를 인스턴스 라고 부른다.

```javascript
class User{
	constructor(name, age){
		this.name = name;
		this.age = age;
	}
}
```


```typescript
class User{
	private name:string; // this로 변수를 참조하기 위해서는 먼저 선언해주어야 한다.
	constructor(
		name:string,
		private age:number // 멤버 변수를 자동으로 추가해준다.
	){
		this.name = name; // 이미 선언된 변수를 수정하는 방식
		// this.age = age; → this.age 변수가 없어서 에러 발생
	}
}
```



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

### 추상 클래스 (Abstract class)

> 클래스를 동일하게 만들어내기 위한 설계도가 추상 클래스이다.
> 추상 클래스를 상속받은 클래스를 만들어 사용해야 하고, 직접 새로운 인스턴스를 만들지는 못한다.

```typescript
absctact class User{
	constructor(
		public name:string,
		protected age:number,
		private id:number
	){}
}

class UserA extends User{}

const a = new UserA("aaa", 20, 393920);
const b = new User("bbb", 20, 148993); // 직접 인스턴스 생성 불가
```

> 추상 클래스에 정의된추상 메소드는 **call signiture**를 정의하고 몸체는 구현하지 않는다.
> 추상 메소드는 상속받은 클래스에서 **반드시 정의**해야 한다.

```typescript
absctact class User{
	constructor(
		public name:string,
		protected age:number,
		private id:number
	){}
	getUserName():string
}

class UserA extends User{
	getUserName(){ // 추상 메소드를 정의하지 않으면 에러 발생
		return this.name;
	}
}

const a = new UserA("aaa", 20, 393920);
```

## Interface

## Polymorphism