---
title: Nodejs_express 미들웨어란?
date: '2021-12-02'
tags: ['NODEJS']
draft: false
summary: express 공식문서 뜯어보기
---

공식 문서에서 말하는 express에 대한 설명은 다음과 같다.

> Express는 자체적인 최소한의 기능을 갖춘 라우팅 및 미들웨어 웹 프레임워크이며, Express 애플리케이션은 기본적으로 일련의 미들웨어 함수 호출입니다.

Express는 미들웨어 웹 프레임워크며 Express 애플리케이션은 기본적으로 미들웨어 함수가 호출되는 방식으로 작동한다고 한다.

## Exoress에서 미들웨어란?

공식 문서에서는 미들웨어를 다음과 같이 정의하고 있다.

> 미들웨어 함수는 요청 오브젝트(req), 응답 오브젝트 (res), 그리고 애플리케이션의 요청-응답 주기 중 그 다음의 미들웨어 함수 대한 액세스 권한을 갖는 함수입니다. 그 다음의 미들웨어 함수는 일반적으로 next라는 이름의 변수로 표시됩니다.

이름 그대로 미들웨어 함수는 요청-응답 그 사이에서 일련의 처리 과정을 담당한다. 미들웨어 함수는 next라는 변수를 통해 그 다음 작업의 실행권한을 갖는다. 미들웨어 함수를 실행하고 나서 그 다음 미들웨어(현재 미들웨어 함수에서는 next 변수)를 실행시키지 않으면 작업은 거기서 멈추게 된다.

다음의 예를 보자.

```js
const express = require('express')
const app = express()

app.use(function (req, res, next) {
  console.log('HI')
}) // 모든 요청은 이 미들웨어를 지나가지만 next()를 실행해주지 않기 때문에 요청-응답 주기가 이 코드에서 종료된다.

app.get('/', (req, res) => {
  res.json({ massage: 'ok' }) //이 함수는 위 미들웨어에서 next()로 실행시키지 않았기 때문에 실행되지 않는다.
})
```

위의 코드에서 get요청의 콜백함수를 실행시키려면 next()를 코드에 추가해야 한다.

```js
const express = require('express')
const app = express()

app.use(function (req, res, next) {
  console.log('HI')
  next() // 다음 미들웨어 함수를 실행시켜 코드 흐름을 다음으로 전개한다.
})

app.get('/', (req, res) => {
  res.json({ massage: 'ok' })
})
```

## 미들웨어의 역할

미들웨어는 express에서 다음과 같은 역할을 수행한다.

- 모든 코드를 실행 (무슨 뜻이지...?)
- 요청 및 응답 오브젝트에 대한 변경을 실행.
- 요청-응답 주기를 종료.
- 스택 내의 그 다음 미들웨어 함수를 호출.

## 미들웨어의 종류

### 1. 애플리케이션 레벨 미들웨어

app.use(), app.METHOD()(get, put, post등)에 바인드 되어있는 미들웨어를 함수를 말한다. 다음과 같이 마운트 경로가 없는 경우 앱이 요청을 수신할 때마다 실행된다.

```js
const app = express()

app.use(function (req, res, next) {
  console.log('Time', Date.now())
  next()
})
```

마운트 경로가 있는 경우 해당 경로에 대한 모든 HTTP요청에 대해 실행된다.

```js
app.get('/user/:id', function (req, res, next) {
  res.send('USER')
})
```

하나의 마운트 경로에 일련의 미들웨어 함수를 위치시킬 수도 있다. 이 또한 next()가 없으면 해당 함수에서 요청-응답 주기가 종료된다. 이처럼 하나의 마운트 경로에 여러개의 미들웨어가 있는 경우 미들웨어 스택이라고 한다.

```js
app.use(
  '/user/:id',
  function (req, res, next) {
    console.log('Request URL:', req.originalUrl) // 종료, 이 이후 코드는 실행되지 않음
  },
  function (req, res, next) {
    console.log('Request Type:', req.method)
    next()
  }
)

// next() 추가.
app.use(
  '/user/:id',
  function (req, res, next) {
    console.log('Request URL:', req.originalUrl)
    next() // 두번째 함수 실행.
  },
  function (req, res, next) {
    console.log('Request Type:', req.method)
    next()
  }
)
```

