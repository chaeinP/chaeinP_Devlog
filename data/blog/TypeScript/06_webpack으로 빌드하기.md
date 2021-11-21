---
title: TypeScript_typeScript파일 webpack으로 빌드하기
date: '2021-11-18'
tags: ['TypeScript']
draft: false
summary: typeScript와 webpack
---

## 타입스크립트 프로젝트에 웹팩 설치하기

```shell
npm install --save-dev webpack webpack-cli webpack-dev-server typescript ts-loader clean-webpack-plugin
```

- webpack : 웹팩, 타입스크립트 코드를 자바스크립트 코드로 컴파일하고 컴파일된 파일을 번들링하는 역할을 수행한다.
- webpack-cli : 웹팩 명령어를 실행하기 위한 모듈, webpack ver4이상부터는 webpack 명령어를 사용하기 위해선 해당 모듈이 필요하다.
- webpack-dev-server : 웹팩 빌트인 데브서버, 빌드된 결과물에서 변동사항이 생기면 자동으로 감지해 rebuild하고 브라우저에 반영해주기 때문에 빠른 개발이 가능해진다.
- typescript : typescript가 전역으로 설치된 경우 각 프로젝트마다 개별 typescript를 의존성 모듈로 설치해주는 것이 좋다. 전역 typescript가 업데이트 되거나 버전이 변경됐을 때 이전에 만들어진 프로젝트에 영향이 미치는 것을 방지할 수 있기 때문
- ts-loader : ts-loader는 웹팩이 ts코드를 js코드로 컴파일 할 수 있도록 지원한다.
- clean-webpack-plugin: 프로젝트를 빌드할 때마다 이전 빌드 파일은 모두 삭제하는 웹팩 플러그인

## webpack.config.js 설정

webpackconfig.json을 설정하기 전에 tsconfig.json설정이 제대로 되어있는지 확인한다. ts-loader 가 이 파일을 기반으로 webpack에 컴파일 방법을 전달하기 때문.

- tsconfig.json 확인사항

  - target과 module설정
  - outdir 설정
  - rootdir 설정은 필요없어진다. webpack이 rootdir 역할을 수행하기 때문

- webpack.config.json 설정방법

  - webpack.config.json파일은 자바스크립트와 nodejs문법으로 구성할 수 있다.
  - 웹팩이 정상적으로 작동하게 하기 위해선 import된 파일들에 `.js`확장자를 삭제해야한다. 삭제하지 않으면 웹팩이 더블 확장자로 파일을 찾아 에러가 발생할 수 있다.

  ```js
  import { controller } from './controller.js' /* X */
  import { controller } from './controller' /* O */
  ```

  - 엔트리 포인트, output파일 설정 입력하기

  ```js
  const path = require('path');	// nodejs 내장 라이브러리로 따로 import할 필요 없이 바로 사용

  module.exports = {
    entry: './src/app.ts',
    output: {
      filename: 'bundle.js',
      // ''bundle.[contenthash].js'로 설정하면 파일에 고유한 해쉬값이 추가된다. 이는 브라우저 캐싱을 사용할 때 용이하다.
      path: path.resolve(__dirname, 'dist');
  	  // path는 tsconfig.json파일의 outdir와 경로가 같아야 한다.
  	  // path에는 상대경로를 쓰지 않고 절대 경로를 써야 한다.
  	  // 절대 경로는 nodejs 내장 모듈인 path를 통해 표현할 수 있다.
    // __dirname은 현재 위치한 디렉토리를 의미한다. ,를 기준으로 둘을 합친다. __dirnme/dist
    },
  }
  ```

  - 컴파일에 필요한 명령을 module에 전달한다.

  ```ts
  const path = require('path');

  module.exports = {
    entry: './src/app.ts',
    output: {
  	  filename: 'bundle.js',
  	  path: path.resolve(__dirname, 'dist'),
    },
    module: {
  	  rules: [
  		  {
  			  test: /\.ts$/,
  			  // 어떤 파일에 이 rule을 적용할 것인가를 의미
  			  use: 'ts-loader',
  			  // 위 파일들에 어떤 로더를 사용할 것인지를 의미
  			  // 관련한 환경설정은 tsconfig.json에 적혀있기 때문에 추가적인 환경설정은 적지 않아도 됨
  			  exclude: /node_modules/,
  			  // 컴파일과 빌드 과정에서 exclude시킨다.
  		  }
  	  ]
    }
    resolve: {
  	  extensions: ['.ts', '.js'],
  	  // 웹팩이 관리해야할 확장자들의 모음
    }
  }
  ```

  - 소스맵 기능 추가

  소스맵이란 배포용으로 빌드한 파일과 원본 파일을 연결시켜주는 기능을 한다. 최적화로 압축된 파일이 배포 후 에러가 난다면 디버깅이 어려워진다. 이때 소스맵을 이용해 원본 소스와 빌드파일을 연결해두었다면 원본 소스의 어떤 부분에서 에러가 났는지 확인할 수 있다.

  ```ts
  const path = require('path');

  module.exports = {
    entry: './src/app.ts',
    output: {
  	  filename: 'bundle.js',
  	  path: path.resolve(__dirname, 'dist'),
    },
    devtool: 'inline-source-map',
    // 이 기능을 사용하기 위해선 tsconfig.json 파일 또한 "souceMap : true"로 설정되어있어야 한다.
    module: {
  	  rules: [
  		  {
  			  test: /\.ts$/,
  			  use: 'ts-loader',
  			  exclude: /node_modules/,
  		  }
  	  ]
    }
    resolve: {
  	  extensions: ['.ts', '.js'],
    }
  }
  ```

