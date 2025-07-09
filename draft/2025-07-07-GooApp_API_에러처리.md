---
layout: post
title: GooApp API 에러처리
tags:
  - GooApp
  - Errors
description: API 요청 과정에서 발생하는 에러를 처리해보자
image: /images/logos/gooapp_logo.png
comments: true
---

## API 요청 에러

API 요청과 응답 과정에서 원하는 데이터가 전달되지 않거나 예상치 못한 에러가 발생할 경우 앱이 강제종료되지 않도록 핸들링을 해줘야 한다.
에러의 유형은 각 API의 목적에 따라 달라지는데 `유저 생성 API`에서 생성이나 저장에 실패하거나 `점수 갱신 API`에서 갱신할 유저 ID를 받지 못해 갱신에 실패하는 등 다양한 상황이 발생한다.
대표적으로 발생할 수 있는 에러들을 정리해서 문서화했고, 요청을 재시도 하거나 예상 못한 사소한 에러들은 에러 메시지만 띄우는 방식으로 처리해줬다.

## 에러 및 에러 코드 정의

각 API 기능마다 발생할 경우 기능에 문제가 생기는 에러들을 정의했다.
API 명세서에도 해당 기능에서 어떤 에러가 발생하는지를 명시해 바로 확인할 수 있도록 했다.
명세서에 없는 예외적인 에러는 시스템에 치명적인 영향을 주지 않도록 기본적인 로깅 및 알림을 통해 대응하며, 필요 시 명세에 반영한다.

| HTTP 코드 | 에러 코드             | 에러 메시지                    |
| --------- | --------------------- | ------------------------------ |
| 400       | INVALID_VALUE         | 요청의 형식이 잘못되었습니다.  |
| 404       | INVALID_API           | 잘못된 API 요청입니다.         |
| 404       | USER_NOT_FOUND        | 유저 검색에 실패했습니다.      |
| 500       | INTERNAL_SERVER_ERROR | 서버 내부 오류가 발생했습니다. |
| 500       | USER_CREATE_FAIL      | 유저 생성에 실패했습니다.      |


### 커스텀 에러 정의

기본 에러에 **HTTP 상태코드**와 **에러코드**를 넣어 사용하기 위해서 커스텀 에러를 선언했다.

```ts
export class CustomError extends Error {
  statusCode: number;
  errorCode: string;

  constructor(statusCode: number, errorCode: string, message: string) {
    super(message);
    this.statusCode = statusCode;
    this.errorCode = errorCode;
  }
}
```

선언한 커스텀 에러를 바탕으로 에러 상황마다 반복적으로 사용하는 에러를 추가로 만들어 사용했다.
```ts
export class UserCreateFail extends CustomError {
  constructor() {
    super(500, 'USER_CREATE_FAIL', '유저 생성에 실패했습니다.');
  }
}

export class UserNotFound extends CustomError {
  constructor() {
    super(404, 'USER_NOT_FOUND', '유저 검색에 실패했습니다.');
  }
}
```

## 백엔드(Express.js) 에러 처리

서버에서 요청을 처리하며 에러가 발생하면 에러 코드 등의 정보와 함게 응답을 보낸다.

Express.js에서는 미들웨어를 사용하여 요청 처리 과정에 함수를 끼워 넣어 데이터를 변환하는 등의 기능이 있다.
미들웨어에 에러 핸들러와 유효성 검사를 넣어서 요청 데이터를 가로체 확인하도록 만들었다.

에러 처리를 하기 위한 작업 과정은 다음과 같다.

1. 에러 핸들러 생성
2. 요청 유효성 검사 : express-validator
3. 정상적인 요청과 에러 상황을 테스트


### 에러 핸들러 생성

공통적인 에러 처리와 에러 로깅을 위해 `errorHandler` 함수를 만들어 미들웨어로 사용했다.
`errorHandler`는 요청 처리중에 에러가 발생하면 자동으로 실행된다.

정의되지 않은 에러라면 기본 `500 - Internal Server Error` 를 응답하고, 정의된 `CustomError`라면 그 정보를 응답한다.

```ts
// 에러 처리 미들웨어
export default function errorHandler(err: unknown, req: Request, res: Response, _next: NextFunction,
) {
  // 기본값
  let errorStatus = 500;
  let errorCode = 'INTERNAL_SERVER_ERROR';
  let message = '서버 내부 오류가 발생했습니다.';

  // 커스텀 에러 처리
  if (err instanceof CustomError) {
    errorStatus = err.statusCode;
    errorCode = err.errorCode;
    message = err.message;
  }

  // 에러 로깅 - 에러 내용과 요청 정보를 띄운다.
  console.error(err);
  console.error(`[${req.method} ${req.url} - ${message}]`);

  // 에러 정보 응답
  res.status(errorStatus).json({ message, errorCode });
}
```


app의 마지막 미들웨어로 `errorHandler`를 넣어서 모든 API에서 발생하는 에러를 공통으로 처리하게 만든다.

