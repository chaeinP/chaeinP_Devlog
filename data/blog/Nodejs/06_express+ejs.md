---
title: Nodejs_express에 ejs템플릿 엔진 적용하기
date: '2021-11-28'
tags: ['NODEJS']
draft: false
summary: 템플릿 엔진으로 SSR 동적 HTML 구성하기
---

## 서버사이드 템플릿 엔진

서버에서 DB 혹은 API에서 가져온 데이터를 미리 정의된 Template에 넣어 html을 그려서 클라이언트에 전달해주는 역할을 한다. 즉, HTML 코드에서 고정적으로 사용되는 부분은 템플릿으로 만들어두고 동적으로 생성되는 부분만 템플릿 특정 장소에 끼워넣는 방식으로 동작할 수 있도록 해준다.

- [템플릿 엔진이란](https://gmlwjd9405.github.io/2018/12/21/template-engine.html)

## ejs의 특징

HTML 문법을 그대로 사용하되, 추가로 Javascript문법을 사용할 수 있다는 점에서 대중적인 템플릿 엔진 중 하나이다.

## ejs 시작하기

- app.set('view engine', 'ejs')

```js
const express = require('express')
const app = express()

app.set('view engine', 'ejs') //view engine을 ejs로 지정한다.

app.get('/', (req, res) => {
  res.send('HI')
})

app.listen(3000, () => {
  console.log('Listening on port 3000')
})
```

- views 폴더 생성

  express view engine은 view와 관련된 것들은 views라는 폴더에 있을 것이라고 가정한다. views라는 폴더를 생성하고 내부에 ejs파일을 위치시킨다.

  ```html
  <!-- ./views/home.ejs-->
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>HOME</title>
    </head>
    <body>
      <h1>Home page</h1>
      <p>
        Lorem ipsum dolor sit, amet consectetur adipisicing elit. Autem, eaque quod aspernatur alias
        similique commodi impedit placeat nemo repellendus nihil delectus in reprehenderit! Sequi
        iure reprehenderit eveniet facilis perspiciatis alias.
      </p>
      <p>
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Magni ipsum neque error iusto nemo
        facilis maiores nobis odit. Eum ab deleniti cupiditate tenetur praesentium saepe rem
        sapiente tempora fuga blanditiis?
      </p>
    </body>
  </html>
  ```

- ejs파일을 렌더하기

```js
const express = require('express')
const app = express()

app.set('view engine', 'ejs')

app.get('/', (req, res) => {
  res.render('home')
  // render 메소드에 연결한 ejs파일 이름을 기입한다.
  // view engine에 이미 ejs를 사용하겠다고 선언했기 때문에 ejs확장자를 표기할 필요는 없다.(붙여도 된다.)
  // view engine의 기본 경로는 views이기 때문에 파일이 views경로에 있다면 굳이 경로를 표기하지 않아도된다.(만약 다른 폴더에 있으면 경로를 표시해야한다.)
})

app.listen(3000, () => {
  console.log('Listening on port 3000')
})
```

그러나 위에처럼만 작성하면 문제가 발생할 수 있다. 지금 설정된 경로는 process.cwd()의 결과에서 '/views/ejs'를 붙여 실행하게 되어있다. 만약 내가 프로젝트 디렉토리 한단계 앞에서 `nodemon express-ejs-practice/index.js`를 실행했다면 views/ejs 경로를 못찾아 템플릿이 렌더가 안되는 결과가 발생한다.
(`desktop/study/express-ejs-practice/views/index.js`경로로 실행되어야 하는 것이 `desktop/study/views/index.js`경로로 실행되어 에러가 난다는 의미다.) 따라서 예상치 못한 에러를 막기 위해 언제나 루트 디렉토리를 기준으로 경로를 찾을 수 있도록 추가 설정을 진행해야 한다.

- path 를 사용한 경로 설정

```js
const express = require('express')
const app = express()
const path = require('path')

app.set('view engine', 'ejs')
app.set('views', path.join(__dirname, '/views'))
app.get('/', (req, res) => {
  res.render('home')
})

app.listen(3000, () => {
  console.log('Listening on port 3000')
})
```

path는 node에서 지원하는 내장 모듈이다. \_\_dirname은 형재 파일이 위치한 디렉토리를 의미하며 \_\_dirname까지의 절대경로를 계산해 그 뒤로 '/views'를 합친다. app.set으로 views의 경로가 정해져 이제 어떤 경로에서 실행시켜도 ejs가 잘 작동한다.

## ejs tags

ejs는 동적 데이터 렌더를 위해 tag라는 기능을 사용한다. 사용할 수 있는 tags들은 [ejs 공식문서](https://ejs.co/)에서 확인할 수 있다.

- `<%= %>`
  이 태그는 자바스크립트 문법으로 데이터를 추가할 때 사용할 수 있다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>HOME</title>
  </head>
  <body>
    <h1>Home page <%= 'hello world'.toUpperCase() %></h1>
  </body>
</html>
```

```js
//render 결과
Home page HELLO WORLD
```

- 데이터 넘기기
  ejs파일에서 render되는 데이터는 값 그 자체인 것이 좋다. 값을 얻기 위한 추가적 연산이 들어갈수록 render가 오래 걸리기 때문이다. 따라서 연산은 최대한 ejs 파일 밖에서 진행하고 값만 넘겨주어야 한다.

```js
//랜덤 숫자를 데이터로 넘겨주기
//ver1
app.get('/rand', (req, res) => {
  const num = Math.floor(Math.random() * 100)
  res.render('random', { rand: num })
})

//ver2
app.get('/rand', (req, res) => {
  const num = Math.floor(Math.random() * 100)
  res.render('random', { num })
})
```

random으로 계산된 num을 ejs파일 안에서 rand라는 변수로 사용하겠다는 의미다. (리액트에서 props 넘겨주는 거랑 비슷하네..?)

```html
<!-- ver1 -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Random</title>
  </head>
  <body>
    <h1>Your random number is <%= rand %></h1>
    <!-- 넘겨받은 num값이 rand라는 변수에 담겨 출력된다.-->
  </body>
</html>

<!-- ver2 -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Random</title>
  </head>
  <body>
    <h1>Your random number is <%= num %></h1>
    <!-- 넘겨받은 num값이 rand라는 변수에 담겨 출력된다.-->
  </body>
</html>
```

## 조건문

ejs 템플릿 안에서 조건문을 작성할 때에느 `<% %>`를 사용한다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Random</title>
  </head>
  <body>
    <h1>Your random number is <%= num %></h1>
    <% if(num % 2 == 0) {%>
    <h2>That is an even number!</h2>
    <%} else {%>
    <h2>That is an odd number!</h2>
    <%} %>
    <!--삼항 연산자-->
    <h3>That is : <%= num % 2 == 0 ? 'EVEN' : 'ODD'%></h3>
  </body>
</html>
```

## 반복문

```js
//index.js
app.get('/cat', (req, res) => {
  const cats = ['Blue', 'Rocket', 'Monty', 'Stephany', 'Winston']
  res.render('cat', { cats })
})
```

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>All cats</title>
  </head>
  <body>
    <h1>All the cats</h1>
    <ul>
      <% for(let cat of cats){ %>
      <li><%= cat %></li>
      <% } %>
    </ul>
  </body>
</html>
```

## 데이터 사용하기

```js
//data.json
{
    "soccer": {
        "name": "Soccer",
        "subscribers": 800000,
        "description": "The football subreddit. News, results and discussion about the beautiful game.",
        "posts": [
            {
                "title": "Marten de Roon to make pizza for more than 1,000 people in Bergamo if Atalanta win the Champions league.",
                "author": "joeextreme"
            },
            {
                "title": "Stephan Lichtsteiner has retired from professional football",
                "author": "odd person"
            },
            {
                "title": "OFFICIAL: Dani Parejo signs for Villareal.",
                "author": "joeextreme"
            }
        ]
    },
}
```

```js
//index.js
const redditData = require('./data.json')
app.get('/r/:subreddit', (req, res) => {
  const { subreddit } = req.params
  const data = redditData[subreddit]
  if (data) res.render('subreddit', { ...data })
  else res.render('notfound', { subreddit })
})
```

```html
<!--subreddit.ejs-->
<body>
  <h1>Browsing the <%= name %> subreddit</h1>
  <h2><%= description %></h2>
  <p><%= subscribers %> Total Subscribers</p>
  <hr />
  <% for (let post of posts){ %>
  <article>
    <p><%= post.title %> - <b><%= post.author %></b></p>
    <% if (post.img){ %>
    <img src="<%= post.img %>" alt="" />
    <% } %>
  </article>
  <% } %>
</body>
```

## express.static()

정적 파일이란 image, css, js파일 처럼 값에 변화가 없는 파일들을 의미한다. express에서는 요청이 들어오면 정적 파일의 경로를 찾아 응답으로 간편하게 넘겨줄 수 있는 미들웨어를 제공하는데 그 미들웨어가 `express.static()`이다.

```js
// index.js
app.use(express.static('public')) //정적파일들이 위치한 디렉토리
```

이렇게 미들웨어를 사용하면 구체적인 경로를 적지 않아도 미들웨어에서 알아서 해당 경로에서 파일을 탐색해 link 한다.

```html
<!-- subreddit.ejs -->
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="/app.css" />
    <script src="/app.js"></script>
    <!--현재 css는 public/app.css 에 위치하고 있는 상태-->
    <title><%= name %></title>
  </head>
</html>
```

## component 재사용

`<%-include('경로')%>`를 사용하면 반복되는 템플릿을 컴포넌트화해 재사용할 수 있다.

```html
<!--partial/head.ejs-->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="/css/bootstrap.min.css" />
    <script src="/js/jquery.js"></script>
    <script src="/js/bootstrap.min.js"></script>
    <title>My site</title>
  </head>
</html>
```

```html
<!--subreddit.ejs-->
<%- include('partial/head.ejs') %>
  <body>
    <h1>Home page <%= 'hello world'.toUpperCase() %></h1>
    <p>
      Lorem ipsum dolor sit, amet consectetur adipisicing elit. Autem, eaque quod aspernatur alias
      similique commodi impedit placeat nemo repellendus nihil delectus in reprehenderit! Sequi iure
      reprehenderit eveniet facilis perspiciatis alias.
    </p>
    <p>
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Magni ipsum neque error iusto nemo
      facilis maiores nobis odit. Eum ab deleniti cupiditate tenetur praesentium saepe rem sapiente
      tempora fuga blanditiis?
    </p>
  </body>
</html>
```
