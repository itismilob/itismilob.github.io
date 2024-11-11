---
layout: post
title: NPM workspace
tags:
  - NPM
  - 문제해결
description: package.json script 문제 해결
image: /images/logos/npm_logo.jpeg
comments: true
---

## 문제 상황

React와 Express로 이루어진 프로젝트를 koyeb를 통해 배포하던 중 문제가 생겼다.
koyeb에서 프로젝트를 빌드하면 npm install이 실행되는데
root 디렉터리에 바로 위치한 **서버**의 패키지는 잘 설치가 되었다.
하지만 client 폴더로 한 번 들어가서 위치한 **클라이언트** 패키지는 설치가 되지 않았다.

먼저 프로젝트 폴더 구조는 다음과 같다.

- root
  - git
  - client
    - react
    - package.json
  - express
  - package.json
  - dist

---

## 해결 시도

처음에는 단순하게 root의 package.json에다 client의 npm install 명령어를 실행하려고 했다.

```json
// root/package.json

"script":{
	"client-install" : "npm install --prefix client"
}
```

~~당연하게도 작동하지 않았다.~~

---

방법을 찾다가 workspace 옵션을 알게되었다.
하나의 npm에서 하위 폴더에 있는 npm까지 관리할 수 있는 기능이었다.

---

## Workspace

> workspace는 최상위 패키지에서 하위 패키지를 관리하고 서로 참조하도록 만든다.
> 하위 패키지들의 의존성을 관리하고 설치하는 기능까지 있다.

### 사용 방법

- 최상위 package.json에 workspaces 옵션을 추가해주면 사용할 수 있다.
- 하위 패키지 스크립트를 실행하기 위해서는 `-w [하위 패키지]`를 추가한다.

```json
// root/package.json

// 하위 패키지의 폴더명을 적어준다.
"workspaces":[
	"client",
	...
]

// -w 옵션을 통해 하위 폴더의 스크립트를 실행한다.
"script":{
	"client-build" : "npm run build -w client",
	"client-dev" : "npm run dev -w client"
}
```

- `-S` 키워드를 활용해 하위 패키지에 모듈을 추가할 수도 있다.

```bash
npm i -S [패키지] -w [하위 패키지]
```

---

## 문제 해결

root 폴더에서 서버와 클라이언트 패키지를 관리할 수 있도록 폴더 구조를 바꿨다.

- root
  - git
  - package.json
  - client
    - react
    - package.json
  - server
    - express
    - package.json
  - dist

root 패키지에서 클라이언트 빌드와 서버 실행을 모두 하도록 스크립트를 추가했다.
nodemon도 root에 추가해서 빌드 -> 서버 실행 과정을 자동화 했다.

```json
  "workspaces": [
    "server",
    "client"
  ],
  "scripts": {
    "build": "npm run build -w client",
    "start": "npm run start -w server",
    "dev": "nodemon --exec \"npm run build -w client && npm run dev -w server\""
  },
```

---
