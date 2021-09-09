---
title: JavaScript_DOM api
date: '2021-06-21'
tags: ['JavaScript']
draft: false
summary: 자바스크립트로 html을 직접 제어해보자!
---

| 메소드                                   | 의미                             | 결과                                                |
| ---------------------------------------- | -------------------------------- | --------------------------------------------------- |
| document.getElementById('id')            | HTML id속성으로 태그 선택하기    | d에 해당하는 태그 하나                              |
| document.getElementsByClassName('class') | HTML class속성으로 태그 선택하기 | class에 해당하는 태그 모음(HTMLCollection)          |
| document.getElementsByTagName('tag')     | HTML 태그 이름으로 태그 선택하기 | tag에 해당하는 태그 모음(HTMLCollection)            |
| document.querySelector('css')            | css 선택자로 태그 선택하기       | css 선택자에 해당하는 태그 중 가장 첫번째 태그 하나 |
| document.querySelectorAll('css')         | css 선택자로 태그 선택하기       | css 선택자에 해당하는 태그 모음(NodeList)           |

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>JS with Codeit</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div id="myNumber">0</div>
    <div id="btns">
      <div>
        <button id="decrease">-</button>
        <button id="increase">+</button>
      </div>
      <div>
        <button class="color-btn red" data-color="#FF0000"></button>
        <button class="color-btn orange" data-color="#FFA500"></button>
        <button class="color-btn yellow" data-color="#FFFF00"></button>
        <button class="color-btn green" data-color="#008000"></button>
        <button class="color-btn blue" data-color="#0000FF"></button>
        <button class="color-btn indigo" data-color="#4B0082"></button>
        <button class="color-btn purple" data-color="#800080"></button>
        <button class="color-btn black" data-color="#000000"></button>
      </div>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

### 1. document.getElementById('id')

> id로 태그 선택하기

```jsx
// id로 태그 선택하기
const myTag1 = document.getElementById('myNumber')
console.log(myTag1) // <div id="myNumber">0</div>

const myTag2 = document.getElementById('btns')
console.log(myTag2) //<div id="btns">...</div> 해당 태그 내부의 모든 내용이 표시된다.

const myTag3 = document.getElementById('codeit')
console.log(myTag3) //존재하지 않는 태그를 선택하게 되면 null 값이 반환된다.
```

### 2. document.getElementsByClassName('class')

> class 로 태그 선택하기

```jsx
const myTags1 = document.getElementsByClassName('color-btn')
console.log(myTags1) // HTMLCOllection(8)[....] => 유사 배열로 인덱스 데이터와 length 값을 출력할 수 있다.
console.log(myTags1[1]) // <button class="color-btn orange" data-color="#FFA500"></button> 1번 인덱스, 배열의 순서는 깊이와는 상관없이 위에서부터 차례로 출력.
console.log(myTags1.length) // 8

const myTags2 = document.getElementsByClassName('red') // 요소 하나만 출력하려고 하더라도
console.log(myTags2) // HTMLCollection [button.color-btn.red] , 처럼 콜렉션이 출력되기 때문에
console.log(myTags2[0]) // <button class="color-btn red" data-color="#FF0000"></button> , 인덱스로 접근해야한다.

const myTags3 = document.getElementsByClassName('white') // 존재하지 않는 값을 넣으면
console.log(myTags3) // HTMLCollection[] , 빈 html 콜렉션 값이 출력된다.
console.log(myTags3 === null) // false , null 값이 출력되지 않는다는 점이 getElementById 와 다르다.
console.log(myTags3.length) // 0

const allTags = document.getElementsByTagName('*') // css 선택자처럼 '*'값을 전달하게 되면 모든 태그를 선택할 수 있다.
```

**유사배열의 특징**

1. 숫자 형태의 indexing이 가능하다.
2. length 프로퍼티가 있다.
3. 배열의 기본 메소드를 사용할 수 없다.
4. Array.isArray(유사배열)은 false다.

주의! 유사 배열은 다양한 형태로 존재할 수 있다. 예를 들어 HTMLCollection의 경우 for..of 문을 활용하는 데 문제는 없었지만 어떤 유사 배열의 경우에는 for..of문을 활용할 수 없는 경우도 있다.

### 3. document.querySelector('css') 와 document.querySelectorAll('css')

> css 선택자로 태그 선택하기

```jsx
const myTag4 = document.querySelector('#myNumber') // .getElementById('myNumber')와 동일
console.log(myTag4) // <div id="myNumber">0</div>

const myTag5 = document.getElementById('myNumber')
console.log(myTag5) // <div id="myNumber">0</div>

const myTag6 = document.querySelector('.color-btn') // .color-btn 클래스 중 첫번째 태그가 선택된다.
console.log(myTag6) // <button class="color-btn red" data-color="#FF0000"></button>

const myTags4 = document.querySelectorAll('.color-btn') // .color-btn 클래스를 가진 모든 태그를 Nodelist 라는 유사배열로 반환한다.
console.log(myTags4) // Nodelist(8) [.....]

const myTags5 = document.getElementsByClassName('color-btn')
console.log(myTags5) // HTMLCOllection(8)[....]
```

querySelector는 getElementById와 동일하게 존재하지 않는 요소를 선택할 시 null 값을 반환한다.
그러나, querySelectorAll은 존재하지 않는 요소를 선택할 경우, 비어있는 Nodelist가 리턴된다.
