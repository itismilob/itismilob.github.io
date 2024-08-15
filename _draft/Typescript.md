---
layout: post
title: Typescript
tags:
  - Typescript
comments: true
---

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


### 타입 종류
> Typescript에는 다양한 종류의 타입이 있으며 타입을 새로 선언하여 사용할 수 있다.


#### 원시 타입
- number
- string
- boolean
- array : 타입 뒤에 `[]`를 붙인다 (`number[]`, `string[]`, ...)
- undefined

#### 타입 별칭 (Type Alias)
> 타입의 이름을 설정한다.

#### 옵셔널 타입 (Optional Type)

> 값이 있을 수도, 없을 수도 있는 변수를 저장하기 위해 사용한다.
> 해당 변수의 타입은 `type | undefined`가 된다.

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