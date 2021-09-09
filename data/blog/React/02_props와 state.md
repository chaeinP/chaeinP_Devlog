---
title: React_props와 state
date: '2021-07-09'
tags: ['JSX']
draft: false
summary: 리액트 데이터 흐름의 핵심, props와 state(reach hook 등장 이전을 중심으로)
---

_여기서는 react hook 이전의 문법들을 주로 다룬다. _

> `props`는 컴포넌트 외부에서 컴포넌트에 전달되는 데이터이고 `state`는 컴포넌트 내부에서 전달되는 데이터로 컴포넌트 내에서 변경이 가능하다. 컴포넌트에 전달되는 `props`와 `state`값에 변경이 발생하면 랜더 메소드가 자동으로 실행되어 브라우저 상태를 업데이트 한다.

### 1. props 사용법

(1) 함수형 컴포넌트와 props

함수형 컴포넌트에 props를 인자로 전달한다.

```jsx
import logo from './logo.svg';
import './App.css';
import React from 'react';

function App() {
  return (
    <div>
      <Component message = "안녕하세요!"/> {/* Component 함수 외부에서 들어오는 props 값*/}
    </div>
  );
}

//함수형 컴포넌트
function Component(props){----->{/* App 컴포넌트 props 객체 생성 {message : "안녕하세요!"} */}
  return (
    <div>
      <h1>{props.message} 이것은 함수로 만든 컴포넌트입니다. </h1>
    </div>
  )
}

export default App;
```

(2) 클래스 컴포넌트와 props

클래스 컴포넌트에서 props를 사용할 때에는 this.로 지정해주어야한다.

```jsx
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
        <p>{this.state.count}</p>
      </div>
    )
  }
}
```

### 2. props 기본값 설정

외부 컴포넌트에서 props 값이 지정되지 않으면 props 값은 undefined가 되어 화면에 출력되지 않는다. 이 때 props에 기본값을 설정해주면 특정한 값이 지정되기 전까지 기본값이 출력된다.

(1) 클래스, 함수 공통 기본값 설정방법

> `컴포넌트이름.defaultProps`를 사용하면 props의 기본값을 설정할 수 있다.

```jsx
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
      </div>
    )
  }
}

// 함수와 클래스에서 모두 사용 가능
Component.defaultProps = {
  message: '기본값',
}
```

(2) 클래스만의 기본값 설정방법

> `static defaultProps`를 사용하면 클래스 내부에서 props의 기본값 설정이 가능하다.

```jsx
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
      </div>
    )
  }
  //클래스에서 props 기본값 설정하는 방법
  static defaultProps = {
    message: '기본값',
  }
}
```

### 3. state 사용법

react hook이 나오기 이전 state는 클래스 컴포넌트 내부에서만 정의될 수 있었다.

(1) state 정의하는 법 1

```jsx
class Component extends React.Component {
  state = {
    count: 0,
  }

  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
        <p>{this.state.count}</p>
      </div>
    )
  }
}
```

(2) state 정의하는 법 2

```jsx
class Component extends React.Component {
  constructor(props) {
    super(props)
    this.state = { count: 0 }
  }

  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
        <p>{this.state.count}</p>
      </div>
    )
  }
}
```

### 4. state 값 변경(setState()를 이용하여)

> setState메소드를 이용하면 state값이 바뀔 때마다 렌더메소드를 자동으로 호출한다.

(1) setState 사용법 1

```jsx
class Component extends React.Component {

  state = {
    count: 0,
  }

  render(){
    return(
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
        <p>{this.state.count}</p>
      </div>
    )
  }

  componentDidMount(){
    setTimeout(() => {
      //setState 사용법 1 : 객체를 매번 다시 생성
      this.setState({
        count: this.state.count + 1;
      })
   	},1000)
  }
}

```

(2) setState 사용법 2

```jsx
class Component extends React.Component {
  state = {
    count: 0,
  }

  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다. </h1>
        <p>{this.state.count}</p>
      </div>
    )
  }

  componentDidMount() {
    setTimeout(() => {
      //setState 사용법 2 : 이전 state 값 사용
      this.setState((previousState) => {
        const newState = { count: previousState.count + 1 }
        return newState
      })
    }, 1000)
  }
}
```
