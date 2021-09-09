---
title: JavaScript_DOM Tree란?
date: '2021-06-22'
tags: ['JavaScript']
draft: false
summary: 자바스크립트로 DOM을 돌아다녀보자!
---

```
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>JS with Codeit</title>
</head>
<body>
  <div id="content">
    <h2 id="title-1">Cat-1</h1>
    <ul id="list-1">
      <li>Ragdoll</li>
      <li>British Shorthair</li>
      <li>Scottish Fold</li>
      <li>Bengal</li>
      <li>Siamese</li>
      <li>Maine Coon</li>
      <li>American Shorthair</li>
      <li>Russian Blue</li>
    </ul>
    <h2 id="title-2">Cat-2</h1>
    <ul id="list-2">
      <li>Sphynx</li>
      <li>Munchkin</li>
      <li>Persian</li>
      <li>Norwegian Forset</li>
      <li>Turkish Angora</li>
      <li>Bombay</li>
      <li>Selkirk Rex</li>
      <li>Munchkin</li>
    </ul>
  </div>
  <script src="index.js"></script>
</body>
</html>
```

```javascript
DOM 트리 여행하기
const myTag = document.querySelector('#list-1');
console.log(myTag); // <ul id="list-1">...</ul>
```

### 1. 형제 요소 노드로 이동

> .previousElementSibling
> .nextElementSibling

```jsx
console.log(myTag.previousElementSibling)
// <h2 id="title-1">Cat-1</h1>, 이전 형제 요소, 요소가 없으면 null 출력
console.log(myTag.nextElementSibling)
// <h2 id="title-2">Cat-2</h1>, 다음 형제 요소, 요소가 없으면 null
```

### 2. 부모 요소 노드로 이동

> .parentElement

```jsx
console.log(myTag.parentElement)
// <div id="content">... </div> 해당 태그의 부모요소
```

### 3. 자식 요소 노드로 이동

> .children[index]
> .firstElementChild
> .lastElementChild

```jsx
console.log(myTag.children[1])
// <li>British Shorthair</li> 해당 요소의 두번째 자식요소
console.log(myTag.firstElementChild)
// <li>Ragdoll</li> 해당 요소의 첫번째 자식 요소
console.log(myTag.lastElementChild)
//  <li>Russian Blue</li> 해당 요소의 두번째 자식 요소
```

### 4. 복합

```jsx
console.log(myTag.parentElement.nextElementSibling)
// <script src="index.js"></script>
```

### 5.innerHTML

> innerHTML은 요소안의 값을 확인할 때보단 수정할 때 더 많이 사용한다.

```jsx
// innerHTML
console.log(myTag.innerHTML)
/*    <li>Ragdoll</li>
      <li>British Shorthair</li>
      <li>Scottish Fold</li>
      <li>Bengal</li>
      <li>Siamese</li>
      <li>Maine Coon</li>
      <li>American Shorthair</li>
      <li>Russian Blue</li> */
// 들여쓰기와 줄바꿈 모두 반영되어 출력
```

```jsx
myTag.innerHTML = '<li>Exotic</li>'
// 선택된 요소의 자식 요소가 요소가 이값으로 교체된다.
myTag.innerHTML += '<li>Exotic</li>'
// 기존 HTML 마지막 부분에 요소를 추가하는 식
```

### 6.outerHTML

> innerHTML 과 같이 줄바꿈이나 들여쓰기 모두 포함되어 출력된다.

```jsx
console.log(myTag.outerHTML)
/*<ul id="list-1">
      <li>Ragdoll</li>
      <li>British Shorthair</li>
      <li>Scottish Fold</li>
      <li>Bengal</li>
      <li>Siamese</li>
      <li>Maine Coon</li>
      <li>American Shorthair</li>
      <li>Russian Blue</li>
    </ul>*/
```

```jsx
myTag.outerHTML = '<ul id ="new-liest"><li>Exotic </li></ul>'
// 선택된 요소가 완전히 사라지고 새 값으로 교체된다.
```

### 7. textContent

> 요소안 내용들 중 html 을 제외한 텍스트만 출력한다.

```jsx
/*
      Ragdoll
      British Shorthair
      Scottish Fold
      Bengal
      Siamese
      Maine Coon
      American Shorthair
      Russian Blue
*/
```

```jsx
myTag.textContent = 'new text!'
// innerHTML과 같에 요소 안 자식 요소의 내용이 완전히 교체된다. textContent는 모든 값을 문자열로 반환하기 때문에 html 특수문자도 코드 그 자체로 문자열로 수정된다.
```
