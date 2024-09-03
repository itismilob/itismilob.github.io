---
layout: post
title: Typescript 4
tags:
  - Typescript
summary: Typescript 설정과 타입 정의 파일
comments: true
---

## 타입스크립트 설정 (Typescript Configuration)

> Typescript를 사용하기 위해 설치해준다.
> 
> `npm install -D typescript`
> 
> `tsconfig.json` 파일을 만들어 Typescript의 설정을 작성한다.

- `include` : Typescript를 사용하는 디렉터리들을 지정한다.
- `compilerOptions` : Typescript를 Javascript로 컴파일 할 때 사용하는 옵션들을 지정한다.
	- `outDir` : 컴파일된 Javascript 파일을 저장하는 위치를 지정한다.
	- `target` : Javascript의 버전을 지정한다.
	- `lib` : 코드를 실행하는 환경을 지정해 자동완성 기능을 사용할 수 있다.  (정의파일)
	- `strict` : 엄격한 타입 검사 활성화 여부를 지정한다.
	- `allowJs` : Typescript에서 Javascript를 import해 사용할 수 있는지 지정한다.


```json title="tsconfig.json"
// tsconfig.json
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

---

> `package.json`에서 `scripts`에 `"build" : "tsc"`로 설정한후 스크립트를 실행하면 Typescript가 Javascript로 컴파일된다.

``` json title="package.json"
// package.json
"scripts":{
	"build":"tsc"
}
```


``` bash
npm run build
```

---

> `ts-node` 라이브러리를 사용하여 자동으로 컴파일 후 파일을 실행하도록 할 수 있다.
> 
> `npm install ts-node`

```json
// package.json
"scripts":{
	"dev" : "ts-node src/index.ts"
}
```

``` bash
npm run dev
```

---

> `nodemon` 라이브러리를 사용해 파일 저장 시 자동으로 컴파일 후 실행하도록 할 수 있다.
>
> `npm install nodemon`

```json
// package.json
"scripts":{
	"dev" : "nodemon --exec ts-node src/index.ts"
}
```

``` bash
npm run dev
```

---

### 정의 파일 (Declaration Files)

> Typescript로 만들었지만 Javascript로 사용되는 패키지, 프레임워크, 라이브러리 등의 타입을 알기 위해서는 정의 파일이 필요하다.
> 
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

---

### JSDoc

> Javascript에서 코드를 수정하지 않고 타입을 검사하기 위해 코멘트를 추가해 사용할 수 있다.

- `@ts-check` : Javascript 파일에서 타입을 검사를 활성화한다.
- `@param` : 함수의 매개변수의 타입을 지정한다.
- `@returns` : 반환값의 타입을 지정한다.

```javascript title="javascript JSDoc"
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

---

## 타입스크립트를 이용한 블록체인 만들기

> 블록체인은 (이전 블록의 hash, 길이, 저장할 데이터) 3가지를 묶어 새로운 hash를 만들어 저장한다.
>
> node.js의 패키지인 `crypto` 를 사용하여 hash 값을 만들어 사용한다.
>


### crypto

> crypto는 node.js의 패키지로 다양한 암호화 기능을 제공한다.

CommonJS와 ES6 모듈 사이에 import를 하는 경우 에러가 발생한다.
에러를 해결하기 위해서는 `package.json`에 `esModuleInterop`, `module` 옵션을 설정해준다.

```json
// package.json
{
  "compilerOptions": {
	...    
	"esModuleInterop": true,
    "module": "CommonJS"
  }
}
```

---

### Definitely Typed

> [Definitely Typed - Githuub](https://github.com/DefinitelyTyped/DefinitelyTyped)
> 
> npm에 올라온 다양한 패키지의 타입 정의 파일을 사용할 수 있다.


> 단순히 import만 하면 Typescript가 crypto의 타입을 인식하지 못한다.
> 
> `npm install -D @types/node` : 노드의 패키지에 대한 **타입 정의 파일**을 불러온다.

node 뿐만 아니라 

---

### 블록체인 코드

``` typescript
import crypto from "crypto";

interface BlockShape {
  hash: string;
  prevHash: string;
  height: number;
  data: string;
}

class Block implements BlockShape {
  public hash: string;
  
  constructor(
    public prevHash: string,
    public height: number,
    public data: string
  ) {
    this.hash = Block.calculateHash(prevHash, height, data);
  }
  
  static calculateHash(prevHash: string, height: number, data: string): string {
    const toHash = `${prevHash}${height}${data}`;

	// crypto를 사용하여 Hash값을 생성한다.
    return crypto.createHash("sha256").update(toHash).digest("base64");
  }
}

class Blockchain {
  private blocks: Block[];
  constructor() {
    this.blocks = [];
  }
  
  private getPrevHash() {
    if (this.blocks.length === 0) return "";
    return this.blocks[this.blocks.length - 1].hash;
  }
  
  public addBlock(data: string) {
    const block = new Block(this.getPrevHash(), this.blocks.length + 1, data);
    this.blocks.push(block);
  }

  public getBlocks() {
    return [...this.blocks];
  }
}

  

const blockchain = new Blockchain();

blockchain.addBlock("first one");
blockchain.addBlock("second one");
blockchain.addBlock("third one");

console.log(blockchain.getBlocks());
```

