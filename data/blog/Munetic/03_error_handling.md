---
title: Munetic_미들웨어를 이용한 ErrorHandling
date: '2021-12-19'
tags: ['Munetic', 'NODEJS', 'TypeScript']
draft: false
summary: Munetic Project_학습 기록(3)
---

> [Munetic github](https://github.com/innovationacademy-kr/slabs-munetic)

> 이 글은 Munetic 기업 협력 프로젝트 1팀의 프로젝트 기간이 끝난 뒤 **인수인계를 위해 정리한 학습 기록 및 회고 글** 입니다.
> 원글은 해당 깃레포 위키에서 확인할 수 있습니다. 다음의 글에 오류나 잘못된 정보가 있다면 알려주시고 정정 부탁드립니다.

## ErrorResponse 객체 사용방법

컨트롤러 함수 및 서비스 함수에서 발생하는 에러는 에러 객체를 통해 그 형태를 통일했습니다.

```ts
// modules/errorResponse.ts
export default class ErrorResponse extends Error {
  status: number

  constructor(status: number, message: string) {
    super(message)
    this.status = status
  }
}
```

사용 예시는 다음과 같습니다.

```ts
/**
 * 아이디/이메일/닉네임 중복검사
 */
export const isValidInfo: RequestHandler = async (req, res, next) => {
  try {
    const userList = await UserService.searchAllUser(req.query)
    if (userList.length === 0) {
      res.status(Status.OK).json(new ResJSON('사용할 수 있는 유저 정보 입니다.', {}))
    } else throw new ErrorResponse(Status.BAD_REQUEST, '이미 존재하는 유저 정보 입니다.') //ErrorResponse객체를 이용해 Error객체를 발생시킵니다.
  } catch (err) {
    next(err) //발생된 Error는 next()를 통해 다음 미들웨어로 전달됩니다.
  }
}
```

## errorHandler 미들웨어

발생한 ErrorResponse객체는 errorHandler 함수에서 클라이언트에 응답으로 전달됩니다. 여기서 ErrprResponse 객체 형태로 들어오지 못한 에러는 Internal_Server_Error 메세지를 담은 ErrorResponse객체로 재가공해 응답합니다.

```ts
export default function errorHandler(
  err: Error | ErrorResponse,
  req: Request,
  res: Response,
  next: NextFunction
) {
  let error: ErrorResponse
  if (!(err instanceof ErrorResponse)) {
    // 인자로 들어온 에러 객체가 ErrorResponse 인스턴스가 아니면
    console.log(err)
    error = new ErrorResponse( // ErrorResponse형태로 만들어준다.
      Status.INTERNAL_SERVER_ERROR,
      '서버에서 오류가 발생했습니다. 잠시 후 다시 시도해주세요.'
    )
  } else error = err as ErrorResponse
  res.status(error.status).json(error.message) // 에러를 클라이언트에 전달한다.
  return
}
```

이 함수는 app.ts에 가장 마지막에 미들웨어로 호출되어있습니다.

```ts
//app.ts

app.use(errorHandler)
```
