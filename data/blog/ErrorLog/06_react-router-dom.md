---
title: Error Log_react-router-dom Route에 props 전달하는법
date: '2021-09-28'
tags: ['Error_Log'],['Project']
draft: false
summary: utterence github OAUTH는 canonical url을 중심으로 작동한다.
---

> 이 포스트는 토이프로젝트 'signup/signin 간단 구현' 에서 발생한 에러를 기록한 것입니다.

### 문제상황

`react-router-dom`으로 라우팅 설정을 할때 option으로 component를 주로 사용했는데 이 경우 어떻게 props를 전달해야하는지 난감했다. 야심차레 component 옵션 뒤에 props를 추가해보았지만 동작하지 않았다.

```js
//App.js
function App(){
  return (
    <BrowserRouter>
      <MainTemplate>
      {isLogin ?
        <Route exact path="/" component = {Home} logoutHandler={logoutHandler}/> // 동작 안함
        : <Route exact path="/" component = {Login}/>
      }
      <MainTemplate/>
    <BrowserRouter/>)
}
```

### 해결

> props를 내려주어야 할때는 render 옵션을 사용한다.

```js
//App.js
function App(){
  return (
    <BrowserRouter>
      <MainTemplate>
        <Route
            exact
            path="/"
            render={() =>
              login.isLogin ? (
                <Home logoutHandler={logoutHandler} />
              ) : (
                <Login loginHandler={loginHandler} />
              )
            }
          />
      <MainTemplate/>
    <BrowserRouter/>)
}
```

렌더 옵션으로는 함수를 사용하고 위와 같이 적으면 컴포넌트에 props를 전달하는 모습 그대로 라우팅을 지정할 수 있다.
