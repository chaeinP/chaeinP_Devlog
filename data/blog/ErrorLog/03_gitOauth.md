---
title: Error Log_Access-token이 JSON 객체 형태로 들어오지 않을 때
date: '2021-09-08'
tags: ['Error_Log']
draft: false
summary: 요청 헤더의 Accept 속성
---

### 문제상황

Github 소셜 로그인 구현 중 access-token 발급 코드를 작성하였으나 access-token이 원하는 형태로 전달되지 않았다.

내가 받은 응답은 `access_token=토큰 내용&token_type=bearer` 형태로 생긴 문자열이었다. 이렇게 되면 `토큰 내용`을 사용하기 위해 정규표현식이든 뭐든 사용해서 해당 부분만 따로 추출해내야 하는데 애초에 내가 원하는 응답 형태는 JSON 객체 형태였기 때문에 다른 방법을 찾아보기로 했다.

### 해결

> post 요청에 헤더에 `Accept:application/json`옵션을 추가하면 객체 형태로 받을 수 있다!

![](https://images.velog.io/images/pca0046/post/602c6ff1-5ce8-4800-9823-8cad41bbe118/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-09-08%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.03.18.png)
[출처: 깃허브 공식문서](https://docs.github.com/en/developers/apps/building-github-apps/identifying-and-authorizing-users-for-github-apps)

공식문서에는 디폴트 응답이 위 이미지와 같은 객체 형태라 하였으나 내가 받은 응답은 그렇지 못했기 때문에 헤더에 객체 형태로 받아올 수 있는 옵션을 추가해야한다.

```js
const { data } = await axios.post(
    "https://github.com/login/oauth/access_token",
    {
      client_id: clientID,
      client_secret: clientSecret,
      code: authorizationCode,
    },
    { headers: { Accept: "application/json" } }
  );

  res.status(200).send(data);
};
```

문제 해결!
