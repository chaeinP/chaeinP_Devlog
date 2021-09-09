---
title: JavaScript_DOM 이란?
date: '2021-06-22'
tags: ['JavaScript']
draft: false
summary: 자바스크립트 DOM 파헤쳐보자!
---

```jsx

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>JS with Codeit</title>
</head>
<body>
  <h1 id="title">DOM(Document Object Model)</h1>
  <h2 id="sub-title">문서 객체 모델</h2>
  <script src="index.js"></script>
</body>
</html>
```

### 1. window 객체

> 웹브라우저는 객체로 이루어져있다. window 객체 안에 document 라는 객체도 있고 console이라는 객체도 있다. window 객체는 브라우저의 창을 대변하면서 자바스크립트 최상단에 존재하는 객체이기도 하다. 여기서 최상단이란 window 객체가 javascript 모든 객체를 포함하고 있다는 뜻이다. 이 window 객체는 자바스크립트 어느 곳에서나 접근할 수 있는 개체로 전역객체에 해당한다.

```jsx
console.log(window) // window 객체 출력, 자바스크립트로 객체 안 메소드를 활용하면 웹브라우저를 조작할 수 있다.
console.log(window.innerWidth) // 창의 너비 출력
console.log(window.innerHeight) // 창의 높이 출력

/* window. */
window.console.log(window)
window.console.log(window.innerWidth)
window.console.log(window.innerHeight)
// console.log도 원칙적으로는 윈도우 객체의 내부 객체로
// window.을 붙여야하지만 우리가 어떤 객체를 사용하든
// 모든 객체는 window 객체안에 포함되어 있기 때문에
// 따로 window.을 언급하지 않아도 된다.
```

### 2. DOM(Document Object Model) : 문서 객체 모델

> 웹 페이지에 나타나는 HTML 문서 전체를 객체로 표현한 것

```jsx
const title = document.querySelector('#title') //<h1 id="title">DOM(Document Object Model)</h1>
title.style.color = 'red' // title이 빨간색으로 변함
```

### 3. console.log vs console.dir

> ** 1. 출력하는 자료형이 다르다. **
> : dir 메소드는 문자열 표시 형식으로 콘솔에 출력한다.

> **2. log는 값 자체를, dir은 객체의 속성을**
> : log 메소드는 파라미터로 전달받은 값을 위주로 출력하는 반면, dir메소드는 객체의 속성을 좀 더 자세하게 출력한다.
> dir 메소드는 객체의 유형이 먼저 출력되고, 특히 함수 부분에서는 클릭해서 펼쳤을 때 함수가 가진 속성들을 더 보여준다.

> **3. log는 여러개, dir은 하나만**
> : log 메소드는 여러 값을 쉼표로 구분해서 전달하면 전달받은 모든 값을 출력하는 반면, dir 메소드는 여러 값을 전달하더라도 첫번쨰 값만 출력한다.

> **4. console.log(document) vs console.dir(document)**
> : log 메소드는 대상을 HTML 형태로 출력하고, 객체의 속성에 좀 더 중점을 둔 dir 메소드는 대상을 객체 형태로 출력한다.

```jsx
console.log(document) //객체가 아닌 html 출력
console.log(typeof document) // object
console.dir(document) //객체형태로 다양한 프로퍼티를 확인할 수 있음
```
