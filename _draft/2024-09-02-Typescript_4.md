---
layout: post
title: Typescript 4
tags:
  - Typescript
summary: ttt
comments: true
---

## 타입스크립트 설정 (Typescript Configuration)

> `tsconfig.json` 파일을 만들어 Typescript의 설정을 작성한다.


- `include` : Typescript를 사용하는 디렉터리들을 지정한다.
- `compilerOptions` : Typescript를 Javascript로 컴파일 할 때 사용하는 옵션들을 지정한다.
	- `outDir` : 컴파일된 Javascript 파일을 저장하는 위치를 지정한다.
	- `target` : Javascript의 버전을 지정한다.
	- `lib` : 코드를 실행하는 환경을 지정해 자동완성 기능을 사용할 수 있다.  (정의파일)
	- `strict` : 엄격한 타입 검사 활성화 여부를 지정한다.
	- `allowJs` : Typescript에서 Javascript를 import해 사용할 수 있는지 지정한다.


```json
{
  "include": ["src"],
  "compilerOptions": {
	  "outDir": "build",
	  "target": "ES6",
	  "lib": ["ES6", "DOM"],
	  "strict": true,
	  "allowJs": true
  }
}
```


> `package.json`에서 `scripts`에 `"build" : "tsc"`로 설정한후 스크립트를 실행하면 Typescript가 Javascript로 컴파일된다.

``` json
"scripts":{
	"build":"tsc"
}
```


```
npm run build
```
---


### 정의 파일 (Declaration Files)

> Typescript로 만들었지만 Javascript로 사용되는 패키지, 프레임워크, 라이브러리 등의 타입을 알기 위해서는 정의 파일이 필요하다.
> `-.d.ts` 정의 파일을 생성해 사용된 타입들을 정의한다.

```javascript title="myPackage.js"
// myPackage.js
function add(a, b){
	return a + b;
}
```

```typescript title="myPackage.d.ts"
// myPackage.d.ts
declare module "myPackage" {
	function add(a:number, b:number): number;
}
```

### JSDoc

> Javascript에서 코드를 수정하지 않고 타입을 검사하기 위해 코멘트를 추가해 사용할 수 있다.

- `@ts-check` : Javascript 파일에서 타입을 검사를 활성화한다.
- `@param` : 함수의 매개변수의 타입을 지정한다.
- `@returns` : 반환값의 타입을 지정한다.

```javascript
// @ts-check

/**
* Add number a and b (함수 설명)
* @param {number} a (변수 a의 타입)
* @param {number} b (변수 b의 타입)
* @returns {number} (반환값의 타입)
*/
function add(a, b){
	return a + b;
}
```