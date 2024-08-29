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
		private age:number, // 멤버 변수를 자동으로 추가해준다.
		name:string
	){
		this.name = name; // 이미 선언된 변수를 수정하는 방식
		// this.age = age; → this.age 변수가 없어서 에러 발생
	}
}
```

---



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
	constructor(name:string, age:number, id:number){
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

---


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

---


## 추상 클래스 (Abstract class)

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

---

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


---

## 클래스 사용 예시

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

---


## 인터페이스 (Interface)

> **오브젝트의 형태**를 정의하는 목적으로 타입 대신에 사용한다.
> 모든 인터페이스의 기능은 타입을 사용해도 구현할 수 있다.

```typescript
type Person = {
	name:string,
	age:number
}

interface Person {
	name:string,
	age:number
}
// 위의 두 코드는 같은 기능을 한다.

interface Name = string; // 에러 interface는 오브젝트만 정의할 수 있다.
```

---

> 클래스가 인터페이스를 상속 받아 사용할 수 있다.
> 인터페이스를 상속 받은 클래스는 **private, protected** 를 사용하지 못한다.

``` typescript
interface User {
	firstName:string,
	lastName:string,
	fullName():string
}

class Player implements User {
	constructor(
		public firstName:string,
		public lastName:string
	){}
	
	fullName(){
		return `${this.firstName} ${this.lastName}`;
	}
}
```

---

### 타입과 인터페이스의 차이

> 기존에 타입에 값을 추가하기 위해서는 새로운 타입을 만들어 & 연산자로 타입을 추가해야 한다.
> 인터페이스는 기존 인터페이스에 추가하거나 상속 받아 확장하여 사용할 수 있다.

``` typescript
// 타입에 갑을 추가하는 방법
type PlayerA {
	name:string
}
type PlayerAA = PlayerA & {
	lastName:string
}
const a:PlayerAA = {
	name:"a",
	lastName:"A"
}

// 인터페이스에 값을 추가하거나 상속받는 방법
interface PlayerB {
	name:string
}
interface PlayerB { // 자동으로 인터페이스가 합쳐진다.
	firstName:string
}
interface PlayerBB extends PlayerB{
	lastName:string
}
const b:PlayerBB = {
	name:"b",
	firstName:"B",
	lastName:"B"
}
```

---

#### 타입을 여러개 사용하는 경우

> `|` 는 **or** 연산자로 여러개의 타입 중 하나를 의미한다.
> `&` 는 **and** 연산자로 여러개의 타입 모두를 의미한다.

``` typescript
type Status = "success" | "fail" | "error";
const s = "fail";
const s = "not fail" // 타입 에러

type A = {name:string};
type B = A & {age:number};
const b:B = {name:"bbb", age:111};
```

### 추상화와 인터페이스의 차이

> 추상 클래스는 컴파일 시 일반 클래스로 변환된다.
> 인터페이스는 자바스크립트 코드로 변환되지 않고 사라진다.
> 추상 클래스를 남기지 않고 클래스의 형태를 정하기만 할 때 인터페이스를 사용한다.

``` typescript
// 추상 클래스를 이용한 방법 (컴파일 시 User 클래스가 남는다.)
abstract class User {
	constructor(
		public name:string
	){}
	getName():string
}
class Player extends User {...}

// 인터페이스를 이용한 방법 (컴파일 시 Player 클래스만 남는다.)
interface User {
	public name:string,
	getName():string
}
class Player implements User {...}
```

---

## 다양성 (Polymorphism)

> 인터페이스와 클래스에서 제네릭을 적용하는 방법이다.
> LocalStorage에서 타입 T를 제네릭으로 받아서 전달한다.

``` typescript
interface SStorage<T> {
	[key:string]:T
}

class LocalStorage<T> {
	private storage:SStorage<T> = {}
	set(key:string, value:T){
		this.storage[key] = value;
	}
	remove(key:string){
		delete this.storage[key];
	}
	get(key:string):T{
		return this.storage[key];
	}
	clear(){
		this.storage = {};
	}
}

const stringsStorage = new LocalStorage<string>();
stringsStorage.set("1","first");

const booleanStorage = new LocalStorage<boolean>();
booleanStorage.set("false", false);
```

---