라우터 미들웨어 스택의 나머지 미들웨어 함수들을 건너 뛰고 다음 라우터의 미들웨어를 호출하고 싶을 땐 next('route')로 제어권을 넘길 수 있다. next('route')는 app.METHOD() 또는 router.METHOD()함수를 이용해 로드된 미들웨어 함수에서만 작동된다.

```js
app.get(
  '/',
  function (req, res, next) {
    if (true) next('route')
    // 다음 라우터로 제어권이 넘어감
    else next()
  },
  function (req, res, next) {
    console.log('hi') // 실행 안됨
  }
)

app.get('/', function (req, res, next) {
  console.log('hey') //앞 미들웨어 함수의 next('route')로 실행됨
})
```

```js
app.get(
  '/',
  function (req, res, next) {
    if (false) next('route')
    else next() // 미들웨어 스택에 있는 다음 미들웨어 실행
  },
  function (req, res, next) {
    console.log('hi') // 실행되고 요청 주기 종료
  }
)

app.get('/', function (req, res, next) {
  console.log('hey') // 실행 안됨
})
```

### 2.라우터 레벨 미들웨어

라우터 레벨 미들웨어는 express.Router()인스턴스에 바인드된다는 점을 제외하면 애플리케이션 레벨 미들웨어와 동일하게 작동한다.

```js
var app = express()
var router = express.Router()

router.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})
```

### 3. 오류 처리 미들웨어

다른 미들웨어 함수와 달리 오류 처리 미들웨어는 req, res, next 앞에 error라는 인수를 추가로 갖는다.

```js
app.use(function (err, req, res, next) {
  console.error(err.stack)
  res.status(500).send('Something broke!')
})
```

오류 처리 미들웨어는 app.use() 및 라우트 호출을 정의한 후 마지막으로 정의해야한다. 오류 처리 미들웨어는 여러개를 사용해 조직적으로 에러를 관리할 수 있다.

```js
var bodyParser = require('body-parser')
var methodOverride = require('method-override')

app.use(bodyParser())
app.use(methodOverride())
app.use(logErrors)
app.use(clientErrorHandler)
app.use(errorHandler)
```

```js
function logErrors(err, req, res, next) {
  console.error(err.stack)
  next(err) // clienrErrorHandler 미들웨어로 에러 전달.
}
```

```js
function clientErrorHandler(err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something failed!' }) // 미들웨어 실행 종료, errorHandler 미들웨어 함수는 실행되지 않음.
  } else {
    next(err) // 에러 전달
  }
}
```

```js
function errorHandler(err, req, res, next) {
  res.status(500)
  res.render('error', { error: err }) // 요청-응답 주기 종료
}
```

next('route')를 제외하고 next()함수로 어떤 내용을 전달하는 경우 Express는 현재의 요청에 오류가 있는 것으로 간주하고 오류 처리와 관련되지 않은 나머지 라우팅 및 미들웨어함수를 건너뛴다.

```js
app.get('/', (req: Request, res: Response, next: NextFunction) => {
  console.log('hello')
  next(req) //next()에 어떤 내용을 전달하고 있음, 바로 에러 처리 미들웨어로 넘어감
})

app.use(() => {
  console.log('hi') // 실행되지 않음
})

app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.log('err') // 실행
})
```

오류 처리로 넘어가기 전 미들웨어 함수를 실행시키고 싶다면 next('Route')를 이용해 다음 라우터 핸들러로 이동시킨다.

```js
app.get('/a_route_behind_paywall', function checkIfPaidSubscriber(req, res, next) {
  if (!req.user.hasPaid) {
    // continue handling this request
    next('route')
  }
})
```

참조

- [express 공식 문서 - 미들웨어 사용](https://expressjs.com/ko/guide/using-middleware.html)
