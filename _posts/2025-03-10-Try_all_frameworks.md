---
layout: post
title: Try All Frameworks
tags:
  - React
  - Vue
  - Angular
  - Svelte
  - JQuery
  - Express
  - Firebase
description: 5개의 프레임워크를 사용하고 비교해보자.
image: /images/posts/tryallframeworks/logo.png
comments: true
---


# 프로젝트 정보

[프로젝트 Github](https://github.com/itismilob/Try-all-frameworks)

5개의 프론트앤드 프레임워크를 사용해보며 비교하기 위해서 시작한 프로젝트.
간단한 게시판 기능(로그인/회원가입, 글 작성)을 통해서 각 프레임워크 기능들을 활용했다.
배포를 최소화 하여 간단하게 실행할 수 있도록 Express를 통한 SSR 방식을 사용했고, Firebase Storage를 사용해 데이터를 저장했다.


## Sign in page

> Javascript를 사용해서 프레임워크를 선택할 수 있도록 만들었다.
> 사용자가 없으면 자동으로 가입되도록 했다. 간단한 프로필 이미지 업로드 기능을 만들었다.

![Sign in](/images/posts/tryallframeworks/Screenshot1.png)

## Community

> React, Vue, Angular, Svelte, JQuery (+typescript)
> 각 프레임워크 별로 동일한 게시판 기능을 만들었다.

List page

![List page](/images/posts/tryallframeworks/Screenshot2.png)

Content page, comment

![Content page](/images/posts/tryallframeworks/Screenshot3.png)

Write page

![Write page](/images/posts/tryallframeworks/Screenshot4.png)

# 프로젝트 설명

## Server

Express와 Firebase storage를 사용해 서버를 만들었다.
모든 페이지를 동일한 포트로 배포하기 위해서 SSR 방식을 사용했다.
페이지 라우팅을 각 프레임워크 별로 분리했다. (`/react/~`, `/vue/~`)

빌드 파일들의 위치를 잡기 위해서 폴더 구조를 여러번 바꿔봤지만 server 폴더에 빌드 파일을 넣는게 맞는 것 같다.

## Style, Type

CSS와 자주 사용하는 type들은 공통으로 사용했다.
type들은 package를 따로 만들었고 각 레포마다 설치해서 사용했다.

동일한 기능과 동일한 스타일로 만들었기 때문에 개발 속도가 확실히 빨라졌다.

## React

유일하게 사용해봤던 프레임워크로 만드는데 어려움이 없었다.
React를 기준으로 다른 프레임워크들을 비교하기 위해서 사용했다.

## Vue

[Vue tutorial](https://ko.vuejs.org/tutorial/#step-1)

React보다 가독성이 좋은 것 같다는 느낌을 받았다.
Tailwind 등의 class 스타일링과 사용하면 더 복잡해지겠지만 JS 코드와 HTML 템플릿의 분리가 가독성을 높였다. (SFC-Single File Component의 장점 같다.)

라우팅도 자체적인 라이브러리로 지원해서 바로 쓸 수 있었다.
데이터 관리 등의 대부분이 React와 비슷한 개념이었고 Lifecycle 부분만 좀 달랐다.

## Angular

[Angular tutorial](https://angular.dev/tutorials/learn-angular)

배울 때부터 확실히 내용이 방대하고 규칙이 엄격하다고 느껴졌다.
함수형이 아니라 클래스 형으로 만들어졌기 때문에 코딩 방식이 React와는 순서가 반대로 클래스 데코레이터에 템플릿을 선언하고 클래스 내부에서 데이터를 관리하는 점이 특이했다.

컴포넌트가 렌더링 될 때 다른 프레임워크와 다르게 해당 컴포넌트 이름의 엘리먼트로 만들어져서 CSS가 틀어지는 불편함이 있었다. (Card 컴포넌트를 만들면 브라우저에서`<card>` 태그가 만들어짐)

이번 프로젝트에서 사용하진 않았지만 DI(의존성 주입)을 사용할 수 있고, 라우팅, 빌드, HTTP 요청 등 다양한 기능을 기본적으로 지원한다는 점이 장점이라고 생각한다.

## Svelte

[Svelte tutorial](https://svelte.dev/tutorial/svelte/welcome-to-svelte)

Vue처럼 가독성이 좋았고 배우기도 어렵지 않았다.
Next.js처럼 폴더 구조 라우팅을 지원하는게 너무 편했다.
데이터 prop 전달이 `+page.server.ts`파일로 따로 만들어야 하는 점이 특이했다.

하지만 최신버전에 대한 정보가 홈페이지 말고는 찾기 어려웠고 막히면 방법을 찾기 쉽지 않았다.
특히 빌드해서 서버에서 SSR로 배포하려는 시도가 가장 복잡했다.
SvelteKit config에서 adapter를 변경하고 커스텀 서버로 연결했다. [SvelteKit - custom server](https://svelte.dev/docs/kit/adapter-node#Custom-server)
Express 서버를 cjs로 만들었다가 Svelte handler의 mjs와 충돌해서 결국 서버 전체를 mjs로 바꿔야했다.

## JQuery

[JQuery doc](https://api.jquery.com/)

JQuery 자체는 단순한 기능이지만 다른 프레임워크처럼 SPA로 만들고 싶어서 새로운 방식을 처음부터 만든다는 느낌을 받았다.
너무 자유롭다면 이미 다른 라이브러리에 있는 기능들을 다시 만들어야 해서 프레임워크의 아키텍쳐가 필요하다는 생각이 들었다.

### JQuery SPA 기능

`location.hash`를 기준으로 페이지를 라우팅한다.
페이지 outlet 함수를 Layout에 전달하고 Layout을 렌더링 할 `<div id='app'>` 아이디도 전달한다.

```ts
// routing (main.ts)
const loadPage = async () => {
	const currentPage = location.hash.split('/')[0];
	let page: Function;
	
	switch (currentPage) {
		case '#list':
			page = List;
			break;
		case '#content':
			page = Content;
			break;
		case '#write':
			page = Write;
			break;
		case '#not-auth':
			page = NotAuth;
			break;
		default:
			page = NotFound;
	}
	
	Layout(page, '#app');
};
```

---

렌더링 위치를 정하기 위해 parent 아이디 값을 전달하면 각 함수에서 해당 parent 엘리먼트에 컴포넌트를 렌더링한다.
`$(parent).html(component)`를 사용해서 동적으로 렌더링 시켰다.
Layout를 먼저 렌더링 하고, 각 페이지 outlet 함수를 실행시킨다.

```ts
// Layout.ts
export default function Layout(outlet: Function, parent: string) {
	const render = () => {
		const component = `
			...
			<main id="outlet"></main>`;
		$(parent).html(component);
	};
	const init = ()=>{
		// fetch data...
		render();
		outlet('#outlet');
	}
	init();
}
```

outlet 함수에서 데이터를 불러온 후 Layout의 `<main id="outlet">`위치에 outlet을 렌더링 시킨다.

```ts
// NotFound.ts
export default function NotFound(parent: string) {
	const render = () => {
		const component = `
			<h1>404</h1>
			<div>Not Found</div>`;
		$(parent).html(component);
	};
	const init = ()=>{
		// fetch data...
		render();
	}
	init();
}
```

---

컴포넌트를 재사용하는 방법이 복잡했다.
각 `li`별로 아이디를 주고 해당 아이디를 기준으로 `card`를 렌더링 하도록 했다.
다른 프레임워크들은 컴포넌트 자체를 반복할 수 있지만, JQuery는 컴포넌트를 넣을 부모 엘리먼트를 먼저 반복해 놓고 그 안에다가 컴포넌트를 넣어줘야 했다.
~~다른 방법이 있을지도 모르겠다.~~

# 후기

프레임워크를 비교해보며 장점과 단점을 알게되었고, 어떤 프로젝트에 어떤 프레임워크를 선택할지 고민할 수 있었다.
커뮤니티의 중요성을 알게되었고 커뮤니티가 방대한 리액트를 많이 사용하는 이유도 알게되었다.
상태 관리 등 사용하지 않은 복잡하고 많은 기능들이 더 있는데 나중에 추가해봐야겠다.
