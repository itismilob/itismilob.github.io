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
	- `lib` : 코드를 실행하는 환경을 지정해 자동완성 기능을 사용할 수 있다. 


```json
{
  "include": ["src"],
  "compilerOptions": {
	  "outDir": "build",
	  "target": "ES6",
	  "lib": ["ES6", "DOM"]
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

> 