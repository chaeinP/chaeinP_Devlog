---
title: Nodejs_cls-rtracer && tracer로 로그 기능 구현하기
date: '2021-11-16'
tags: ['NODEJS', 'TYPESCRIPT']
draft: false
summary: 백엔드에서 중요한 로그 기능 구현하기(2)
---

## cls

Http request에 대한 로그를 남길 때 request를 식별할 수 있는 고유 id가 없으면 여러 요청이 동시에 들어오 경우 각 요청을 구별할 수가 없게 된다. 따라서 request마다 고유 id값을 부여해주는 것이 필요하다. java와 같은 멀티 스레딩 언어의 경우 각 스레드에 요청을 할당할때 스레드별로 request id를 부여해 추적하는 것이 어렵지 않다. 이러한 방식을 Thred-Local Stroage `tls`라고 한다.

그러나 nodejs는 싱글 스레드 기반 이벤트 루프로 요청을 처리하기 때문에 java와 같은 방식으로 request id를 부여할 수 없다. 간단하게 구현할 수 있는 방식으로는 요청이 들어왔을 때 식별할 수 있는 id를 생성해 파라미터로 넘겨주는 것이다. 그러나 이러한 방식은 프로그램의 뎁스가 깊어질수록 추적을 위해 생성한 id를 인자로 계속 넘겨주어야 하므로 코드가 지저분하고 번거로워질 수 있다.

이러한 문제를 보완하기 위해 등장한 것이 Continuation-Local Storage `cls`이다. cls는 멀티 스레드 언어에서 사용하는 tls와 유사하게 동작하지만 멀티 스레드 대신 콜백 기능을 활용한 것이다. 대표적인 라이브러리로 `cls-rtracer`가 있다. cls-rtracer는 고유한 request id를 생성하고 호출 체인 어디에서든 생성된 id를 사용할 수 있는 express/koa용 미들웨어를 제공한다.

- 미들웨어 설정

  ```js
  //app.ts
  import rTracer from 'cls-rtracer'

  app.use(rTracer.expressMiddleware())
  ```

이제 필요할 때마다 `const requestId = rTracer.id();`로 request id를 부여해 사용할 수 있다.

## tracer

tracer는 로그를 남기기 위해 사용되는 라이브러리 중 하나이다. node에서 사용되는 logger라이브러리 중 대표적인 것엔 winston이 있다. 42-checkin-server에는 tracer가 사용되었고 tracer가 성능이나 추적기능에 있어 winston보다 뛰어나다는 얘기가 있어 tracer를 사용했다.(사실인지는 모름)

- Daily log 형식 지정하기
  Daily log기능은 일자별로 발생한 로그를 수집하는 기능이다. Dailyfile에 로그 레벨 별 로그 저장 format을 기록하면 지정된 경로에 그날의 로그가 기록된 파일이 생성된다.

```js
//logger.ts

import rTracer from 'cls-rtracer' //requestid 사용을 위해 rTracer 불러오기
import { dailyfile } from 'tracer' // tracer의 dailyfile기능 사용
const rootFolder = './logs' // log파일이 저장될 경로 지정
const logFormat = '{{timestamp}} <{{title}}> ({{file}}:{{line}}) {{message}}' // 로그가 기록될 format 지정, 로그 발생 시간, 로그 레벨, 로그가 찍힌 파일과 라인 넘버, 로그 메세지 순으로 기록된다.
const logDateformat = 'yyyy-mm-dd HH:MM:ss' //로그의 timestamp 포맷을 지정한다.
```

로그 레벨은 info와 error로 나누었다. 각 레벨에 맞는 dailyfile을 생성하고 logger 객체로 관련 메소드를 묶어주었다.

```ts
const logger_info = dailyfile({
  root: rootFolder,
  allLogsFileName: 'info',
  stackIndex: 1, //무슨 역할이지?
  level: 'info',
  format: logFormat,
  dateformat: logDateformat,
})

const logger_error = dailyfile({
  root: rootFolder,
  allLogsFileName: 'error',
  stackIndex: 1,
  level: 'error',
  format: logFormat,
  dateformat: logDateformat,
})

export const logger = {
  info(...trace: any[]) {
    return logger_info.info(rTracer.id(), ...trace) //들어간 인자는 message부분에 입력된다. 여기에 rTracer를 사용 requestId를 부여했다.
  },
  error(...trace: any[]) {
    logger_info.info(rTracer.id(), trace)
    return logger_error.error(rTracer.id(), trace)
  },
}
```

- log 찍기
  만든 logger.info로 들어오는 모든 request에 대한 log를 남기기로 했다. app.ts에 다음의 함수를 추가해 로그를 남겼다.

```js
//app.ts
import { logger } from './modules/logger'

//...
app.use((req: Request, res: Response, next: NextFunction) => {
  const {
    method,
    path,
    url,
    query,
    headers: { cookie },
    body,
  } = req
  const request = { method, path, cookie, body, url, query }
  logger.info({ request })
  next()
})
```

- 로그 확인하기
  요청을 보내니 Dailyfile을 생성할 때 지정한 './log' 경로에 `info.20211128.log`파일이 생성됐다. 내부에는 다음과 같은 기록이 남았다.

```js
/*
./log/info.20211128.log
*/
2021-11-28 16:31:28 <info> (app.ts:28) 334905a0-501d-11ec-88ac-73b992c576c9 {
  request: {
    method: 'GET',
    path: '/users',
    cookie: undefined,
    body: {},
    url: '/users',
    query: {}
  }
}
2021-11-28 16:32:23 <info> (app.ts:28) 53ef2d20-501d-11ec-b892-a7272a4d418b {
  request: {
    method: 'GET',
    path: '/usersdfs',
    cookie: undefined,
    body: {},
    url: '/usersdfs',
    query: {}
  }
}
```

참고자료

- [CLS에 대해 알아보자](https://www.kyungyeon.dev/posts/43)
- [nodejs 서버에서 로그를 남겨보자](https://42place.innovationacademy.kr/archives/9137)
