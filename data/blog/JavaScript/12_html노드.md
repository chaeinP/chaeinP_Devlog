---
title: JavaScript_html 노드 추가와 제거
date: '2021-06-23'
tags: ['JavaScript']
draft: false
summary: 자바스크립트로 html 요소 추가/제거하기!
---

```

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>JS with Codeit</title>
</head>
<body>
  <div>
    <h1>오늘 할 일</h1>
		<ol id="today">
			<li>자바스크립트 공부</li>
			<li>고양이 화장실 청소</li>
			<li>고양이 장난감 쇼핑</li>
		</ol>
		<h1>내일 할 일</h1>
		<ol id="tomorrow">
			<li>자바스크립트 공부</li>
			<li>뮤지컬 공연 예매</li>
			<li>유튜브 시청</li>
		</ol>
  </div>
  <script src="index.js"></script>
</body>
</html>
```

```jsx
const today = document.querySelector('#today')
```

### 1. 요소 추가

> innerHTML 과 outerHTML 모두 새로운 데이터를 추가하면 기존 값을 덮어쓰기 때문에 데이터 보존을 위해선 기존 데이터를 추가해야한다.

```jsx
//innerHTML
today.innerHTML = '<li>처음</li>' + today.innerHTML
today.innerHTML = today.innerHTML + '<li>마지막</li>'
//outerHTML
today.outerHTML = '<p>이전</p>' + today.outerHTML
```

특히 outerHTML의 경우 프로퍼티를 수정할 때마다 새로운 요소가 되기 때문에 그 이후에 그 요소를 다시 다루려면 querySelector로 계속 찾아줘야 된다는 번거로움이 있다.

```jsx
const newToday = document.querySelector('#today')
newToday.outerHTML = newToday.outerHTML + '<p>다음</p>'
```

</br>

**요소 노드 추가하기**

```jsx
const tomorrow = document.querySelector('#tomorrow')
```

1. 요소 노드 만들기: document.createElement('태그이름')
2. 요소 노드 꾸미기: textContent, innerHTML, ...
3. 요소 노드 추가하기: Node.prepend, .append, .after, .before

```jsx
// 호출한 메소드의 자식 노드 첫번째로 추가
const first = document.createElement('li')
first.textContent = '처음'
tomorrow.prepend(first)

// 호출한 메소드의 자식 노드 마지막으로 추가
const last = document.createElement('li')
last.textContent = '마지막'
tomorrow.append(last)

// 호출한 메소드의 이전 형제 노드로 추가
const prev = document.createElement('p')
prev.textContent = '이전'
tomorrow.before(prev)

// 호출한 메소드의 다음 형제 노드로 추가
const next = document.createElement('p')
next.textContent = '다음'
tomorrow.after(next)
```

위 메소드들은 요소노드를 추가할 뿐만 아니라 그냥 문자열을 넣어도 텍스트 노드로 추가한다. _ex) tomorrow.before('문자열')_
추가할 때 여러개의 노드를 동시에 전달할 수도 있다.
_ex) tomorrow.before(prev,last,'이전')_
여러개를 동시에 추가할 때는 전달한 순서 그대로 추가 된다.

### 2. 요소 삭제와 이동

**노드 삭제하기 Node.remove()**

```jsx
tomorrow.remove() //<ol id="tomorrow">...</ol> 전체 삭제
today.children[2].remove() //<ol id="today"></ol>의 세번째 자식 요소 삭제
```

**노드 이동하기 prepend, append, before, after**

```jsx
today.append(tomorrow.children[1]) // 내일 할 일의 2.뮤지컬 공연 예매를 오늘 할 일 리스트 마지막으로 이동
tomorrow.children[1].after(today.children[1]) // 오늘 할 일의 2. 고양이 화장실 청소를 내일 할일의 3번째 요소로 추가
tomorrow.children[2].before(today.children[1]) // 결과는 윗 문장과 같다.
tomorrow.lastElementChild.before(today.children[1]) // 결과는 윗 문장과 같다.
```
