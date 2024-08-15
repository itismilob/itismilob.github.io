---
layout: post
title: Typescript
tags:
  - Typescript
comments: true
---
Typescript 특징, 타입 정리

## Typescript

> Typescript = Javascript + Type
> 
> Javascript에서는 배열 + 문자 등과 같이 불가능한 코드도 가능해서 런타임 에러를 발생시킨다.
> Typescript는 타입을 설정해서 타입 안정성 있는 코드를 작성할 수 있기 때문에 버그를 줄이고 생산성을 높일 수 있다.

### Typescript의 특징
- Javascript의 슈퍼셋(Superset) 언어이다.
- Strongly typed(강타입) 언어이다.
- Javascript의 문법을 그대로 사용한다.

### Typescript의 작동방식
- 코드를 작성하면서 바로바로 타입 에러를 검사해준다.
- Typescript는 Javascript로 컴파일해서 브라우저 등에서 사용된다.
- 컴파일 중에 버그를 발견한 경우 Javascript로 변환되지 않는다.



## 타입 선택하기

> 타입 추론 (Type Inference)
> 타입을 명시하지 않고 변수를 선언하는 경우 선언된 값에 따라 자동으로 타입이 결정된다.

``` typescript
let a = "hello"
a = 6 // 타입 에러
```

> 타입 명시 (Type Annotations)
> 변수명 뒤에 `: type`을 붙여서 타입을 직접 설정한다.

``` typescript
let a : string = "hello"
let b : string = false // 타입 에러
```


### 타입 정의
> 객체나 함수의 타입 등을 정의해서 사용한다.

``` typescript
const foo : {
	name: string,
	grade: number,
} = {
	name: "foo",
	grade: 2
}
```

### 타입 별칭 (Type Alias)
> 타입이 길거나 복잡해지는 경우 타입의 이름을 설정해 사용할 수 있다.
``` typescript
type 별칭 = 타입;
```

```typescript
type Student = {
	name: string,
	grade: number,
}

const foo : Student = {
	name: "foo",
	grade: 2
}
```


### 타입 종류
> Typescript에는 다양한 종류의 타입이 있으며 타입을 새로 선언하여 사용할 수 있다.

#### 원시 타입
> Javascript에서 사용되는 타입이다.
- number : 숫자 (int, float등이 없고 모든 수는 number로 표현한다.)
- string : 문자
- boolean : true / false

#### 배열
- array : 타입 뒤에 `[]`를 붙인다 (`number[]`, `string[]`, ...)


#### 옵셔널 타입 (Optional Type)

> 값이 있을 수도, 없을 수도 있는 변수를 저장하기 위해 사용한다.
> 해당 변수의 타입은 `타입 | undefined`가 된다.

```typescript
type Student = {
	name: string,
	grade: number,
	phone?: number // ? 를 붙이면 옵셔널 타입이 된다.
}

const foo : Student = {
	name: "foo",
	grade: 2
	// phone이 없어도 문제 없음
}
```

#### 함수 타입

> 함수를 정의할 때 매개변수와 반환값의 타입을 정한다.
> `function foo(매개변수:타입):반환타입{...}`

``` typescript
type Player = {
	name:string
}

function playerMaker(name:string):Player{
	return {name};
}
const playerMaker2 = (name:string):Player => ({name});

const foo = playerMaker("foo");
```

#### 읽기 전용 타입 (Readonly)
> 값을 수정하지 못하고 읽기만 가능한 타입이다.

``` typescript
type Player = {
	readonly name:string
}
const foo:Player = {
	name: "foo"
}
foo.name = "bar" // 타입 에러

const numbers:readonly number[] = [1, 2, 3]
numbers.push(4) // 타입 에러
```

#### 튜플 타입 (Tuple)
> 튜플은 배열의 서브타입으로 크기와 타입이 고정된 배열이다.

``` typescript
let player:[string, number, boolean] = ["foo", 1, true]
player = [1, "foo", true] // 에러 (순서가 맞지 않음)
player = ["foo", 1, 0] // 에러 (타입이 맞지 않음)
```

#### null, undefined
- null : 빈 값
- undefined : 초기화되지 않은 값

#### any
> Typescript의 보호에서 빠져나오기 위해 사용한다.
> 어떤 값이든 타입 검사 오류가 발생하지 않는다.

#### unknown
> 해당 값이 어떤 타입일지 모르는 경우에 사용한다.
> if 문을 사용하여 타입을 확인하고 변수를 사용해야 한다.

``` typescript
let a:unknown;

if(typeof a === 'number'){
	let b = a + 1;
} else if (typeof a === 'string){
	let b = a.toUpperCase();
}
```

#### void
> 반환값이 없는 함수에서 사용한다.
> 보통 따로 명시하지 않아도 된다.

``` typescript
function hello():void{
	console.log("hello");
}
```


#### never
> 절대 값이 할당되지 않아야 하는 타입이다.
> **오류를 발생** 시키거나 함수에서 **절대 반환하지 않아야 하는 반환 타입**으로 사용한다.

``` typescript
function hello(name:string|number){
	if(typeof name === 'string'){
		// ...
	} else if (typeof name === 'number'){
		// ...
	} else {
		// name의 타입은 never가 된다.
		console.log(name); // 작동하지 않음
	}
}

function makeError():never{
	throw new Error("message");
}
```