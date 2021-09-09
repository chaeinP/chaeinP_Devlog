---
title: React_JSX 규칙
date: '2021-07-09'
tags: ['JSX']
draft: false
summary: 자바스크립트도 html도 아닌 JSX만의 규칙!
---

리액트에는 가상 돔 조작을 편리하게하기 위한 JSX 엘리먼트가 존재한다. JSX 의 생김새는 HTML과 유사하게 생겼으나 Babel을 통해 자바스크립트가 해석할 수 있는 언어로 변환해야하기 때문에 주어진 규칙을 준수해야한다.

#### 1. 태그는 무조건 닫혀있어야 한다.

닫힌 태그를 반복해서 쓸 필요가 없는 경우 self closing 태그를 사용해서라도 태그를 닫아야한다.

```jsx
<div>
  <input /> -------------> self closing을 통해 닫아준 경우
</div>
```

#### 2. 2개 이상의 태그는 꼭 하나의 태그로 감싸져 있어야 한다.

```jsx
<>  ------------> 빈태그로 hello 컴포넌트를 감싸고 있는 모습
  <Hello />
  <Hello />
</>

return ( ------> 소괄호는 있어도 되고 없어도 된다. 가독성을 위해서만 사용할 뿐
  <>
    <Hello />
    <Hello />
  </>
)
```

#### 3. jsx 내부에 js 표현식을 사용해야할 때는 중괄호 안에 작성한다.

```jsx
const name = 'react';
<>
  <Hello />
  <div>name</div> ----> 라고 사용하면 name이 문자 그대로 브라우저에 표시됨
</>

<>
  <Hello />
  <div> { name } </div> ----> 중괄호로 감싸주어야 변수로 기능함
</>
```

#### 4. jsx 엘리먼트에 style 을 추가할 때에는 스타일 객체가 따로 필요하다.

```jsx
 const style = {
    backgroundColor : 'black', -----> css에서 하이픈으로 연결되어있는 스타일 속성들은 (ex.background-color) camelCase로 작성한다.
    color: 'aqua'
    fontSize: 24 ----> 숫자로 표시하면 px단위로 처리된다.
    padding: '1rem' ------> 픽셀이 아닌 단위를 사용하기 위해선 문자열로 표기해야한다.
  }

  <>
    <Hello />
    <div style = { style }> { name } </div>
  </>
```

#### 5. html 클래스는 className로 사용한다. class로 사용할시 에러가 발생한다.

```jsx
<>
  <Hello />
  <div className="REACT" style={style}>
    {' '}
    {name}{' '}
  </div>
</>
```

#### 6. 주석 기호는 중괄호 안에 작성한다.

```jsx
 {/*어쩌고저쩌고*/}
  <div //어쩌고저쩌고 --------> 태그 안에 주석을 작성할 때에는 중괄호로 감싸지 않아도 된다.
  >
```