```ts
// app.js
import express from 'express';
const app = express();
  
app.use('/users', userRouter);
app.use(errorHandler);

app.listen(PORT, () => {
	console.log(`http://localhost:${PORT}`);
});
```

### express-validator

express-validator 라이브러리를 통한 유효성 검사로 요청속에 잘못된 데이터가 있는지 확인한다.
API 라우터에 `validator`를 넣고 `validateRequest`미들웨어를 통해서 에러가 있으면 커스텀 에러를 생성한다.

express-validator의 `param` 함수로 요청의 path-parameter속 데이터가 MongoDB 아이디 값이 맞는지 유효성 검사를 진행한다.
검사 후 `validateRequest` 함수에서 에러가 발생했는지 확인하고 에러 핸들러로 넘긴다.

```ts
router.get(
	'/:userID', // 요청 Path
	param('userID').isMongoId(), // 유효성 검사 - userID 데이터가 MongoId 인지 확인
	validateRequest, // 유효성 검사에서 에러가 있는지 확인해 에러 핸들러로 넘긴다.
	getUser // 정상적인 요청일 때 넘어가는 Controller 미들웨어
);
```

```ts
export default function validateRequest(
  req: Request,
  res: Response,
  next: NextFunction,
) {
  // 유효성 검사의 결과를 가져온다.
  const errors = validationResult(req);

  // 에러가 있는지 확인하고 커스텀 에러를 발생시킨다.
  if (!errors.isEmpty()) {
    throw new CustomError(
      400,
      'INVALID_VALUE',
      '요청의 형식이 잘못되었습니다.',
    );
  }

  // 에러가 없다면 다음 미들웨어 함수를 실행하도록 넘긴다.
  next();
}
```

## 프론트엔드(React-native) 에러처리

React Native 에서는 Axios 라이브러리를 통해 API 요청을 보내고 요청 과정에서의 에러를 처리할 수 있게 만들었다.
Axios의 Interceptor를 통해서 요청/응답을 가로체 데이터 변환, 에러 처리 등을 할 수 있다.

1. Axios Interceptor를 사용한 커스텀 에러 생성과 에러 Alert
2. 각 상황에 맞는 개별 에러 처리

### Axios 에러 처리

Axios의 Interceptor를 사용해 응답 데이터를 확인하고 에러가 발생한 경우 에러처리를 수행한다.
발생한 에러를 백엔드와 동일한 CustomError로 변환하여 같은 방식으로 처리한다.
응답 에러가 발생하면 사용자에게 알려주기 위해서 react native의 `Alert`를 사용해서 알려준다.
마지막으로 `Promise.reject`를 반환하여 axios 요청을 실행했던 코드에서 개별 처리를 하도록 넘겨준다.

```ts
// 응답 에러를 처리하는 헨들러 정의
function resErrorHandler(error: unknown) {
  // 기본 에러 데이터
  let status = 500;
  let code = 'UNKNOWN';
  let message = '알 수 없는 에러입니다.';

  // axios 에러일 때
  if (axios.isAxiosError(error)) {
    // 에러 상황에 맞는 데이터 입력
    if (!error.response) {
      status = 503;
      code = 'NETWORK_ERROR';
      message = '네트워크 오류가 발생했습니다.';
    } else if (error.code === 'ECONNABORTED') {
      status = 408;
      code = 'TIMEOUT';
      message = '요청 시간이 초과되었습니다.';
    } else {
      status = error.response.status;
      code = error.response.data.errorCode;
      message = error.response.data.message;
    }
  }

  // 커스텀 에러로 만들어 모든 에러를 같은 방식으로 처리할 수 있도록 제작
  const customError = new CustomError(status, code, message);

  // react native - Alert 알림
  showErrorAlert(customError);
  // Promise 에러를 반환하여 axios 요청을 실행한 코드에서 처리하도록 넘김
  return Promise.reject(customError);
}

axios.interceptors.response.use(res => res, resErrorHandler);
```

### 개별 에러 처리

API 요청을 실행하던 함수에서 요청 에러가 발생하면 실행 과정을 되돌리거나 페이지를 이동시키는 등의 개별 에러 처리 기능들을 선언해서 처리할 수 있다.

```ts
try{
	const newUser = await axios.post(/.../);
	if(newUser){
		saveUserData(newUser);
	}else{
		throw new Error('유저 생성에 실패했습니다.')
	}
} catch(error) {
	// 요청 에러가 발생했거나 유저가 생성되지 않으면 화면을 이동시킨다.
	navigation.replace('UserNotCreated');
}
```

## 마무리

에러 처리 부분에서는 정형화된 작업 방식을 찾기 어려웠기 때문에 가장 고민도 많았고 시간도 많이 들어간 작업이었다. 이번 작업을 통해서 에러가 발생하는 다양한 상황을 생각하고 에러 처리를 하는 흐름을 배웠다.

백엔드에서는 요청을 검증하고(validation) 처리하며, 에러 발생 시 에러 핸들러를 통해 에러 정보를 담은 응답을 반환하고 로그를 남긴다.
프론트엔드에서는 요청과 응답 전반에 대해 에러를 감지하고 처리하며, 사용자에게 알림을 통해 문제를 전달하고, 개발자가 이를 확인할 수 있도록 돕는다.

express-validation과 axios interceptor 같은 기능들은 단순하지만 사용 방법이 자유로워서 더 어렵게 느껴졌다. API를 사용하는 백앤드, 프론트의 에러를 전체적으로 통일하기 위해서 커스텀 에러를 사용해보니 가독성이 향상되고 디버깅을 하며 알아보기 편해졌다.
다음번에는 Jest 같은 테스트 라이브러리를 통해 발생하는 에러 상황들을 전부 테스트해보는 것도 좋을 것 같다.