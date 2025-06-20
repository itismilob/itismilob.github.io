---
layout: post
title: React Native CLI vs Expo
tags:
  - React_Native
  - Expo
  - GooApp
description: React Native CLI와 Expo를 비교하면서 어떤걸 사용할지 정해보자!
image: /images/logos/react_logo.webp
comments: true
---

React Native CLI와 Expo를 비교하면서 어떤걸 사용할지 정해보자!
이전에 GooApp을 React Native의 테스트 프로젝트로 진행하면서 Expo를 사용했다.
Expo를 사용하면서 느꼈던 장단점을 CLI와 비교하면서 사용할 프레임워크를 고민했다.

---

## 장단점 정리

|항목|React Native CLI|Expo|
|---|---|---|
|**설정 및 시작 속도**|초기 설정 복잡|✅ 매우 빠름|
|**로컬 빌드 지원**|✅ 완벽하게 지원|기본 미지원 (EAS CLI)|
|**Native 코드 수정**|✅ 자유롭게 가능|기본 불가능 (eject 필요)|
|**서드파티 라이브러리 호환성**|✅ 모든 라이브러리 사용 가능|제한적|
|**앱 크기 최적화**|✅ 필요 기능만 설치 가능|기본 SDK 포함 기능이 많음|
|**OTA 업데이트**|직접 구현 필요 (CodePush 등)|✅ 기본 지원 (Expo Updates)|
|**앱 테스트 용이성**|직접 빌드하거나 에뮬레이터 필요|✅ Expo Go 앱으로 즉시 테스트 가능|
|**CI/CD 연동**|✅ 자유롭게 가능|유료 플랜|
|**백그라운드 기능 (Task 등)**|✅ 완전 지원|제한된 백그라운드 기능|
|**푸시 알림 커스터마이징**|✅ 자유롭게 구성 가능|Expo Push (커스터마이징 제한)|
|**기존 Native 앱 통합**|✅ 가능 (기존 앱에 RN 추가 가능)|불가능|

---

## 프로젝트에 적용될 장단점

### React Native CLI

**장점**
- 내 컴퓨터를 사용해 로컬 빌드가 가능하다.
- 필요한 것들만 설치해서 빌드해 앱 용량 최적화에 유리하다.
- 일부 Expo SDK 라이브러리를 사용 가능하다.

**단점**
- 초기 설정이 복잡하고 추가 툴(Xcode, Android Studio)가 필요하다.
- 테스트를 위해 빌드와 설치 과정이 필요하다.

### Expo

**장점**

- Expo Go 앱을 통해 편리한 테스트가 가능하다.
- Expo SDK에서 다양한 모듈, 라이브러리를 지원한다.
- Expo Updates를 통한 OTA를 기본 지원한다.

**단점**
- EAS를 통한 클라우드 서버를 통해서만 빌드가 가능하다. (대기열)
- ReactNative의 세부 구조를 알기 어렵다.

---

## CLI를 선택한 이유

### React Native를 제대로 이해하고 구조를 파악하기 위해서 CLI를 선택했다.

1. 초기 설정을 직접 할 수 있다.
    - Expo에서는 설정이 되어있어서 알기 어렵지만, CLI는 필요한 설정을 직접 구성하면서 구조를 익힐 수 있다.
2. React Native의 기본 API들을 사용한다.
    - Expo SDK는 API를 수정한 버전을 사용하기 때문에 원래의 RN 기능을 다루기 위해서는 CLI를 사용해야 한다.
3. 확장성에 제한이 없다.
    - Expo는 일부 네이티브 모듈에 사용 제약이 있지만 CLI는 자유롭게 연동 가능하다.
4. 로컬에서 직접 빌드가 가능하다.
    - Expo는 빌드 서버에서 대기열이 발생하지만 CLI는 직접 컴퓨터에서 바로 빠르게 빌드할 수 있다.
5. 앱 용량 최적화
    - Expo는 여려 기능을 기본적으로 포함해 앱 용량이 커질 수 있다. CLI는 필요한 모듈만 설치해 가볍고 단순한 앱을 만들 수 있다.

---

Expo는 빠른 개발과 테스트에 유리하지만, React Native의 구조와 작동 방식을 깊이 있게 이해하기에는 한계가 있었다. CLI 환경에서는 직접 구조를 설계하고 다양한 네이티브 기능을 다뤄볼 수 있어, React Native를 더 깊이 배우기에 적합하다고 느꼈다.Expo Go를 사용하지 못하는게 최대 단점… 😅