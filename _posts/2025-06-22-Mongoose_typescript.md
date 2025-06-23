---
layout: post
title: Mongoose with Typescript
tags:
  - MongoDB
  - NodeJS
  - Typescript
description: Mongoose를 Typescript로 사용해보자
image: /images/logos/typescript_logo.webp
comments: true
---

## Mongoose

> MongoDB를 Node.js에서 사용할 수 있도록 하는 모듈이다.

스키마와 모델로 데이터를 불러와 객체화 시킨 후 빠르게 수정, 데이터 접근을 가능하게 만들어준다.

## MongoDB와 연결하기

> 서버와 MongoDB(혹은 MongoDB Atlas)를 연결해야 사용 가능하다.

서버 실행 전에 한 번 연결하고 계속 사용한다.

```ts
import mongoose from 'mongoose';

async function connectDB() {
  try {
    await mongoose.connect('MongoDB_URL');
    console.log('MongoDB 연결 성공');
  } catch (err) {
    console.error('MongoDB 연결 실패:', err.message);
  }
}
```

## 스키마

> 데이터의 구조(필드)를 정의하는 객체

필드와 타입, 기본값, 제한 조건 등을 설정한다.
MongoDB에는 스키마가 없지만, Mongoose에서는 명확한 구조를 위해 사용한다.

### 스키마 필드의 데이터 타입

> 필드의 데이터로 사용할 수 있는 타입 목록.
> Javascript의 타입을 확장한 Mongoose의 자체적인 타입이다.

- String
- Number
- Boolean
- Date
- Array
- Buffer : 이진 데이터 (이미지, 파일 등)
- ObjectId : 다른 문서의 ID (ref)
- Map : Key-Value 쌍 객체
- Mixed : 타입 검사를 하지 않음, any 타입
- Decimal128 : 고정 소수점 숫자
- UUID : 고유한 값을 만들기 위한 128비트 숫자

### 스키마 제네릭 타입

