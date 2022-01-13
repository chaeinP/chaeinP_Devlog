---
title: Munetic_Express/TS/Sequelize 환경에서의 TDD
date: '2021-12-20'
tags: ['Munetic', 'NODEJS', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(4)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## 💡 TDD란

Test-Driven 개발 방식으로 코드를 작성하기 전 테스트 설계부터 진행하는 개발 방식입니다. 테스트 작성이라 함은 선 코드 작성 후 테스트 작성 순서를 떠올리기 쉽지만 TDD는 정확히 그 반대의 순서로 진행됩니다. TDD 방법론을 주장한 켄트백은 TDD가 결정과 피드백 사이의 갭을 줄여준다고 말합니다. 여기서 결정이란 개발자가 어떠한 기능을 이런 이런 방식으로 작성해야지 결정하는 것을 의미하고 피드백은 그 코드를 작성했을 때 프로그램이 제대로 작동하는지(success) 아니면 에러를 뱉어내는지를 의미합니다.`일단 떠오르는 대로 작성하고 에러는 나중에 처리하자!` 보다 시작부터 발생할 수 있는 에러와 예외, 코드 간의 유기적 관계들을 미리 고려하면서 에러 발생율이 낮은 코드를 작성하고 디버깅 시간을 줄여 결과적으로 애자일한 개발을 가능하게 하는 것이 TDD의 목적인 것 같습니다.

## 💡 Unit test와 Integration test

테스트의 종류도 그 목적에 따라 다양합니다. 이 프로젝트에서는 Unit Test, Integration Test를 (아주 일부) 진행하였습니다.

Unit test는 각 함수들이 독립적인 환경에서 제 기능을 수행하는지 확인합니다. 따라서 외부 다른 함수들과 상호작용하지 않는 상태로 테스트를 진행합니다. 이를 위해 필요한 파라미터 값이나 리턴값을 직접 지정해주어야합니다.

Integration test는 내부 서버를 구동해 실제로 DB와 연결하고 주어진 요청에 대해 기대한 응답이 제대로 출력되는지를 확인하는 테스트입니다. Unit test는 각 함수의 기능을 독립적으로 테스트했다면 Integration test는 함수들 간의 상호작용을 통해 발생하는 결과까지 테스트 해볼 수 있습니다.

---

## 💡 TDD 실습

## 💻 설치 및 세팅

이 프로젝트에서 사용한 테스트 모듈은 다음과 같습니다.

- jest
- supertest
- node-mocks-http

위 모듈을 아래의 명령어로 설치합니다.

```
npm i -D jest supertest @types/jest @types/supertest node-mocks-http
```

설치가 완료되면 jest.config.ts 파일을 생성해 다음과 같이 작성합니다.

```ts
import type { Config } from '@jest/types'

const config: Config.InitialOptions = {
  verbose: true, // 테스트 실행시 더 구체적인 정보를 얻을 수 있는 옵션
  testEnvironment: 'node', // 테스트 환경
  transform: {
    '^.+\\.ts?$': 'ts-jest', // ts 파일을 필터링해 ts-jest를 적용하는 옵션
  },
}
export default config
```

tsconfig.json에 test 관련 폴더와 파일은 exclude로 지정해 컴파일에서 제외합니다.

```
"exclude": ["node_modules", "jest.config.ts", "./src/tests"]
```

이제 package.json에 script 명령어를 입력합니다.

```ts
 "scripts": {
    "test": "jest",
    "test:watch": "jest --watchAll ",
  }
```

---

## 💻 test 모듈 기본 문법

### 0️⃣ 테스트 파일 생성

- 테스트 파일은 `*.test.ts` 로 작성합니다.

```ts
// 기본 틀
describe('Utils test', () => {
  test('first test', () => {
    console.log('tests work')
  })
})
```

### 1️⃣ 기본 함수

```ts
//src/app/Utils.ts
export class Utils {
  public static toUpperCase(arg: str) {
    return arg.toUpperCase()
  }
}

//src/test/Utils.test.ts
import { Utils } from '../app/Utils'

describe('Utils test', () => {
  test('first test', () => {
    const result = Utils.toUpperCase('abc')
    expect(result) toBe('ABC')
  })
})
```

- describe() : 실행할 테스트를 종류별로 구획화 하는 기능을 합니다. 첫번째 인자로 테스트를 설명하는 디스크립션을 문자열로 넣고, 두번째 인자로 실행할 테스트들을 담고있는 콜백 함수를 넣습니다.
- it(), test() : it과 test는 동일하게 동작합니다. 무엇을 사용해도 무방합니다. describe처럼 첫번째 인자로 test에 대한 description을 두번째 인자로 콜백함수를 받습니다.
- expect() : 테스트할 변수를 인자로 받습니다.
- toBe() : 테스트로 나와야할 결과값을 인자로 받습니다.
  `expect(result) toBe('ABC')`를 우리 말로 해석해보면 result의 값이 'ABC'이기를 기대한다 라는 뜻이 됩니다. result가 기대한대로 'ABC'값이 나오면 test는 통과할 것이고 그렇지 않으면 test는 실패합니다.
- toEqual() : toBe()는 비교하는 두 값의 주소가 완전히 일치하는지를 봅니다. toEqual은 두 값의 주소가 다르더라도 내부 값이 같은지를 비교합니다. 아래의 예처럼 객체와 같이 주소가 다르지만 내부 값을 비교해야할 때는 toEqual을 사용합니다.

```ts
test('객체의 내용이 같은지를 확인하려면 toEqual을 써야 한다', () => {
  const obj = { name: 'John' }
  expect(obj).toEqual({ name: 'John' }) // true
})
```

### 2️⃣ jest hooks(beforeAll, beforeEach, afterAll, afterEach)

```ts
import { Utils } from '../app/Utils'

describe('Utils test', () => {
  beforeAll(()=>{
    console.log('beforeAll') // 테스트 시작전 한번 실행된다.
  })

  beforEach(()=>{
    console.log('beforeEach') // 매 테스트 시작전마다 계속 실행된다.
  })

  test('first test', () => {
    const result = Utils.toUpperCase('abc')
    expect(result) toBe('ABC')
  })
})
// afterAll과 afterEach 또한 테스트가 실행된 후 실행된다는 점만 제외하고 beforeAll과 beforeEach 동작방식과 동일합니다.
```

### 3️⃣ error test

에러 테스트는 테스트할 함수를 다시 함수로 감싸거나 try catch로 분기를 내 테스트할 수 있습니다.

```ts
// 함수로 감싼 방법
test.only('test invlaid URL', () => {
  //test.only는 이 테스트만 테스트 하겠다는 뜻
  function expectError() {
    Utils.parserl('')
  }
  expect(expectError).toThrowError() // 위 함수가 에러를 던지는지 여부를 확인한다.
  expect(expectError).toThrow('Empty Url') //에러 메세지를 비교할 수도 있다.
})

// 화살표 함수 사용
test.only('test invlaid URL', () => {
  expect(() => {
    Utils.parseUrl('')
  }).toThrowError() // 위 함수가 에러를 던지는지 여부를 확인한다.
})

//try catch
test.only('test invlaid URL', () => {
  try {
    Utils.parseUrl('')
  } catch (error) {
    expect(error).toBeInstanceOf(Error)
    expect(error).toHaveProperty('message', 'Empty Url')
  }
})
```

---

## 💻 Unit test 작성

### 1️⃣ 함수 기능 정의

login_id값으로 User테이블을 조회해 해당하는 유저가 있는지를 판별하는 함수를 만들기로 합니다. 이 함수가 수행해야할 기능을 간단하게 정의합니다.

```
- 이 함수의 이름은 loginSearch이다.
- 이 함수는 인자로 login_id 키값을 가진 객체를 받는다.
- User.findAll 함수를 이용해 인자를 조건으로 걸어 유저가 있는지 조회한다.
- 조회된 리스트를 리턴한다.
```

### 2️⃣ 정의된 기능을 바탕으로 테스트 작성

> 이 함수의 이름은 loginSearch이다.

```ts
//tests/unit/user.service.test.ts

import { loginSearch } from '../../user.service.ts' //테스트할 함수를 불러옵니다.

describe('로그인: loginSearch unit test', () => { //테스트 디스크립션을 작성합니다.
```

> 이 함수는 인자로 login_id 키값을 가진 객체를 받는다.
> 함수가 임의로 받게될 인자를 설정합니다.

```ts
import { loginSearch } from '../../user.service.ts'

describe('로그인: loginSearch unit test', () => {
  const userInfo = {
    login_id: 'chaepark',
  };
)
```

> User.findAll 함수를 이용해 인자를 조건으로 걸어 유저가 있는지 조회한다.

먼저 User 모델 클래스를 불러옵니다. Unit test는 외부와 단절돼 독립적인 테스트를 하기 때문에 Sequelize Model을 상속받은 User 클래스처럼 db와 연결되어 작업하는 함수 및 클래스를 바로 사용할 수가 없습니다. 이에 따라 User 클래스와 User.findAll을 mocking해 임의의 함수로 만들어 이 함수가 리턴하는 값을 직접 설정해주어야합니다.

```ts
import { loginSearch } from '../../user.service.ts'
import { User } from '../../models/user.model';

jest.mock('../../models/user'); // User클래스 mocking
const userFindAll = jest.spyOn(User, 'findAll'); // findAll 함수 mocking

describe('로그인: loginSearch unit test', () => {
  const userInfo = {
    login_id: 'chaepark',
  };
  it('인자로 들어온 조건에 대해 findAll 함수로 조회한다.', async () => {
    await UserService.search(userInfo);
    expect(User.findAll).toBeCalled(); // User.findAll 함수를 사용하는지 여부를 체크
  });
)
```

> 조회된 리스트를 리턴한다.

mocking한 findAll 함수는 프로미스 객체를 리턴합니다. `mockResolvedValueOnce()`를 활용해 작업 성공 결과를 리턴값으로 findAll 함수의 결과값을 미리 설정합니다. 여기선 UserInstance라는 더미데이터를 결과값으로 설정하였습니다.

```ts
import { loginSearch } from '../../user.service.ts'
import { User } from '../../models/user.model';
import UserInstance from '../dummy/userInstance';

jest.mock('../../models/user'); // User클래스 mocking
const userFindAll = jest.spyOn(User, 'findAll'); // findAll 함수 mocking

describe('로그인: loginSearch unit test', () => {
  const userInfo = {
    login_id: 'chaepark',
  };
  it('인자로 들어온 조건에 대해 findAll 함수로 조회한다.', async () => {
    await UserService.search(userInfo);
    expect(User.findAll).toBeCalled(); // User.findAll 함수를 사용하는지 여부를 체크
  });
  it('findAll 함수로 조회된 리스트를 리턴한다.', () => {
    userFindAll.mockResolvedValueOnce([UserInstance]);
    UserService.search(userInfo).then(data =>
      expect(data).toStrictEqual([UserInstance]), // 함수의 리턴값이 기댓값과 같은지 비교
    );
  });
)
```

만약 작업 실패 결과에 따른 함수 반환 결과를 테스트하고 싶다면 `mockReturnValue(Promise.reject(errorMessage))`처럼 returnValue로 작업 실패 결과를 갖는 Promise 객체를 리턴하면 됩니다.

---

### 3️⃣ req,res,next를 인자로 받는 함수의 unit test

req,res,next를 인자로 받는 함수도 Unit test를 할 때 인자의 값을 임으로 설정해주어야 합니다. 이때 `node-mocks-http`라는 모듈을 사용해 req,res,next를 mocking해 사용할 수 있습니다.

```ts
import * as Status from 'http-status'
import * as httpMocks from 'node-mocks-http'
import ErrorResponse from '../../modules/errorResponse'
import errorHandler from '../../modules/errorHandler'

describe('module : errorHandler unit test', () => {
  let req: any, res: any, next: any
  const err = new Error('error')
  const errResponse = new ErrorResponse(Status.BAD_REQUEST, '잘못된 요청입니다.')
  beforeEach(() => {
    //테스트 실행 전 req, res, next mocking
    req = httpMocks.createRequest()
    res = httpMocks.createResponse()
    next = jest.fn()
  })

  it('err가 ErrorResponse 인스턴스가 아니면 새로운 ErrorResponse 객체를 생성해 응답한다.', async () => {
    await errorHandler(err, req, res, next)
    expect(res.statusCode).toBe(Status.INTERNAL_SERVER_ERROR) //res 상태코드 테스트
    expect(res._getJSONData()).toBe(
      // res.json 테스트
      '서버에서 오류가 발생했습니다. 잠시 후 다시 시도해주세요.'
    )
  })
  it('err가 ErrorResponse 인스턴스이면 인스턴스 정보를 사용해 응답한다.', async () => {
    await errorHandler(errResponse, req, res, next)
    expect(res.statusCode).toBe(Status.BAD_REQUEST)
    expect(res._getJSONData()).toStrictEqual(errResponse.message)
  })
})
```

## 💻 Integration test 작성

Integration test는 위에서 설명한 것 처럼 임의로 서버를 실행해 주어진 조건에서 제대로 동작하는지를 통합적으로 체크해볼 수 있는 테스트입니다. Integration test를 위해서 supertest 모듈을 사용했습니다.

### 0️⃣ app 객체 분리

supertest는 테스트시 서버를 실행시키기 때문에 app 객체가 필요합니다. 본 프로젝트는 원래 app.ts에 app설정과 포트를 실행해 연결하는 것까지 한 파일에 묶여있었습니다. 이렇게 되면 supertest에서 서버를 실행하는 코드와 `app.listen(3030)` 이 충돌해 에러를 발생시킬 수 있습니다.

```ts
// app.ts
import express, { Request, Response } from 'express'
const app: express.Application = express()

app.get('/', (req: Request, res: Response) => {
  res.send('Hello, world!')
})

app.listen(3030)
```

이에 따라 앱객체와 포트연결을 다음과 같이 분리했습니다.

```ts
// app.ts
import express, { Request, Response } from 'express'
const app: express.Application = express()

app.get('/', (req: Request, res: Response) => {
  res.send('Hello, world!')
})

export default app
```

```ts
// server.ts
import app from './app'

app.listen(3030, () =>
  console.log(`=============
          🚀 App listening on the port 3030
          =============`)
)
```

### 1️⃣ DB 연결

```ts
import app from '../../app' // 앱객체 불러오기
import request from 'supertest' // supertest 불러오기
import { sequelize } from '../../models' // 생성한 Sequelize 인스턴스 불러오기

beforeAll(async () => {
  try {
    await sequelize.sync({ force: false }) //DB 연결
  } catch (e) {
    console.log(e)
  }
})
```

### 2️⃣ 테스트 코드 작성

```ts
describe('회원가입 POST + /signup', () => {
  it('회원가입이 완료되면 회원정보가 반환된다.', async () => {
    const response = await request(app) // api, body정보를 담은 요청이 들어왔을 때 응답을 변수에 담음
      .post('/api/auth/signup') // 테스트할 api 경로
      .send({ ...userInfo }) // req.body 내용
    expect(response.statusCode).toBe(Status.CREATED) //상태코드 비교
    expect(response.body.message).toBe('request success') //응답 메세지 비교
    expect(response.body.data.login_id).toBe(userInfo.login_id)
  })
})
```

만약 api에 쿼리가 포함되어 있다면 `.send()` 대신 `.query({key:value})` 형태로 작성해 테스트 할 수 있습니다.

## 짧은 회고

처음 TDD를 개발 과정에 적용해보면서 무수히 많은 시행착오를 겪었습니다. 테스트 코드 작성 중에 발생하는 에러로 테스트 코드 자체를 디버깅하면서 테스트 코드 작성 자체에 대해 회의가 들기도 했습니다. 그러나 테스트 툴 사용에 어느정도 익숙해지면서 TDD의 장점에 공감할 수 있었습니다. 개인적으로 코드 작성 전에 수도 코드나 글로 개발 로직을 정리한 후 개발을 시작하는 편이어서 TDD를 사용하는게 매우 편리했습니다. 그럼에도 불구하고 개발 일정에 쫓겨 TDD를 많이 시도하지 못한 점은 아쉽습니다.

TDD에 대해 개발자들의 의견이 많이 갈린다고 합니다. 저는 TDD를 하면서 긍정적인 사용 경험을 느꼈지만 오히려 비효율적이고 생산성을 떨어뜨릴 수 있다고 느끼는 팀원도 있었습니다. 각자가 추구하는 방향과 방법에 따라 TDD를 적용해보면 좋을 거 같습니다.

참고 자료

- [TypeScript Express에서 세팅하는 JEST](https://velog.io/@cloudjun/Node-TypeScript-Express%EC%97%90%EC%84%9C-%EC%84%B8%ED%8C%85%ED%95%98%EB%8A%94-JEST)
- [Node.js - Jest 를 이용한 테스팅 (Testing using Jest)](https://lgphone.tistory.com/100)
- [Supertest 사용법 (2) - 사용 예제](https://velog.io/@modolee/supertest-user-guide-02)
