---
title: Nodejs_TypeScript로 Nodejs 시작하기
date: '2021-11-09'
tags: ['NODEJS', 'TYPESCRIPT']
draft: false
summary: TypeScript로 Nodejs 개발 연습(1)
---

### require vs import

```js
const app = require(express)
import express from 'express'
```

nodejs로 된 코드들을 보면 모듈을 불러올 때 require()를 쓰는 경우도 있고 import구분을 쓰는 경우도 있다.

- require()는 nodejs문법이며 import는 ES6문법이다.
- require()는 프로그램 어느 지점에서나 호출할 수 있지만 import는 파일의 시작부분에서만 사용할 수 있다.
- import문은 사용자가 필요한 모듈 부분만 선택해서 로드할 수 있기 때문에 더 선호되면 성능과 메모리 측면에서도 require()보다 우수하다.
- require()는 nodejs문이기 때문에 typeScript가 읽지 못한다. 이를 해결하기 위해선 `@types/node`를 의존성 모듈로 설치해야한다.

```
npm install --save-dev @types/node
```

- import문은 typescript가 읽을 수 있기 때문에 따로 type설치를 진행하지 않아도 된다.
- typescript로 모듈을 불러올때는 import문만 사용한다. `const app = require('express')`는 작동하지 않는다는 뜻. import 구문을 사용한 ts파일을 nodejs 환경에서 컴파일해보면 다른 문법으로 변경돼있는 것을 볼 수 있다.
- typescript에서는 외부 모듈을 불러오면 불러오는 모듈(ex. express)의 타입을 알지 못하기 때문에 선언파일을 찾을 수 없다는 에러가 발생한다. 이때 해결 방법은 두가지다.

  - `@types/express` 설치

  ```
  npm install --save-dev @types/express
  ```

  - 모듈을 선언하는 .d.ts파일 추가

  ```js
  /*
  ./src/@types/express.d.ts
  */
  import { IJwtUser } from '../modules/jwt.strategy'
  import { Users } from '../models/users';

  declare module "express" {
     export interface Request {
        clientIp: string;
        user: {
           jwt: IJwtUser,
           ft: Users,
        }
     }
  }
  ```

  > 42 checkin backend는 두 방법을 모두 사용했던데 이유는 모르겠다. 선언 파일은 다른 목적으로 작성했을 수도 있을 것 같다.

### 미들웨어 타입

미들웨어 함수에서 사용되는 인자의 타입은 다음과 같다.

```js
import express, { Request, Response, NextFunction } from 'express'

export const app = express()

app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  res.status(500).send({ message: err.message })
})
```

하지만 매번 이렇게 인자의 타입을 지정해주는건 번거롭다. 그럴땐 `import {RequestHnadler} from 'express'`를 사용한다. 이 실행문은 어떤 실행코드를 불러오는 것이 아니라 type만 불러오는 것이기 때문에 js파일로 컴파일하면 사라지게 된다.

```js
// controller
import { RequestHandler } from 'express'

export const createTodo: RequestHandler = (req, res, next) => {}
```

### body-parser

Express 4.X 이전에는 body-parser라는 추가 패키지를 사용해야만 req.body 내용을 사용할 수 있었다. 하지만 Express 4.X 이후 해당 모듈이 Express 내에 내장되어 더이상 추가 패키지를 설치하지 않아도 된다.

- 변경 전

```js
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))
```

- 변경 후

```js
app.use(express.json())
app.use(express.urlencoded({ extended: true }))
```

> 42 checkin code에서는 urlencoded() 설정을 따로 안해주었는데 이 설정은 추가 옵션인건지 반드시 설정해주어야하는건지 아직 모르겠음. 근데 안해도 작동은 한다.

### req.body typecasting

req.body로 들어오는 값에 type을 부여하는 방법은 다읍과 같다.

```js
export const createTodo = (req: Request, res: Response, next: NextFunction) => {
  const text = (req.body as { text: string }).text;
  const newTodo = new Todo(Math.random().toString(), text);

  TODOS.push(newTodo);
  res.status(201).json({ message: "Created the todo", createdTodo: newTodo });
};
```

req.body에 text 값을 가져오고 싶을 때 `req.body as {text: string}`으로 타입을 부여한 뒤 객체 값을 가져올 수 있도록 `(req.body as { text: string }).text`로 작성하면 된다.

### params와 query typecasting

req.params는 `api/get/:id`라는 경로가 있을 때, id값을 가져오고 싶을 때 사용한다. 즉, url에 경로에 있는 파라미터를 가져올 때 사용한다. req.query는 url 경로에 있는 쿼리 스트링을 가져올 때 사용한다. `api/get/user?id=1234`면 `id=1234`가 쿼리 스트링이 된다. typescripts에서 req.params, req.query를 사용할 때에는 가져올 데이터의 타입값을 지정해주어야한다.

- 타입값을 지정해주지 않았을 때 params, query의 type은 any가 된다.

```js
export const updateTodo: RequestHandler = (req, res, next) => {
  const todoId = req.params.id
}
```

- 타입값을 지정해주면 params의 type이 스트링이 된다.

```js
// 인자 타입을 어떤 형태로 주었느냐에 따라 다르게 표현할 수 있다.
export const updateTodo = (req: Request<{ id: string }>, res: Response, next: NextFunction) => {
  const todoId = req.params.id
}

export const updateTodo: RequestHandler<{ id: string }> = (req, res, next) => {
  const todoId = req.params.id
}
```
