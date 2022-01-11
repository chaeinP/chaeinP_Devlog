---
title: Node.js_tsoa + swagger API 문서 자동화
date: '2021-12-16'
tags: ['Munetic', 'NODEJS', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(1) Nodejs+Typescript환경에 tsoa+swagger를 사용해 자동으로 API 명세 만들기
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)
> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간 동안 정리한 학습 기록 및 회고 글입니다.

현재 기업협업 프로젝트 진행 중에 있다. 이 프로젝트는 총 3팀이 순차적으로 프로젝트를 진행한다. 나는 가장 첫번째 팀에 속해 기획부터 개발환경 세팅, 기본적인 db구축과 UI컴포넌트 작업을 수행하고있다. 이 프로젝트에서 가장 중요한 이슈는 인수인계이다. 우리의 코드를 기반으로 그 다음팀이 개발을 진행해야 하기 때문에 개발 뿐만 아니라 문서화 또한 가볍게 여길 문제가 아니었다.

특히 API 같은 경우엔 서비스가 배포되기 이전까지 무수히 많은 변동을 거친다. 사실 배포된 이후에도 API는 종종 수정된다. 이때마다 수정된 API를 제대로 공유하지 않으면 큰 문제를 발생시킬 수 있다. 따라서 API 명세를 주기적으로 업데이트 해주어야하는데 개발 일정에 쫓기다보면 놓치기 쉽다. 이를 자동화할 수 있는 방법을 고민하다 tsoa+swagger를 알게 되었다.

## tsoa란

> It is a framework with integrated OpenAPI compiler to build Node.js serve-side applications using TypeScript.

tsoa는 타입스크립트를 사용하는 Node.js 서버 코드를 OpenAPI로 컴파일 해주는 프레임워크이다. 쉽게 말하면 개발 코드가 변경될 때마다 자동으로 API 문서화 + 라우팅 변경을 수행해주는 도구라고 할 수 있다. 또한 tsoa는 swagger와 연동되어 API 명세를 웹에서 문서로 확인할 수 있다.

## swagger란

스웨거 또한 API를 문서화할 수 있도록 도와주는 프레임워크이다. 또한 이 문서를 웹에서 공유할 수 있는 클라우드 플랫폼이기도 한다. 프로젝트와 swagger를 연동하고 yaml이나 json파일의 형식으로 API 형식을 작성하면 이를 스웨거 클라우드에서 보기 좋은 문서 형태로 확인할 수 있다. tsoa를 사용하면 이 yaml, json 파일 작성을 자동으로 수행해주어 편리함을 도모할 수 있다.

## tsoa + swagger 사용방법