## 웹팩으로 빌드하기

```js
//package.json
"scripts" : {
	"build": "webpack",
}
```

`npm run build`

## webpack-dev-server 사용해 개발용 워크플로우 만들기

개발을 하고 있는 중이라면 수정과 빌드를 반복하는 것은 매우 번거로운 일이다. webpack-dev-serer는 메모리 디스크에 파일을 빌드해 저장하고 변경사항이 있을 때마다 자동으로 빌드하고 서버에 업데이트 한다. 이 빌드는 실제 디렉토리에서 이루어지지 않고 webpack 개발 서버의 메모리에서 이루어지기 때문에 실제로 빌드파일이 생성되지는 않는다.

webpack-dev-server를 사용하기 위해선 webpack.config.json파일에 설정을 추가해야한다.

- 먼저 이미 빌드된 파일이 있다면 빌드파일은 삭제한다.
- output에 publicPath 설정을 추가한다.

  ```js
  const path = require('path');

  module.exports = {
    entry: './src/app.ts',
    output: {
  	  filename: 'bundle.js',
  	  path: path.resolve(__dirname, 'dist'),
      publicPath: 'dist' //웹팩 데브 서버의 경로를 의미
    },
    devtool: 'inline-source-map',
    module: {
  	  rules: [
  		  {
  			  test: /\.ts$/,
  			  use: 'ts-loader',
  			  exclude: /node_modules/,
  		  }
  	  ]
    }
    resolve: {
  	  extensions: ['.ts', '.js'],
    }
  }
  ```

- 웹팩 설정을 개발용과 배포용으로 나누어 사용하는것이 적절하다. 이때 개발용 설정은 mode: 'development'이다.

  ```js
  const path = require('path');

  module.exports = {
    mode: 'development',
  	entry: './src/app.ts',
  	output: {
  		filename: 'bundle.js',
  		path: path.resolve(__dirname, 'dist'),
  		publicPath: 'dist' //웹팩 데브 서버의 경로를 의미
  	},
  	devtool: 'inline-source-map',
  	module: {
  	rules: [
  		{
  			test: /\.ts$/,
  			use: 'ts-loader',
  			exclude: /node_modules/,
  		}
  	]
  	}
  	resolve: {
  	extensions: ['.ts', '.js'],
  	}
  }
  ```

  - package.json에 webpack-dev-server 설정을 추가한다.
    ```js
    //package.json
    "scripts" : {
    	"start": "webpack-dev-server",
    }
    ```

## 배포용 webpack.config.prod.js 설정

- config파일의 이름은 프로그래머 마음대로 지으면 된다.
- mode를 배포용으로 바꾼다.
- webpack-dev-server 관련 옵션 삭제
- 소스맵 설정 삭제
- 관련 플러그인 추가. module과 rules이 각 파일에 해당하는 설정들이라면 plugins는 워크플로우 전체에 해당하는 설정이다.

```js
  const path = require('path');
  const CleanPlugin = requrie('clean-webpack-plugin'); // 웹팩플러그인 불러오기

  module.exports = {
    mode: 'production', //배포용
  	entry: './src/app.ts',
  	output: {
		filename: 'bundle.js',
		path: path.resolve(__dirname, 'dist'),
	//dev서버 경로 삭제
  	},
  	devtool: 'none',
  	module: {
  	rules: [
  		{
  			test: /\.ts$/,
  			use: 'ts-loader',
  			exclude: /node_modules/,
  		}
  	]
  	}
  	resolve: {
  		extensions: ['.ts', '.js'],
  	},
	plugins: [
		new CleanPlugin.CleanWebpackPlugin()
	]
  }
```

- package.json script 추가

```js
//package.json
"scripts" : {
	"start": "webpack-dev-server",
	"build": "webpack --config webpack.config.prod.js"
	// --config옵션이 없으면 webpack은 default로 webpack.config.js파일을 사용한다.
	// --config를 추가하고 해당 파일 이름을 입력하면 파일이름을 config파일로 사용한다.
}
```

더 자세한 내용은 [웹팩 공식문서](https://webpack.kr/concepts/)를 확인할 것