> 스키마를 생성할 때 명시적으로 타입을 선언하며 사용되는 제네릭 타입들이다.
> [공식 문서 →](https://mongoosejs.com/docs/typescript/schemas.html#generic-parameters)

- `RawDocType` : DB에 저장되는 **원시 데이터 타입** (`userSchemaType`)
- `TModelType` : **모델 타입**, 보통 Model 타입 상속
  **query helpers, static methods** 함께 지정 ([[Mongoose#Query Helpers]], [[Mongoose#Statics]])
  `Model<DocType, QueryHelpers, InstanceMethods>`
- `TInstanceMethods` : 도큐먼트 인스턴스에 붙이는 **커스텀 메서드** 타입 ([[Mongoose#Methods]])
- `TQueryHelpers` : **query helper** 정의용 인터페이스 ([[Mongoose#Query Helpers]])
- `TVirtuals` : **virtual**로 정의한 필드들의 타입 ([[Mongoose#Virtuals]])
- `TStaticMethods` : 모델에 붙는 **static** 메서드의 정의 타입 ([[Mongoose#Statics]])
- `TSchemaOptions` : 스키마 옵션 타입
- `DocType` : 스키마에서 추론되는 문서 타입 (`RowDocType`, `InstanceMethods`, `Virtuals`로 자동 생성됨)
- `THydratedDocumentType` : hydrated doc 타입, `Model.findOne()`, `Model.hydrate()` 등의 기본 반환 타입

### InferSchemaType

> 스키마에 정의된 필드를 기반으로 실제 DB에 저장될 데이터 타입을 추론해준다.

```ts
const userSchema = new Schema({
  userID: String,
  nickname: String,
  topScore: Number,
});

type User = InferSchemaType<typeof userSchema>;
/* User 타입은 다음과 같이 정의된다.
User = {
	userID?: String,
	nickname?: String,
	topScore?: Number
}
*/
```

## 모델

> 실제 데이터베이스와 연결되는 객체

스키마를 바탕으로 MongoDB의 컬렉션과 연결한다.
모델이 데이터 쿼리(CRUD) 기능을 제공한다.

## 스키마, 모델 만들기

스키마 정의

- 첫 인자 : 스키마 필드 정의
- 두번째 인자 : 스키마 옵션 설정
  timestamp, \_id 자동 생성 등 - [옵션 더보기 →](https://mongoosejs.com/docs/guide.html#options)

```ts
import { Schema, Model, model } from 'mongoose';

// 필드의 타입 (따로 정의하지 않아도 자동 추론된다.)
interface User {
  userID: string;
  nickname: string;
  topScore: number;
}
// User 모텔 타입 - Model을 상속받는다.
// Static 함수가 없다면 불필요함
interface UserModel extends Model<User> {
  // Static 함수들이 정의된다.
}

const UserSchema = new Schema<User, UserModel>({
  userID: {
    required: true,
    unique: true,
    type: String,
  },
  nickname: {
    required: true,
    unique: true,
    type: String,
  },
  topScore: {
    type: Number,
    default: 0,
  },
});

// 스키마를 바탕으로 모델 생성
const userModel = model<User, UserModel>('userModel', UserSchema);
export { userModel };
```

---

모델의 인스턴스(새로운 문서)를 만들어 값을 담아 저장할 수 있다.

```ts
const newUser = new userModel();
newUser.nickname = 'testUser';
newUser.userID = 'userID';

await newUser.save();
```

## CRUD

> Mongoose를 통해 MongoDB 문서에 대한 CRUD 작업을 할 수 있다.

### Create

> 문서 생성

인스턴스를 생성해 데이터 create

```ts
const newUser = new userModel({
  userID: 'userID',
  nickname: 'testUser',
});
await newUser.save();
```

인스턴스 없이 데이터 create

```ts
await userModel.create({
  userID: 'userID',
  nickname: 'testUser',
});
```

### Read

> 문서 데이터 불러오기

전체 데이터 불러오기
(MongoDB의 비교 연산자를 사용했다. [공식 문서 →](https://www.mongodb.com/ko-kr/docs/manual/reference/operator/query-comparison/))

```ts
const users = await userModel.find();

// 조건으로 검색
const scores = await scoreModel.find({
  correct: { $gte: 5 },
  wrong: { $lte: 2 },
});
```

하나의 데이터 불러오기

```ts
const user = await userModel.findOne({ userID: 'userID' });
```

아이디로 데이터 불러오기

```ts
const user = await userModel.findById('userID');
```

### Update

> 문서 데이터 업데이트 (두 번째 인자로 변경할 값을 입력)

하나의 데이터만 업데이트

```ts
await userModel.updateOne({ userID: 'userID' }, { nickname: 'changed' });
```

아이디로 찾아서 업데이트

```ts
await userModel.findByIdAndUpdate('userID', { nickname: 'changed' });
```

### Delete

> 문서 삭제

문서 하나 검색해 삭제

```ts
await userModel.deleteOne({ userID: 'userID' });
```

문서를 아이디로 검색해 삭제

```ts
await userModel.findByIdAndDelete('userID');
```

## 쿼리 메소드

> CRUD 이외에 데이터를 검색하거나 수정하기 위해 사용되는 메소드

### Sort

> 데이터 정렬

```ts
const first = await userModel.findOne().sort({ _id: 1 }); // 생성 순서 오름차순
const last = await userModel.findOne().sort({ _id: -1 }); // 생성 순서 내림차순
```

### Where

> 쿼리에 조건을 체이닝 형식으로 추가할 수 있다.

```ts
const scores = await scoreModel.where('correct').gt(3).lt(30);
```

### Limit

> 쿼리 결과 개수 제한

```ts
const users = await userModel.find().limit(5);
```

### Skip

> 앞에서 몇 개를 건너뛰고 검색 (페이징에서 주로 사용)

```ts
const users = await userModel.find().skip(10).limit(10);
```

### Select

> 스키마의 필드를 선택해 특정 값들만 검색한다.

```ts
const userNicknames = await userModel.find().select('userID nickname'); // userID, nickname만 가져옴
const userWithoutNicknames = await userModel.find().select('-nickname'); // nickname 제외
```

### CountDocuments

> 문서 개수 세기

```ts
const count = await scoreModel.countDocuments({
  correct: { $gte: 5 },
  wrong: { $lte: 2 },
});
```

### Distinct

> 특정 필드에서 중복 제거된 값만 불러온다.

```ts
const nicknames = await userModel.distinct('nickname');
```

### Lean

> 결과 문서를 Mongoose Doc이 아닌 Plain Object로 변환해 검색 속도를 빠르게 한다.

```ts
const users = await userModel.find().lean();
```

### Transform

> 문서의 출력 결과를 가공한다.

lean 메소드를 앞에 먼저 사용해야 타입스크립트에서 타입을 추론할 수 있다.

```ts
const result = await userModel
  .find()
  .lean()
  .transform((docs) => {
    return docs.map((doc) => ({
      id: doc._id,
      name: doc.nickname.toUpperCase(),
    }));
  });
```

### Populate

> 한 문서에서 참조한 ObjectId로 다른 문서의 데이터를 찾아 불러온다.

스키마에 참조할 필드 정의 (참조할 모델을 정의할 때 사용한 명칭으로 연결한다.)

```ts
const scoreSchema = new Schema({
  user: { type: mongoose.Schema.Types.ObjectId, ref: 'userModel' },
  correct: Number,
  wrong: Number,
  accuracy: Number,
});

const scoreModel = mongoose.model('scoreModel', scoreSchema);
```

populate로 user정보도 같이 불러온다.

```ts
const score = await scoreModel.findById('userID').populate('userModel');
console.log(score.user.nickname);
```

## 에러 처리 방식

### 1. async/await + try/catch

가독성이 좋아 주로 사용한다.

```ts
try {
  const users = await userModel.find();
  // 정상 작동 처리
} catch (error) {
  // 에러 핸들링
}
```

### 2. .then().catch() 프로미스 체이닝

채이닝이 길어질 수 있어 가독성이 떨어진다.

```ts
userModel
  .find()
  .then((users) => {
    // 정상 작동 처리
  })
  .catch((error) => {
    // 에러 핸들링
  });
```

### 3. 콜백 방식

요청 인자의 가장 마지막에 에러 처리 콜백을 넣는다.
레거시 스타일로 거의 사용되지 않는다.

```ts
userModel.findById(userID, (error, user) => {
  if (error) {
    // 에러 핸들링
  }
  // 정상 작동 처리
});
```

## API

### Methods

> **문서(검색 결과)** 인스턴스에서 호출하는 **메소드**를 정의할 수 있다.

- 메소드 타입을 정의해서 스키마에 추가한다.
- 스키마에 메소드를 정의한다.

```ts
// 메소드 타입을 따로 정의
interface userMethods {
	sayHi(): string;
}

// 스키마에 메소드 타입 추가
const userSchema = new Schema<User, userModel, userMethods>({...});

// 스키마에 메소드 정의
userSchema.methods.sayHi = ()=>{
	return `Hello I'm ${this.nickname}`;
};

const userModel = mongoose.model('userModel', userSchema);

const user = await userModel.findById('userID');
// 메소드 사용
console.log(user.sayHi());
```

### Statics

> **모델**에서 호출하는 **메소드**를 정의할 수 있다.

- 모델의 타입에 메소드를 정의한다.
- 스키마에 스테틱 메소드를 추가한다.

```ts
// 모델 타입에 스테틱 메소드 정의
interface UserModel extends Model<UserSchemaType> {
	findByNickname(): UserSchemaType;
}

const userSchema = new Schema<User, userModel>({...});

// 스키마에 스테틱 메소드 정의
userSchema.statics.findByNickname = (nickname)=>{
	return this.findOne({nickname});
};

const userModel = mongoose.model('userModel', userSchema);
// 스테틱 메소드 사용
const user = await userModel.findByNickname('testUser');
```

### Query Helpers

> 모델에 커스텀 쿼리 함수를 추가해 사용한다.
> 복잡한 쿼리 로직을 캡슐화해 만들어 사용할 수 있다.

```ts
const userSchema = new Schema(
  {
    userID: String,
    nickname: String,
    topScore: Number,
  },
  {
    // query helpers 정의
    query: {
      byNickname(nickname: string) {
        return this.find({ nickname });
      },
    },
  }
);
//... 모델 정의

const testUser = await userModel.find().byNickname('testUser');
```

### Virtuals

> 가상 필드, DB에 저장하지 않지만 문서에 포함되는 계산된 필드를 추가한다.

```ts
const userSchema = new Schema(
  {
    userID: String,
    nickname: String,
    topScore: Number,
  },
  {
    // Virtual 정의
    virtuals: {
      // 아이디와 닉네임을 연결한 가상 필드 추가
      id_nickname: {
        get() {
          return this.userID + '_' + this.nickname;
        },
      },
    },
  }
);
//... 모델 정의

const user = await userModel.findOne({ userID: 'userID' });
// 아이디와 닉네임이 연결된 virtual 필드가 출력된다.
console.log(user.id_nickname);
```

---

스키마 `InferSchemaType`는 실제 데이터 구조만 나타내고 `virtual`는 포함되지 않는다.

필요하다면 hydrate를 이용해 타입을 추론해야 한다.
`type userDocument = ReturnType<(typeof userModel)['hydrate']>;`

### Subdocuments

> 문서 안에 다른 문서를 넣는 구조 (populate와 다르게 문서를 직접 넣음)

```ts
// 스키마 필드 타입 정의
interface Child {
  name: string;
}
interface Parent {
  children: Child[];
}

// 모델에 사용할 타입 정의
type ParentModel = Model<Parent>;

// 스키마 생성
const childSchema = new Schema<Child>({
	name: String;
})
const parentSchema = new Schema<Parent, ParentModel>({
  children: [childSchema]
});

// 모델 생성
const parentModel = model<Parent, ParentModel>('Parent', parentSchema);

// parent 문서 생성, child 값 생성
const parent = new parentModel();
const child = {name: 'child1'};
// child 추가
parent.children.push(child);
await parent.save();
```

### hydrate

> Plain object를 mongoose document로 바꿔주는 함수다.

```ts
const user = { userID: 'userID', nickname: 'testUser' };
const doc = userModel.hydrate(user);
```
