---
title: JavaScript_html 비표준 속성 활용하기
date: '2021-06-24'
tags: ['JavaScript']
draft: false
summary: 비표준 속성은 자바스크립트로 어떻게 제어할 수 있을까?
---

```
<!-- html -->
<body>
  <p>할 일 : <b field="title"></b></p>
  <p>담당자 : <b field="manager"></b></p>
  <p>상태 : <b field="status"></b></p>
  <div>
    상태 변경:
    <button class="btn" status="대기중">대기중</button>
    <button class="btn" status="진행중">진행중</button>
    <button class="btn" status="완료">완료</button>
  </div>
  <script src="index.js"></script>
</body>
```

```css
/*css*/
[status] {
  padding: 5px 10px;
}

[status='대기중'] {
  background-color: #ff6767;
  color: #ffffff;
}

[status='진행중'] {
  background-color: #5f62ff;
  color: #ffffff;
}

[status='완료'] {
  background-color: #07c456;
  color: #ffffff;
}
```

### 1. 선택자로 활용

> querySelector로 태그를 선택할 때 css 선택자를 활용해서 태그를 선택하는 데에 활용할 수 있다.

```jsx
const fields = document.querySelectorAll('[field]') // 괄호 안 css 선택자 활용
console.log(fields) // Nodelist(3) [b,b,b]
```

### 2. 값을 표시할 태그를 구분할 때 활용

> 비표준 속성은 객체 형태의 데이터가 있을 때, 각 프로퍼티 값들이 들어갈 태그를 구분하는데 활용할 수 있다.

```jsx
const task = {
  title: '코드 에디터 개발',
  manager: 'CastleRing, Raccoon Lee',
  status: '',
}

for (let tag of fields) {
  const field = tag.getAttribute('field')
  console.log(field) // title, manager, status
  tag.textContent = task[field]
  console.log(task[field]) // 코드 에디터 개발, CastleRing, Raccoon Lee
}

/* html filed 속성 값에 task value가 추가된다. 
<b filed="title"> 코드 에디터 개발 </b>
<b filed="manager"> CastleRing, Raccoon Lee </b>
<b filed="state"></b>
*/
```

### 3. 스타일이나 데이터 변경에 활용

> getAttribute 메소드를 활용해서 속성값을 가져오고, setAttribute 메소드를 활용해서 속성값을 설정해주는 원리로 이벤트를 통해 실시간으로 스타일을 변경하거나 데이터를 변경하는데 활용할 수 있다.

```jsx
const btns = document.querySelectorAll('.btn')
for (let btn of btns) {
  const status = btn.getAttribute('status')
  btn.onclick = function () {
    fields[2].textContent = status
    fields[2].setAttribute('status', status)
  }
}

// <b field ="status" status="대기중">대기중</b>
// <b field ="status" status="진행증">진행중</b>
// <b field ="status" status="완료">완료</b>
```
