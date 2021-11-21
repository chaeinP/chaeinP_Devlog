---
title: TypeScript_nodejs+ts 환경에 webpack 적용하기
date: '2021-11-21'
tags: ['TypeScript', 'NODEJS']
draft: false
summary: nodejs + ts에 webpack적용하면서 만난 에러들
---

> [typeScript파일 webpack으로 빌드하기](https://chaeinp.vercel.app/blog/TypeScript/06_webpack%EC%9C%BC%EB%A1%9C%20%EB%B9%8C%EB%93%9C%ED%95%98%EA%B8%B0) 포스트 참고.

## webpack 버전 에러 (webpack ver.5 와 webpack ver.4)

이전에 작성된 [typeScript파일 webpack으로 빌드하기](https://chaeinp.vercel.app/blog/TypeScript/06_webpack%EC%9C%BC%EB%A1%9C%20%EB%B9%8C%EB%93%9C%ED%95%98%EA%B8%B0)는 webpack ver.4를 기준으로 작성되었다.

기존에 만들고 있던 node + ts 프로그램에 webpack을 적용해 빌드하자 다음과 같은 에러가 발생하였다.

```sh
ERROR in ./node_modules/serve-static/index.js 19:14-37
Module not found: Error: Can't resolve 'path' in '/Users/chaein/Desktop/study/NodeJS/42Check-in-Server_study/node_modules/serve-static'
.
.
ERROR in ./node_modules/string_decoder/node_modules/safe-buffer/index.js 3:13-30
Module not found: Error: Can't resolve 'buffer' in '/Users/chaein/Desktop/study/NodeJS/42Check-in-Server_study/node_modules/string_decoder/node_modules/safe-buffer'
.
.
```

검색을 해보니 webpack ver4까지는 내부에 자동으로 path와 같은 nodejs 모듈을 지원해주었는데 ver5부터는 지원하지 않는다고 한다. 이에 해당 모듈을 추가로 설치하거나 필요하지 않는 모듈은 config파일에 따로 명시해주어야 한다.

```sh
// 에러와 함께 뜬 가이드 메세지를 보면 자세하게 나와있다.
If you want to include a polyfill, you need to:
        - add a fallback 'resolve.fallback: { "path": require.resolve("path-browserify") }' //resoleve.fallback에 명시하거나
        - install 'path-browserify' // 따로 설치하거나
If you don't want to include a polyfill, you can use an empty module like this:
        resolve.fallback: { "path": false } // 혹은 제외시키겠다고 표시해야함
```

[https://webpack.kr/configuration/resolve/](관련 공식문서)를 보니 표기해야할 내용들이 한두가지 아니었고 나는 결국 webpack ver4로 다운그레이드 해 진행하기로 했다. 그 외 모듈들 (ex. webpack-cli 등) 또한 최선 버전은 webpack ver5만 호환되도록 되어있어 결국 관련된 모든 모듈들을 다운그레이드 해야했다.

## webpack-dev-server 대신 nodemon

전에 공부한대로 수정사항이 감지됐을 때 자동으로 빌드하고 서버에 적용될 수 있도록 webpack-dev-server를 사용하려고 했다. 그러나 webpack-dev-server를 실행하자 다음과 같은 에러가 발생했다.

```js
[webpack-cli] Unable to load '@webpack-cli/serve' command
[webpack-cli] TypeError: options.forEach is not a function
```

검색 도중 스택오버플로우에서 이 문제의 원인으로 생각되는 답변을 발견했다.

```
Webpack-dev-server is great for client side development but it will not deploy Express api's or middleware.
```

[답변 출처](https://stackoverflow.com/questions/35233291/running-a-node-express-server-using-webpack-dev-server)

추가적으로 답변자는 백엔드에는 nodemon을 사용하는 것을 추천한다는 피드백을 남겼고 나는 이 답변을 수용해 webpack-dev-server대신 nodemon을 사용하기로 결정했다.

nodemon은 기본적으로 js파일을 watch모드로 실행시켜주는 도구이지만 ts-node모듈을 함께 사용하면 ts 컴파일 + js 파일 실행을 동시에 수행한다.

nodemons 이전 버전의 경우 ts 컴파일을 js파일 실행과 함께 사용하려면 명령어가 복잡했다.

```sh
nodemon --watch "src/**" --ext "ts,json" --ignore "src/**/*.spec.ts" --exec "ts-node src/index.ts"
```

그러나 최신 버전의 경우 js파일을 실행할때와 똑같은 형태로도 ts컴파일과 실행을 수행할 수 있게 됐다.

```sh
nodemon app.ts
```

자세한 내용은 [여기](https://stackoverflow.com/questions/37979489/how-to-watch-and-reload-ts-node-when-typescript-files-change)서 확인할 수 있다.

위 명령어로 실행을 시키자 문제없이 작동되었다. nodemon은 실행할 때 실시간으로 ts를 js파일로 컴파일해 실행한다. 따라서 프로그램 디렉토리에는 컴파일 된 js 파일이 따로 생성되지 않는다. 만약 배포 버전이 nodemon으로 실행되고 있는 경우 요청이 들어올 때마다 매번 컴파일 과정을 수행하기 때문에 서버가 느려질 수 있다. 따라서 배포 버전은 웹팩으로 빌드 후 js 파일을 실행시키는 것이 바람직해 보인다.

## production용 webpack build

이전 포스트에서 만든 webpack.config.js파일을 이용해 웹팩 빌드를 하자 다음과 같은 에러를 만났다.

```sh
ERROR in ./node_modules/destroy/index.js
Module not found: Error: Can't resolve 'fs' in '~~~~~~~
.
.
ERROR in ./node_modules/express/lib/request.js
Module not found: Error: Can't resolve 'net' in '~~~~~~~
```

원인은 webpack config 파일에서 target 설정을 해주지 않았기 때문이다.

```js
target: "node",
```

webpack target의 기본값은 'web'이다. 이를 node로 변경하지 않으면 node 관련 모듈은 지원하지 않는 것 같다. 이에 따라 target을 "node"로 변경하자 정상적으로 작동했다.

[참고한 블로그](https://velog.io/@paerck25/Node.js-Server-Webpack-%EB%B2%88%EB%93%A4%EB%A7%81)
[관련 공식 문서](https://webpack.kr/configuration/target/)
