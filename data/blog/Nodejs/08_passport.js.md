---
title: Nodejs_passportjs로 구글 OAUTH구현하기
date: '2021-12-06'
tags: ['NODEJS']
draft: false
summary: passport js 사용하는 법
---

## passportjs

passport는 여권이라는 이름과 같이 서버에서 사용자를 인증하기 위해 사용하는 Node.js용 미들웨어이다. passport는 다양한 인증 메커니즘(session, jwt 등)을 각 모듈로 패키지화 해 제공하고 있으며 편리하게 인증을 구현할 수 있다. express 웹 기반 애플리케이션에 사용할 수 있다.

passport를 사용하려면 반드시 두 가지 모듈을 설치해야 한다. passport의 핵심 기능을 담고있는 `passport`와 프로젝트 로그인에 맞는 `strategy`가 해당된다. strategy는 특정 provider에 의해 제공되고 있는 passport 모듈이다. 예를 들어 google, facebook 등이 있다. passport는 다양한 strategy를 제공하고 있다. 웬만한 OAUTH는 다 지원된다고 생각하면 된다. strategy 종류는 passport [공식문서](http://www.passportjs.org/packages/)에서 확인할 수 있고 `passport-local`을 설치하면 이 strategy를 직접 구현해 사용할 수도 있다. 나는 google OAUTH를 구현하기 위해 `passport-google-oauth20`를 설치했다.

## Google project setting 하기

[https://console.cloud.google.com](https://console.cloud.google.com)에 접속해서 프로젝트를 생성한다.
생성 후 얻은 client id, client secret, 그리고 기입한 redirect url은 프로그래밍할때 필요하니 기억해두도록 한다.

이때 발급받은 client id와 client secret은 oauth를 사용할 때 필요한 id/password라고 생각하면 된다. 당연히 client secret은 보안에 유의해야한다.

redirect url은 oauth 인증 이후 redirect할 url을 의미한다. google에서 redirect url을 기입하도록 하는 이유는 해커가 redirect url을 조작해 인증 절차 이후 해커가 의도한 site로 redirect하는 것을 막기 위해서이다. 이를 막지 못한다면 user의 정보가 해커의 손에 넘어갈 수 있다. 따라서 client가 사전에 기입한 redirect url과 코드 상에서 생성한 Strategy 인스턴스의 인자로 등록된 callbackURL이 동일한지를 비교하여 인증 진행을 결정한다.

## passport 시작하기

### Strategy 인스턴스 생성

```js
//app.js
const passport = require('passport')
const GoogleStrategy = require('passport-google-oauth20').Strategy

passport.use(
  new GoogleStrategy({
    clientID: process.env.clientID,
    clientSecret: process.env.clientSecret,
    callbackURL: '/auth/google/callback', //google oauth화면에서 redirect될 서버 url
  })
)
```

### OAUTH route 설정

OAUTH로 연결될 route를 설정한다.

```js
//app.js
app.get(
  '/auth/google', //이 API로 요청이 들어오면
  passport.authenticate('google', {
    //google oauth를 실행한다. 여기서 'google'은 GoogleStrategy가 내부적으로 실행될 수 있도록 하는 일종의 약속 같은 것
    scope: ['profile', 'email'], //scope는 oauth를 통해 얻고자 하는 user 정보를 의미한다. 각 OAUTH마다 제공해주는 scope가 다르다. 각 문서를 참고할 것
  })
)
```

### redirect url route 설정

위에 설정한 /auth/google로 들어가면 oauth로 연결된다 oauth는 인증이 완료되면 입력한 redirect url로 요청을 넘기는데 이때 뒤에 code=''라는 쿼리 스트링이 담긴다. 이 쿼리스트링이 google oauth를 통해 넘어온 유저의 정보를 담고 있다. 이제 redirect url로 들어올 route를 설정해주면 된다.

```js
//app.js

app.get('/auth/google/callback', passport.authenticate('google'))
```

### Access token과 profile 확인하기

앞서 작성한 GoogleStrategy 인스턴스에 콜백 함수를 추가하면 모든 인증 과정이 처리된 후 정해진 url로 redirect했을 때 accessToken과 refreshToken, 그리고 유저의 정보를 확인할 수 있다.

```js
//app.js
passport.use(
  new GoogleStrategy(
    {
      clientID: process.env.clientID,
      clientSecret: process.env.clientSecret,
      callbackURL: '/auth/google/callback', //google oauth화면에서 redirect될 서버 url
    },
    (accessToken, refreshToken, profile) => {
      console.log(accessToken) // 유효한 유저라는 것은 인증하는 토큰으로 일정 시간동안 앱내 활동을 가능하게 한다.
      console.log(refreshToken) // accessToken이 만료되면 refreshToken으로 갱신한다. refreshToken은 따로 설정해주지 않으면 undefined값이 나옴
      console.log(profile) //유저 정보
    }
  )
)
```
