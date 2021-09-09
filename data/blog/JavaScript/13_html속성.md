---
title: JavaScript_html 속성 다루기
date: '2021-06-23'
tags: ['JavaScript']
draft: false
summary: 자바스크립트로 html 속성 다루는 법은 따로있다!
---

웹 브라우저가 html 을 해석할 때 dom 객체가 만들어지는데 이 과정에서 대부분의 속성들은 가지고 있는 이름 그대로 요소 노드의 프로퍼티가 된다.그러나 모든 html 속성이 요소 노드의 프로퍼티로 생성되지는 않는다.
이 경우 일반적인 방법으로는 해당 속성에 접근할 수가 없다.

```
//html
<body>
  <div>
    <h1>오늘 할 일</h1>
    <ol id="today">
      <li class="item">자바스크립트 공부</li>
      <li class="item">고양이 화장실 청소</li>
      <li class="item">고양이 장난감 쇼핑</li>
     </ol>
     <h1>내일 할 일</h1>
     <ol id="tomorrow" href="https://www.codeit.kr">
     	<li class="item"><a href="https://developer.mozilla.org/ko/docs/Web/JavaScript">자바스크립트 공부</a></li>
	<li class="item">뮤지컬 공연 예매</li>
	<li class="item">유튜브 시청</li>
      </ol>
  </div>
  <script src="index.js"></script>
</body>
```

### 1. HTML 표준 속성 (HTML attribute)

```jsx
const tomorrow = document.querySelector('#tomorrow')
const item = tomorrow.firstElementChild
const link = item.firstElementChild

// ID 속성
console.log(tomorrow) //<ol id="tomorrow">...</ol>
console.log(tomorrow.id) // tomorrow

// class 속성
console.log(item) //<li class="item">...</li>
console.log(item.className) // item, html class 속성은 className이라는 프로퍼티로 생성된다.

// href 속성
console.log(link) // <a href="https://www.codeit.kr">자바스크립트 공부</a>
console.log(link.href) // "https://developer.mozilla.org/ko/docs/Web/JavaScript"
```

> <ol> 태그에 href 속성을 추가하는 건 html 표준이 아니다.

```jsx
console.log(tomorrow.href) // undefined , 표준 속성이 아닌 경우 undefined 출력
```

### 2. HTML 표준 속성이 아닌 속성에 접근하는 법

**(1) elem.getAttribute('속성')**

> 이 속성을 사용하면 표준과 비표준에 상관없이 모든 속성에 접근 가능하다.

```jsx
console.log(tomorrow.getAttribute('href'))
console.log(item.getAttribute('class'))
//class는 class 속성으로 접근할 땐 className이지만 getAttribute 속성으로 접근할 떄에는 class 이다.
```

**(2) elem.setAttribute('속성', '값')**

> 속성 추가(수정)하기, 기존에 없던 속성은 추가가 되고 기존에 존재하는 속성은 수정되는 형태로 동작한다.

```jsx
tomorrow.setAttribute('class', 'list') // 추가
link.setAttribute('href', 'https://www.codeit.kr') // 수정
```

**(3) elem.removeAttribute('속성')**

> 속성 제거하기

```jsx
tomorrow.removeAttribute('href')
tomorrow.removeAttribute('class')
```

### 3. 스타일 다루기

**(1) .style.스타일요소**

스타일 프로퍼티를 이용할 때에는 적용할 스타일을 camelCase로 작성해야 한다.
ex)text-decoration -> textDecoration

```jsx
const today = document.querySelector('#today')

today.children[0].style.textDecoration = 'line-through'
today.children[0].style.backgroundColor = '#DDDDDD'
today.children[2].style.textDecoration = 'line-through'
today.children[2].style.backgroundColor = '#DDDDDD'
```

> style 프로퍼티를 활용하면 html 태그에 인라인으로 직접 들어가기 때문에 스타일 우선순위가 높아져 다른 스타일에 비해 우선적으로 적용되고 같은 스타일을 다른 여러태그에 적용해야 될 때에는 비슷한 코드를 불필요하게 많이 작성해야하는 문제도 있다. 따라서 자바스크립트를 이용해 스타일을 입힐 때에는 스타일 프로퍼티를 이용하는 것보다 태그에 클래스를 입히는 방식을 권장한다.

**(2) .elem.className**

```jsx
today.children[1].className = 'done' // css 에서 done 클래스에 미리 입혀둔 스타일이 실행된다.

//이 경우 html 클래스 속성 값 전체가 바뀐다.
//<li class ="item"></li> 가 사라지고  <li class ="done"></li> 으로 교체된다.
```

**(3) elem.classList**

> 클래스 속성값을 유사배열로 다루는 프로퍼티. 원래 있던 item 클래스를 그대로 두고 done 클래스를 추가, 제거하고 싶다면 이 프로퍼티를 사용한다.

```jsx
const item1 = tomorrow.children[1]

item1.classList.add('done') // <li class ="item done"></li>클래스 추가
// .classList.add('done','other') 여러새 순서대로 추가 가능, 똑같은 클래스를 중복으로 추가하면 하나만 추가된다.

item1.classList.remove('done')
// 클래스 제거. add와 마찬가지로 여러개의 클래스를 전달하면 여러개를 동시에 삭제할 수 있다.

item1.classList.toggle('done') // 토글은 있으면 제거, 없으면 추가한다.
//add 나 remove 처럼 여러개의 파라미터를 전달한다고 동시에 실행되지는 않는다.
// 대신 두번째 파라미터로 불린형태를 전달하여 true일때는 add 기능 강제, false일때는 remove 기능을 강제한다. 자주 사용되지는 않는다.
```
