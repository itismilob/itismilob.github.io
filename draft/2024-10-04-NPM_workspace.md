---
layout: post
title: NPM workspace
tags:
  - NPM
  - 문제해결
description: package.json script 문제 해결
image: /image/npm_logo.jpeg
comments: true
---

## 문제 상황

> React와 Express로 이루어진 프로젝트를 koyeb를 통해 배포하던 중 문제가 생겼다.
> koyeb에서 프로젝트를 빌드하면 npm install이 실행되는데
> root 디렉터리에 바로 위치한 **서버**의 패키지는 잘 설치가 되었다.
> 하지만 client 폴더로 한 번 들어가서 위치한 **클라이언트** 패키지는 설치가 되지 않았다.

먼저 프로젝트 폴더 구조는 다음과 같다.

- root
	- dist
	- client
		- react
		- package.json
		- node_modules
	- express
	- package.json
	- node_modules

