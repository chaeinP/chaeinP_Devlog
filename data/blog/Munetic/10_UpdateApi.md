---
title: Munetic_[리팩토링] Rest API 디자인 가이드에 맞춰 api 수정하기
date: '2022-01-15'
tags: ['Munetic']
draft: false
summary: 이 Rest API로 정말 괜찮은가
---

> [Munetic 프로젝트 레포지토리](https://github.com/innovationacademy-kr/slabs-munetic)

> [Munetic 리팩토링 레포지토리](https://github.com/chaeinP/slabs-munetic)

프로젝트 이후에 코드를 찬찬히 돌아보는 시간을 갖고 있다. API URI은 설계의 중요성을 알면서도 개발하는데 급급해 크게 신경쓰지 못했다. 이번 기회에 REST API 디자인 가이드를 바탕으로 프로젝트 API URI을 점검하고 개선해보고자 한다. 이후에 API 문서부터 프론트까지 대대적으로 바꿔야할 생각을 하니 조금 아득하다. 이 작은 프로젝트에도 기술부채가 많다.

관련해서 검색을 하다 아주 잘 정리되어있는 블로그글을 발견했다. [REST란? REST API 디자인 가이드](https://covenant.tistory.com/241?category=727168)
이 블로그 글에서는 20개 남짓한 REST API 디자인 가이드에 대해 설명하고 있다. 이 블로그에서 제시한 디자인 가이드를 바탕으로 API URI을 평가할 것이다.

### ✅ url에는 케밥케이스를 사용한다.

케밥 케이스는 단어를 `-`로 연결해 표시한다. ex. tutor-id
현재 api에서는 합성어가 없어서 카멜케이스나 스네이크 등으로 표현된 경로가 없으므로 패스한다.

### ✅ 패스 변수(path parameter, query parameter) 에는 카멜케이스를 사용한다.

패스 변수에도 합성어가 없어서 패스한다.

### ❌ Collection에는 단수가 아닌 복수를 사용한다.

Collection은 자원의 집합을 의미한다. 자원이 user라면 Collection은 users.
현 api는 이 기준에 충족하지 않는다. 모든 Collection을 단수로 하고 있다. 대대적인 수정이 필요하다.

수정 전

```
Get /user/{id}
```

수정 후

```
Get /users/{id}
```

### ✅ Collection 으로 시작해서 Identifier로 끝난다.

위 문장은 `/users/{id}`와 같이 구성된 api를 의미한다. 현재 이 가이드에서 위배되는 api는 없다.

### ❌ 동사보다 명사를 사용한다.

다른 api들은 괜찮은데 auth관련 api들이 이 가이드에 위배된다. `/login`, `/logout`처럼 endpoint가 동사로 되어있기 때문이다. 이와 관련해서 검색을 좀 해봤는데 의견이 분분하다...

[stackoverflow-login,logout restfully design](https://stackoverflow.com/questions/7140074/restfully-design-login-or-register-resources)

[stackoverflow-What method should I use for a login (authentication) request?](https://stackoverflow.com/questions/5868786/what-method-should-i-use-for-a-login-authentication-request)

문서들을 전전하다 이 문서를 봤다. [Rest Api 튜토리얼](https://restfulapi.net/resource-naming/). 이 문서에서는 컨트롤러 리소스의 경우 동사원형을 사용한다고 한다.

```
http://api.example.com/cart-management/users/{id}/cart/checkout(동사원형)
```

Rest api는 전적으로 서버입장에서 자원의 이동 시각으로 바라보아야 한다고 하는데 인증 관련 로직같은 경우는 그런 시각으로 표현하기에 어느정도 한계가 있는것 같다.

로그인/로그아웃/토큰 재발급의 경우 동사 사용을 유지하되 회원가입이나 비밀번호 변경과 같이 DB변경이 일어나는 경우는 다음과 같이 수정했다.

- 회원가입

  ```bash
  # 수정 전
  Post /auth/signup

  # 수정 후
  Post /users
  ```

- 비밀번호 변경

  ```bash
  # 수정 전
  Patch /auth/password

  # 수정 후
  Patch /users/{id}/password
  ```

### ✅ 가급적 소문자를 사용한다.

가이드에서 벗어난 api가 없다. 통과!

### ✅ HTTP 메서드로 표현한다.

- GET: 리소스 정보를 조회
- POST: 새로운 리소스를 생성
- PUT: 존재하는 리소스를 수정
- PATCH: 제공된 리소스로 리소스 수정. 제공된 필드만 업데이트
- DELETE: 존재하는 리소스를 삭제.
  통과!

### ✅ 적절한 상태값을 응답한다.

프로젝트에선 http-status라는 외부 모듈을 사용해 상태코드를 응답하고자 하였음으로 크게 위배되는 api는 없어보인다.

### ❌ JSON property에는 카멜케이스(camelCase)를 사용

JSON 응답 property는 DB 필드값을 그대로 사용했다. DB 조건으로 사용하는 경우나 JSON 객체를 바로 instance로 생성할 때 편리했기 때문인데 응답과정에서는 DB 아키텍쳐나 정보가 그대로 노출되는 것은 바람직하지 않다고 한다. 그런데 이 부분은 교체하면 클라이언트 코드를 수정해야하는 부분이 많아 좀 더 고민해보아야할 것 같다.

### ✅ URI에 파일 확장자를 포함하지 않는다.

파일의 경우 Accept header를 활용하고 URL에 표시하지 않는다.

```
[GET] covenant.tistory.com/users/1/profile-img
Accept: image/jpg
```

해당하는 URL이 없기 때문에 통과

### ✅ API 버전을 위해서 서수를 사용한다.

```
https://kapi.kakao.com/v2/user/me
```

현재 API 버전 관리는 따로 하고 있지 않다. 현재 프로덕트는 다른 팀이 개발중이고 지금 내가 하고 있는 리팩터링은 학습 목적이 커 API 버전 관리는 따로 하지 않아도 괜찮을 것 같다.

### ✅ list 리소스의 경우 total 갯수도 함께 응답한다.

list의 경우 sequelize의 findAndCountAll 함수를 사용해 total 갯수를 함께 응답하고 있으므로 통과

### ❌ 리소스 이름에는 테이블 명을 사용하지 않는다.

현대 collections이 단수로 되어있는 이유는 테이블 명을 그대로 따왔기 때문이다. collections을 복수로 변경하면 함께 해결될 수 있을 것 같다.

### ✅ API 문서화 도구를 사용하자

현재 swagger를 사용하고 있다. 하나의 yml파일에 모든 api를 기록하고 있어 유지보수가 어려운 상황이다. collections별로 분리하는 과정을 거쳐야할 것 같다.

### ❌ 모니터링

Rest API 가이드에서는 Restful HTTP 서비스를 위해 /health, /version, /metrics, /debug, /status와 같은 endpoint가 필요하다고 한다.

- /health : api 서버가 정상적으로 작동하는지를 확인하는 api. 다른 프로젝트에서 cron으로 3초에 한번씩 api를 보내 서버가 불안정할시 요청을 다른 서버로 이관하는 api 처리 방법을 본적이 있다. 안정적인 요청 응답을 위해서 이러한 endpoint추가가 필요해보인다.

- /version: API 버전 응답

- /metrics: 평균 응답 시간과 같은 다양한 metrics을 응답

### 결론

API는 한번 확정되면 수정하기가 매우 어렵다. 클라이언트 코드 까지 모두 변경해야하기 때문이다. API를 잘못 설계하면 API가 새로 추가될때마다 일관성이 떨어지고 모호해져 협업에 큰 방해가 될 수 있다. 아직도 Restful한 API가 무엇인지 한마디로 정의하기가 어렵다. 디자인 가이드에 따라 API를 수정해나가면서 Restful한 API가 어떤 것인지 더 공부해나가야 겠다.
