---
layout: post
title: Typescript 3
tags:
  - Typescript
comments: true
---

## Class

메소드 : 클래스 안에 구현된 함수

인스턴스 instence

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

class UserA extends User{}

const a = new UserA("aaa", 20, 393920);


```

## Abstract class

## Interface

## Polymorphism