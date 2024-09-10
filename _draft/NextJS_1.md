---
layout: post
title: NextJS 1
tags:
  - NextJS
  - Typescript
  - React
summary: NextJS
comments: true
---

## NextJS가 무엇이냐

> CSR의 단점을 보완하기 위해  SSR이 유행하고 있다.
> SSR 위주의 풀스택 웹사이트를 쉽게 제작할 수 있도록 만들어진 프레임워크다.

### NextJS의 장단점
- 장점
	- SSR을 사용한다.
	- 서버 기능, 인증 기능 등을 만드는 난이도가 쉽다.
- 단점
	- SSR의 단점으로 서버에 따라 속도가 느려진다.
	- WebSocket같은 기능들을 지원하지 않는다.

### CSR과 SSR의 비교

- Client-side rendering (CSR) : 클라이언트의 브라우저에서 html을 만든다.
	- 장점 : 앱처럼 부드러운 사이트를 만들 수 있다.
	- 단점 : 첫 페이지 로딩이 느리며 검색 노출이 어렵다. -> 비용이 많이 든다.
- Server-side rendering (SSR) : 서버에서 html을 만들어 클라이언트에 보낸다.
	- 장점 : 로딩이 빠르고 검색 노출이 쉽다. CSR기능도 일부 사용 가능하다.
	- 단점 : 페이지 이동 시 속도가 느리가.

## NextJS 시작하기

`npx-create-next-app@latest` : NextJS 프로젝트를 생성해준다.

> NextJS는 layout과 page로 이루어진 react 파일들로 렌더링을 한다.

`app/layout.tsx` : 하위 페이지들에 공통으로 들어가는 header 등의 레이아웃을 적용한다.
`app/page.tsx` : 프로젝트의 메인 페이지이다.
`app/globals.css` : 전체적으로 적용되는 css 파일이다.
`xxx.module.css` : 특정 페이지에만 적용되는 css 파일이다.
`app/api` : 서버 기능을 만드는 폴더이다.

---

### 라우팅

> NextJS에서는 폴더 구조를 따라 자동으로 라우팅을 해준다.

- `app` 폴더 안에 원하는 이름의 폴더를 만들고 `page.tsx` 파일을 생성한다.
- `app/list/page.tsx` : `/list`로 접속 시 `list/page.tsx` 파일이 렌더링 된다.

---

> `<a>` 대신에 NextJS의`<Link>`를 사용해서 페이지를 이동시킨다.
> `<Link>`는SPA 방식으로 html중 필요한 부분만 리렌더링 시킨다.

```typescript
import Link from "next/link";

<Link href="/">Home</Link>
```

### layout

> layout 파일이 있다면 layout 내용 안에 page 를 담아서 보여준다.
> 상위 폴더의 layout부터 하위 폴더의 layout까지 쌓아서 보여준다.
> **children**으로 page를 받아서 넣어주어야 한다.

```typescript
export default function Layout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <>
      <header>...</header>
      <div>{children}</div>
    </>
  );
}
```


### 이미지 넣기

> 프로젝트 폴더에 `public` 폴더를 만들고 이미지 파일을 추가한다.
> `<img>` 태그 대신에 NextJS의 `<Image>` 를 사용한다.
> `<Image>`는 자동으로 lazy loading, 사이즈 최적화, layout shift 방지를 해준다.


### 컴포넌트

> 컴포넌트는 **server component**와 **client component**로 나뉜다.

- server component
	- 장점 : 페이지 로드시 자바스크립트가 적어서 속도가 빠르다.
	- 단점 : html 안에 자바스크립트를 넣지 못한다. React의 useState 등을 사용하지 못한다.
- client component
	- 장점 : html 안에 자바스크립트를 넣어서 사용 가능
	- 단점 : 페이지 용량이 커지고 로딩속도가 느려진다.

--- 

> 기본적인 React 컴포넌트는 server component로 만들어진다.
> 파일 상단에 `"use client"` 코드를 넣어서 client componet로 변경한다.