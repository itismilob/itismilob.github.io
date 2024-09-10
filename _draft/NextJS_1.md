---
layout: post
title: NextJS 1
tags:
  - NextJS
summary: NextJS
comments: true
---

## NextJS를 사용하는 이유

> Client-side rendering (CSR)의 단점을 보완하기 위해 Server-side rendering이 유행하고 있다.

- CSR : 클라이언트의 브라우저에서 html을 만든다.
	- 장점 : 앱처럼 부드러운 사이트를 만들 수 있다.
	- 단점 : 첫 페이지 로딩이 느리며 검색 노출이 어렵다. -> 비용이 많이 든다.
- SSR : 서버에서 html을 만들어 클라이언트에 보낸다.
	- 장점 : 로딩이 빠르고 검색 노출이 쉽다. CSR기능도 일부 사용 가능하다.
	- 단점 : 페이지 이동 시 속도가 느리가.

## NextJS가 무엇이냐

> SSR 위주의 풀스택 웹사이트를 쉽게 제작할 수 있도록 만들어진 프레임워크다.

- 장점
	- SSR을 사용한다.
	- 서버 기능, 인증 기능 등을 만드는 난이도가 쉽다.
- 단점
	- SSR의 단점으로 서버에 따라 속도가 느려진다.
	- WebSocket같은 기능들을 지원하지 않는다.


## NextJS 시작하기

`npx-create-next-app@latest` : NextJS 프로젝트를 생성해준다.

> NextJS는 layout과 page로 이루어진 react 파일들로 렌더링을 한다.

`app/layout.tsx` : 하위 페이지들에 공통으로 들어가는 header 등의 레이아웃을 적용한다.
`app/page.tsx` : 프로젝트의 메인 페이지이다.
`app/globals.css` : 전체적으로 적용되는 css 파일이다.
`xxx.module.css` : 특정 페이지에만 적용되는 css 파일이다.