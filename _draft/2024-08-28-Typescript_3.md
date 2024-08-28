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
class User {
	constructor(
		public name:string,
		protected age:number,
		private id:number
	){}
}

class UserA extends User {
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


### 정적 속성, 메소드 (Static)

> 클래스의 인스턴트를 생성하지 않고 속성, 메소드를 사용하기 위해 static 키워드를 사용한다.

```typescript
class WorldMap {
    // 정적 속성
    static mapSize:number = 50;
    
    // 정적 메소드
    static resizeMap(size:number):void{
        this.mapSize = size;
    }
}

WorldMap.resizeMap(20);
console.log(WorldMap.mapSize);
```


### 추상 클래스 (Abstract class)

> 클래스를 동일하게 만들어내기 위한 설계도가 추상 클래스이다.
> 추상 클래스를 상속받은 클래스를 만들어 사용해야 하고, 직접 새로운 인스턴스를 만들지는 못한다.

```typescript
abstract class User {
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

> 추상 클래스에 정의된 추상 메소드는 **call signiture**를 정의하고 몸체는 구현하지 않는다.
> 추상 메소드는 상속받은 클래스에서 **반드시 정의**해야 한다.

```typescript
abstract class User{
	constructor(
		public name:string,
		protected age:number,
		private id:number
	){}
	abstract getUserName():string
}

class UserA extends User{
	getUserName(){ // 추상 메소드를 정의하지 않으면 에러 발생
		return this.name;
	}
}

const a = new UserA("aaa", 20, 393920);
```


### 클래스 사용 예시

> 클래스를 이용하여 단어와 정의로 이루어진 사전을 만드는 예시다.


```typescript
// 오브젝트 타입을 만들 때 key와 value의 타입을 정할 수 있다. → dictonary
type Words = {
	// [key의 타입 ('key'가 아닌 어떤 문자가 와도 상관 없음)] : value의 타입
	[key:string]:string
}

// 단어와 정의를 저장하는 사전 클래스
class Dict {
	private words:Words = {};

	// 클래스를 타입으로 사용할 수 있다.
	addWord(word:Word){
		if(this.words[word.term] === undefined){ // 사전에 단어가 정의되지 않은 경우
			this.words[word.term] = word.def;
		}
	}
	getWord(term:string){
		return this.words[term];
	}
}

// 사전에 들어가는 단어 클래스
class Word {
	constructor(
		public term:string,
		public def:string
	){}
}

const dict = new Dict();
const Typescript = new Word("Typescript", "TypeScript is JavaScript with syntax for types.");

dict.addWord(Typescript);
console.log(dict.getWord("Typescript"));
```


## Interface

## Polymorphism