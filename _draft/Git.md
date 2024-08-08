---
layout: post
title: 마크다운 Markdown
tags:
  - Git
  - Github
comments: true
---


## 목차
- 깃이 무엇이냐
- 시작하기
	- `git init`
- 버전 관리
	- `git add`
- 커밋하기
	- `git commit`
- 상태 확인
	- `git status`
	- `git log`
- 작업 되돌리기
	- `git reset`
	- `git revert`
- 브랜치
	- `git branch`
	- `git checkout`
	- `git merge`
- 원격 저장소 - 깃허브
	- `git clone`
	- `git remote`
	- `git push`
	- `git pull`
	- `git fetch`

## 깃이 무엇이냐
1. 버전 관리
	- 파일 변화를 기록했다가 특정 시점의 버전을 꺼내 사용하는 시스템이다.
1. 백업, 저장
	- 수정 사항을 이전 상태로 되돌리거나 수정 내용을 비교할 수 있다.
1. 협업
	- 누가 수정을 했는지, 어디서 문제가 발생했는지 추적할 수 있다.

## 시작하기
깃을 사용하기 위해서 깃 저장소를 만들어야 한다.

`git init` : 해당 디렉터리에 깃 저장소를 생성한다.
`node_modules` 등의 깃으로 관리하지 않는 파일들을 `.gitignore` 파일에 작성해 무시하도록 한다.


## 상태 관리
수정 사항이 있는 파일에 번호를 붙여 버전으로 구별한다.

- 작업 저장소(working directory) : 파일에서 작업을 하는 장소
- 스테이지(staging area) : 버전으로 만들기 위해 수정 사항이 있는 파일들을 올려 대기시키는 장소
- 깃 저장소(git directory - repository) : 스테이지의 파일들을 버전으로 만들어 저장하는 장소.


![Working tree, staging area, and Git directory.](https://git-scm.com/book/en/v2/images/areas.png)


`git add <파일명>` : 변경 사항이 잇는 파일을 스테이지로 옮긴다.
`git reset <파일명>` : add를 취소하고 파일을 작업 저장소로 복구시킨다.


## 파일 상태 확인

파일에는 4가지의 상태가 있다.

![파일의 라이프사이클.](https://git-scm.com/book/en/v2/images/lifecycle.png)

- Untracked : 파일이 새로 생성되어 깃이 인식하지 못하는 상태
- Unmodified : 파일의 변경사항이 모두 커밋을 통해 저장된 상태
- Modified : 파일을 수정하여 변경 사항을 인식한 상태
- Staged : 파일의 변경 사항을 저장하기 위해 스테이지에 올려놓은 상태 (`git add`)

+ `git status` : Untracked, Modified, Staged 파일들을 확인할 수 있다.
	+ 변경사항이 없는 Unmodified 파일은 뜨지 않는다.



## 커밋 - Commit
커밋은 변경 사항을 깃 저장소에 기록해 저장하고 복구하는 단위가 된다.
변화하는 내용만 시간 순서대로 관리한다.
어떤 변경사항이 있는지 확인할 수 있도록 커밋할 때 간단한 메시지로 기록한다.

`git commit -m 'message'` :  스테이지에 있는 모든 파일을 깃 저장소로 커밋한다.

`git log` :  모든 커밋에 대한 정보를 확인할 수 있다. (commit hash 확인가능)
`git log --graph` : 커밋 상태를 그래프로 보여준다.
`git log --stat` : 커밋에 포함된 파일들을 보여준다.

## 작업 되돌리기

커밋을 취소하고 이전의 버전으로 돌아갈 수 있다.

- `git reset` : 돌아가려는 버전과 현재 버전 사이의 모든 커밋이 제거되고 변경사항만 남긴다.
	- (옵션이 없으면 add 취소)
	- `git reset HEAD^` : 바로 직전의 버전으로 돌아감
	- `git reset HEAD~<갯수>` : 현재 버전부터 원하는 갯수만큼의 커밋을 취소함
	- `git reset <commit hash>` : 원하는 버전으로 돌아감
	- `git reset --hard <commit hash>` : 변경사항을 남기지 않고 전부 되돌리기 위해 사용한다.
+ `git revert` : 특정 버전의 커밋과 변경사항을 지우는 revert커밋을 올린다.
	- `git revert HEAD` : 바로 직전의 버전을 제거
	- `git revert <commit hash>` : 원하는 버전을 제거

## 브랜치

branch
branch (name)
checkout (name
checkout -b (name)
switch (name)
branch -d/delete (name)
branch -D (name)

merge

## 원격 저장소 - 깃허브



remote
clone


pull
push
fetch

branch
checkout
	-t 옵션 (리모트 프랜치 가져오기)

---
## 참고

- [Visual studio 사용자를 위한 git](https://wikidocs.net/book/7060)