---
title: Munetic_passport.js로 local 로그인, jwt 미들웨어 만들기
date: '2021-12-22'
tags: ['Munetic', 'NODEJS', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(6)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## 💡 Passport.js란?

Passport.js는 express 기반 서버에서 사용할 수 있는 인증/인가 미들웨어 모듈입니다. 제공하고 있는 함수들이 매우 추상화되어있는데 비해 공식문서가 친절한 편이 아니라 처음 접하는 분들에게는 다소 이해하기 어려울 수 있어 이 문서를 남깁니다.

## 💡 Passport.js 사용 이유

Passport 활용법만 알고 있으면 인증/인가 로직을 간편하게 구현할 수 있다는 장점이 있습니다. 코드량도 줄일 수 있습니다. 또한 지원하고 있는 OAUTH strategy가 많아 매우 편리합니다. 42서버 OAUTH도 지원합니다.

프로젝트 1팀은 다음의 모듈을 사용하였습니다.

- passport
- passport-local
- passport-jwt

프로젝트 1팀이 패스포트로 구현한 인증 로직 종류는 다음과 같습니다.

- local 로그인
- jwt accessToken으로 인가 확인
- jwt refreshToken으로 accessToken 갱신

인증/인가 api가 앱/어드민이 구분되어있기 때문에 각 3개, 총 6개의 인증 관련 패스포트 로직이 개발되어있습니다.

---

## 💡 Passport.js로 local 로그인 구현하기

### 0️⃣ 패스포트 init

app.ts에 passport를 init해줍니다.

```ts
import passport from 'passport'
app.use(passport.initialize())
```

### 1️⃣ Strategy 인스턴스 생성

passport에서는 Strategy라는 개념을 사용합니다. local strategy 인스턴스는 두개의 인자를 받습니다. 첫번째는 사용자가 로그인을 위해 입력한 데이터이며 두번째는 인증 로직이 담긴 콜백함수입니다.

```ts
import passportLocal from 'passport-local'
const Strategy = passportLocal.Strategy

const LocalStrategy = () =>
  new Strategy(
    //로그인시 login_id, login_password 데이터를 사용함을 명시합니다.
    { usernameField: 'login_id', passwordField: 'login_password' },
    //위에서 정의된 데이터를 받아 실행할 콜백함수입니다.
    localStrategyCallback
  )
```

local Strategy에 담긴 콜백함수는 다음과 같습니다. strategy 콜백 함수는 3가지 인자를 갖습니다. 마지막 인자인 done은 passport 모듈에서 제공하는 콜백함수입니다. done 함수는 인증 strategy 콜백 함수 다음에 수행되는 controller함수에 인증 결과를 전달합니다. 추상화되어있는 함수기 때문에 모듈을 뜯어보지 않는이상 내부적으로 어떤 동작을 하는지 확인할 수 없습니다.

```ts
const localStrategyCallback = async (login_id: string, login_password: string, done: any) => {
  //login_id를 기준으로 해당하는 유저가 존재하는지 db조회를 수행합니다.
  const [user] = await UserService.searchActiveUser({ login_id })
  if (
    !user || // 해당 유저가 존재하는지
    (user.type !== 'Tutor' && user.type !== 'Student') || // Admin 계정이 유입되진 않았는지
    user.deletedAt !== null //삭제된 계정인지
  )
    return done(null, false, {
      message: '입력하신 id에 해당하는 계정이 없습니다.',
    })
  //login_id가 존재한다면 비밀번호가 올바른지 확인합니다. db에 저장된 비밀번호는 bcrypt로 암호화 되어있습니다.
  const encryptedPassword = (await user?.toJSON().login_password) as string
  if (!(await verifyPassword(login_password, encryptedPassword)))
    return done(null, false, { message: '잘못된 비밀번호 입니다.' })

  return done(null, user.toJSON()) //아이디와 비밀번호 모두 올바르면 다음 컨트롤러 함수에 조회한 유저 정보를 넘겨줍니다.
}

export default LocalStrategy
```

### 2️⃣ strategy 함수 호출

라우터에서 인증관련 요청이 들어왔을 때 사용할 strategy를 미들웨어로 호출합니다. 'local'이라는 이름을 함께 부여합니다. passport.authenticate('local')을 선언하면 해당 이름과 매칭되어있는 LocalStrategy()가 호출되는 방식입니다.

```
// routes/index.ts

passport.use('local', LocalStrategy());
```

### 3️⃣ 컨트롤러 생성

strategy 콜백함수에서 전달받은 데이터를 바탕으로 클라이언트에 응답할 컨트롤러 함수를 생성합니다. 위에서 설명한대로 passport.authenticate('local') 을 명시해 LocalStrategy 함수를 실행시킵니다. 이후 LocalStrategy함수 실행 결과(done함수에 담은 결과)가 passport.authenticate 두번째 인자로 주어진 콜백함수에 전달됩니다.

```ts
//auth.controller.ts
export const login: RequestHandler = (req, res, next) => {
  try {
    passport.authenticate('local', async (err, user, info) => {
      if (!user)
        //done에서 넘어온 두번쨰 함수를 기준으로 user가 존재하는지 아닌지를 판단
        return next(new ErrorResponse(Status.UNAUTHORIZED, info.message)) // user가 없으면 done함수에서 전달한 메세지를 에러 메세지로 등록해 응답
      const accessToken = await jwt.accessToken(user)
      const { token, cookieOptions } = await jwt.refreshToken(user)
      res.cookie('refreshToken', token, cookieOptions)
      res.status(Status.OK).json(new ResJSON('request success', accessToken))
    })(req, res, next)
  } catch (err) {
    next(err)
  }
}
```

### 4️⃣ 라우터 연결

컨트롤러와 api 경로를 연결합니다.

```ts
//auth.routes.ts
import * as Auth from '../controllers/auth.controller'
router.post('/login', Auth.login)
```

## 💡 Passport.js로 jwt 인가 미들웨어 구현하기

현 프로젝트에서 클라이언트에 제공하는 accessToken과 refreshToken에는 login_id와 id 정보가 담겨있습니다. jwt Strategy는 이 토큰을 해독해 내부 payload로 들어있는 login_id와 id 정보를 콜백함수로 전달하는 역할을 수행합니다. 작동방식은 local 로그인 방식과 유사합니다. 여기선 accessToken 인가를 담당하는 jwt 미들웨어를 기준으로 설명합니다. refreshToken도 방식을 같지만 StrategyOptions을 다르게 설정해주어야 합니다.

### 0️⃣ jwt Strategy 인스턴스 생성

jwt Strategy또한 두가지 인자를 받습니다. 첫번째는 Strategy 옵션입니다. 토큰을 복호화할 옵션값을 의미합니다. 두번째 함수는 토큰옵션 복호화해 얻은 jwt payload 값을 이용해 해당 유저가 db에 존재하는지를 조회하는 콜백함수가 들어옵니다.

jwt Strategy 옵션을 여러가지가 있습니다. 직접 만들어 사용할 수도 있습니다. 클라이언트 요청에 토큰이 어떤 방식으로 담겨있는지에 따라 적절한 것을 사용하면 됩니다. 현 프로젝트의 경우 accessToken이 Authorization 헤더에 Bearer 형식으로 담겨오기 때문에 `fromAuthHeaderAsBearerToken()`이라는 옵션값을 사용하였습니다.

```ts
import { ExtractJwt, Strategy, StrategyOptions } from 'passport-jwt'

export const accessOpts: StrategyOptions = {
  jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(), //헤더 bearer 토큰 옵션값
  ignoreExpiration: false, // 만기된 토큰인지 판별하는 옵션
  secretOrKey: access_secret, // 환경변수로 등록되어있는 토큰 secret key
}

//accessOpts에서 걸러지면 자동으로 401 UNATHORIZED가 응답
//accessOpts에서 통과된 요청은 payload정보를 Strategy 콜백함수로 전달

const JwtStrategyCallback = async (jwt_payload: { sub: any; login_id: any }, done: any) => {
  const [user] = await UserService.searchActiveUser({
    // 넘어온 payload로 db 조회
    login_id: jwt_payload.login_id,
    type: {
      [Op.or]: ['Tutor', 'Student'],
    },
  })
  if (user) {
    return done(null, user.toJSON()) // db에 유저가 존재하면 done함수 두번째 인자로 해당 정보를 전달
  } else {
    return done(null, false) // 자동으로 401 UNATHORIZED 응답
  }
}

export const JwtAccessStrategy = () => new Strategy(accessOpts, JwtStrategyCallback)
```

이렇게 StrategyCallback 까지 통과되면 done함수 두번째인자로 들어있는 정보는 다음 컨트롤러 함수에 req.user 객체 생성돼 전달된다. 타입 스크립트에서는 req.user 타입을 직접 지정해주어야 해당 정보에 접근할 수 있다.

### 1️⃣ req.user 객체 타입 지정

```ts
// @types/express.d.ts
declare module Express {
  export interface Request {
    user?: {
      login_id: string
      id: number
      login_password?: string
      type: string
    }
  }
}
```

위 생성 타입을 tsc가 컴파일시 반영할 수 있도록 tsconfig.json도 수정해준다.

```json
"typeRoots": [
      "./src/@types",
      "./node_modules/@types"
    ]
```

### 2️⃣ strategy 호출

```
// routes/index.ts

passport.use('local', LocalStrategy());
passport.use('jwt', JwtAccessStrategy());
```

### 3️⃣ 컨트롤러 생성

jwt Strategy는 인가가 필요한 모든 컨트롤러에 미들웨어로 사용할 수 있습니다다. 만약 요청을 보낸 유저의 정보가 필요하다면 req.user 정보를 활용할 수 있습니다. 다음은 예시입니다.

```ts
export const editUserProfile: RequestHandler = async (req, res, next) => {
  try {
    let result: ResJSON
    const user = (await UserService.editUserById(
      Number(req.user.id), //req.user정보 사용
      req.body
    )) as any
    result = new ResJSON('유저 프로필을 수정하는데 성공하였습니다.', user)
    res.status(Status.OK).json(result)
  } catch (err) {
    next(err)
  }
}
```

### 4️⃣ 라우터 연결

아래와 같이 라우터를 생성하면 해당 경로로 들어오는 모든 요청은 jwt Strategy와 콜백 함수를 통해 인가 과정을 거치게 됩니다. jwt passport는 기본적으로 session 사용을 디폴트로 하고 있기 때문에 session:false 설정을 추가로 해주었습니다. 저는 매 요청마다 `passport.authenticate('jwt', {session:false})` 이렇게 적기 번거로워 이름이 짧은 함수로 한번 더 감싸 사용했습니다.

```ts
router.get('/', passport.authenticate('jwt', {session:false}), 컨트롤러 함수);
```