> 아래의 내용은 [tsoa 공식문서](https://tsoa-community.github.io/docs)를 참고하였습니다.

### 1. Project init

- tsoa, express, typescript와 관련된 @types를 설치한다.

```sh
yarn add tsoa express
yarn add -D typescript @types/node @types/express
```

- tsconfig.json 생성

```sh
yarn run tsc --init
```

`"experimentalDecorators"` 옵션은 true로 설정해야한다.

- tsoa.json 파일을 생성하여 다음가 같은 설정을 적어준다. 이는 코드를 빌드해 swagger.json파일과 라우팅 파일을 만드는데 사용된다.

```json
// tsoa.json
{
  "entryFile": "src/app.ts", // 파일 탐색을 시작할 지점
  "noImplicitAdditionalProperties": "throw-on-extras", // 공식문서에 나와있는 내용을 아직 제대로 이해하지 못했다.
  "controllerPathGlobs": ["src/**/*Controller.ts"], // 읽어올 컨트롤러 경로
  "spec": {
    //spec은 과거에 swagger라는 이름이었다.
    //최근 업데이트 하면서 바뀐거라 1년 전 문서들만 봐도 모두 swagger라고 되어있다. 웨 아무도 안알려줘...ㅜ
    "basePath": "/api", //api base 경로
    "outputDirectory": "build", //swagger.json 파일을 위치시킬 폴더 경로
    "specVersion": 3 //OpenAPI 스펙 버전으로 3이 가장 최신인 것 같다
  },
  "routes": {
    "routesDir": "./src/routes" // 라우팅파일이 위치할 폴더 경로
  }
}
```

### 2. model & controller & service 정의

- model 정의

```ts
/* src/users/user.ts */
export interface User {
  id: number
  email: string
  name: string
  status?: 'Happy' | 'Sad'
  phoneNumbers: string[]
}
```

- service 정의

```ts
// src/users/usersService.ts
import { User } from './user'

// A post request should not contain an id.
export type UserCreationParams = Pick<User, 'email' | 'name' | 'phoneNumbers'>

export class UsersService {
  public get(id: number, name?: string): User {
    return {
      id,
      email: 'jane@doe.com',
      name: name ?? 'Jane Doe',
      status: 'Happy',
      phoneNumbers: [],
    }
  }

  public create(userCreationParams: UserCreationParams): User {
    return {
      id: Math.floor(Math.random() * 10000), // Random
      status: 'Happy',
      ...userCreationParams,
    }
  }
}
```

- controller 정의

```ts
// src/users/usersController.ts
import { Body, Controller, Get, Path, Post, Query, Route, SuccessResponse } from 'tsoa'
import { User } from './user'
import { UsersService, UserCreationParams } from './usersService'

@Route('users') // 라우팅 경로를 의미
export class UsersController extends Controller {
  @Get('{userId}') // HTTP 메소드
  public async getUser(@Path() userId: number, @Query() name?: string): Promise<User> {
    //@Path는 경로, @Query는 쿼리 스트링
    return new UsersService().get(userId, name)
  }

  @SuccessResponse('201', 'Created') // Custom success response
  @Post()
  public async createUser(@Body() requestBody: UserCreationParams): Promise<void> {
    this.setStatus(201) // set return status 201
    new UsersService().create(requestBody)
    return
  }
}
```

위에서 첫번째 컨트롤러는 app.get('/users/:userId') 형태의 요청을 처리하는 것과 같다. tsoa는 4가지 매개변수를 사용한다.

```sh
- @Path() : 경로
- @Query() : 쿼리
- @Header(): 헤더
- @body(): 바디 (@BodyProps() : 바디의 개별속성)
```

> class는 항상 export로 내보낸다. 현재 tsoa는 export default를 지원하지 않는다.

### 3. app.ts 정의

```ts
// src/app.ts
import express from 'express'

export const app = express()
app.use(express.json())

app.listen(3000)
```

위와 같이 app.ts를 만들어주었으면 tsoa를 통해 route 파일을 빌드해야한다.

```sh
yarn run tsoa routes
```

tsoa.json파일에 명시한 경로대로 routes.ts 파일이 생성된 것을 확인할 수 있다. 내부코드는 복잡하게 짜여있으나 상단에 내가 작성한 컨트롤러가 포함되있는지를 확인할 수 있다. 만약 없으면 routes 파일은 생성되었으나 controller를 읽어오지 못한것이기 때문에 확인해주는 것이 좋을것 같다.

routes 파일 빌드가 완료되었다면 app.ts 파일에 route를 연결한다.

```ts
// src/app.ts
import express from 'express'
import { RegisterRoutes } from './routes/routes'

export const app = express()
app.use(express.json())

RegisterRoutes(app)
app.listen(3000)
```

모든 과정을 마무리 했다면 서버 주소로 접속해 api가 잘 동작하는지 확인한다.

### 4. swagger.json 파일 생성 및 연결

- swagger 관련 모듈을 설치한다.

```sh
yarn add swagger-ui-express
yarn add -D @types/swagger-ui-express
```

- swagger.json 파일 생성

```sh
yarn run swagger
```

위 tsoa.json 파일에 작성한 경로에 swagger.json 파일이 생성된다. 내부에 컨트롤러가 json형식으로 정의되어있음을 확인할 수 있다.

- swagger 클라우드와 연결

```js
// src/app.ts
import express from 'express'
import * as swaggerUi from 'swagger-ui-express'
import { RegisterRoutes } from './routes/routes'

export const app = express()
app.use(express.json())

app.use('/docs', swaggerUi.serve, async (req: express.Request, res: express.Response) => {
  return res.send(swaggerUi.generateHTML(await import('../api/dist/swagger.json')))
})

RegisterRoutes(app)
app.listen(3000)
```

라우팅 연결 함수 위에 다음과 같은 미들웨어를 추가하면 `http://localhost:3000/docs`로 접속했을떄 웹 문서화된 API 명세를 확인할 수 있다. 야호!

## 걱정되는 부분

- API를 자동화할 수 있다는 점에서 매우 편리하지만 팀원들 + 미래에 이 프로젝트를 인계받아 진행할 개발자들이 tsoa 작성법과 사용방법을 따로 공부해야하기 때문에 도입하는 것이 맞는 선택인지 고민이 된다.
- 기존에 함수형으로 작성된 컨트롤러를 class 형태로 교체해야 하는 작업이 추가로 필요하게 되었다.
- 지금 연습으로 시도해본 것은 매우 간단한 API라 큰 어려움 없이 적용했지만 아마 복잡한 API를 구현하게 되면 예상치 못한 에러가 발생할 수도 있겠다는 생각이 든다.

## 결론

결국 tsoa 적용은 하지 않기로 했다. 이유는

- tsoa 커뮤니티가 너무 작아 에러 발생시 정보를 얻기가 너무 힘들었고
- 현재 프로젝트가 npm기반으로 되어있는데 tsoa는 yarn에 최적화되어 있었다.
- 또한 데코레이터를 많이 써야해서 코드가 굉장히 지저분해졌고
- tsoa를 위해 새로운 문법을 공부해야함으로써 프로젝트의 복잡도가 올라가 인수인계시 어려움이 있을것으로 예상됐기 때문이다.

이번 프로젝트를 진행하면서 가장 신경쓰는 부분은 다른 개발자가 이 프로젝트를 이어 개발해도 큰 어려움이 없을 정도로 프로젝트를 구성하는 것이다. 그런 맥락에서 프로젝트에 새로운 기술이나 tool을 도입하는건 상당히 보수적으로 접근해야하는 일인것 같다는 생각이 든다.
