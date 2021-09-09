---
title: Network_JSON
date: '2021-07-30'
tags: ['NETWORK']
draft: false
summary: JSON 객체란?
---

### 01. json

> JSON은 JavaScript Object Notation의 줄임말로 **정보를 교환할 때 사용되는 데이터 포맷**으로 자바스크립트 문법을 빌린 형태이다. 그렇다고 해서 자바스크립트와 JSON이 완벽히 동일한 것은 아니다. 또한, JSON은 텍스트 기반이므로 어떠한 프로그래밍 언어에서도 JSON 데이터를 읽고 사용할 수 있다. JSON은 하나의 객체에 데이터가 나열되어있는 형식을 띠고 있으며 **자료형 타입은 문자열**이다.

```js
fetch('https://jsonplaceholder.typicode.com/users')
  .then((response) => response.text())
  .then((result) => {
    console.log(typeof result) //string
  })
```

#### JSON과 XML

> JSON은 XML의 대안으로서 좀 더 쉽게 데이터를 교환하고 저장하기 위하여 고안되었다.

**XML이란?** XML은 EXtensible Markup Language의 약자로 HTML과 매우 비슷한 문자 기반의 마크업 언어(text-based markup language)이다. XML은 시작 태그와 종료 태그로 데이터를 감싸는 형태를 하고 있으며 배열 사용이 불가해 계층 구조를 효과적으로 보여주기 어렵고 json에 비해 큰 용량을 가지고 있어 파싱 속도도 느리다는 단점이 있다.

**JSON과 XML의 공통점**

1. 둘 다 데이터를 저장하고 전달하기 위해 고안된었다.
2. 둘 다 기계뿐만 아니라 사람도 쉽게 읽을 수 있다.
3. 둘 다 계층적인 데이터 구조를 가진다.
4. 둘 다 다양한 프로그래밍 언어에 의해 파싱될 수 있다.
5. 둘 다 XMLHttpRequest 객체를 이용하여 서버로부터 데이터를 전송받을 수 있다.

**JSON과 XML의 차이점**

1. JSON은 태그를 사용하지 않는다.
2. JSON의 구문이 XML의 구문보다 더 짧다.
3. JSON 데이터가 XML 데이터보다 더 빨리 읽고 쓸 수 있다.
4. XML은 배열을 사용할 수 없지만, JSON은 배열을 사용할 수 있다.
5. XML은 XML 파서로 파싱되며, JSON은 JSON.parse()로 파싱된다.

```jsx
//XML 예제
<dog>
    <name>식빵</name>
    <family>웰시코기<family>
    <age>1</age>
    <weight>2.14</weight>
</dog>

//JSON 예제
{
  "name": "식빵",
  "family": "웰시코기",
  "age": 1,
  "weight": 2.14
}
```

JSON은 XML의 많은 단점을 보완하며 웹 API 데이터 형식의 주류로 떠올랐지만 JSON이 XML에 비해 마냥 좋다고 이야기할 수는 없다. 특히 데이터의 무결성 부분에 있어 XML은 JSON에 비교할 수 없을 정도로 뛰어난 강점을 지닌다. [데이터 무결성이란](https://coding-factory.tistory.com/221) 쉽게 말해 항상 정확한 데이터 값을 유지하고 있다는 것을 의미한다. XML이 갖는 데이터 무결성 강점에 대해선 다음의 링크를 참조할 것.

[**XML VS JSON : 웹 API를 위한 더 나은 선택**](https://www.coovil.net/xml-vs-json/)

### 02. json과 자바스크립트 문법 차이

**(1) 프로퍼티 이름과 값 표현**

- json 에서는 각 프로퍼티의 이름을 반드시 "큰따옴표"로 감싸줘야 한다.

```js
// 큰따옴표만 지원하는 json
const member = {
  name: 'Michael Kim',
  height: 180,
  weight: 70,
  hobbies: ['Basketball', 'Listening to music'],
}

// 자바스크립트는 키를 표현할 때 특수한 경우를 제외하고는 큰따옴표를 사용하지 않아도 된다.
const member = {
  name: 'Michael Kim',
  height: 180,
  weight: 70,
  hobbies: ['Basketball', 'Listening to music'],
}
```

- 자바스크립트에서 문자열은 작은 따옴표와 큰 따옴표 모두 지원하지만 JSON의 경우 문자열은 반드시 큰따옴표만 사용해야한다.

```js
// json은 문자열을 표현할 때 큰따옴표만
const member = {
  name: 'Michael Kim',
}

// 자바스크립트는 큰따옴표, 작은 따옴표 모두 지원한다.
const member = {
  name: 'Michael Kim',
  job: 'developer',
}
```

- json은 프로퍼티의 값으로 undefined, NaN, Infinity 등을 사용할 수 없다.
- 주석을 추가할 수 없다.

### 03. 직렬화와 역직렬화

> string 타입의 JSON 데이터는 자바스크립트의 객체 형태로 변환해야 사용하기 용이하다. `JSON.parse()`를 사용하면 string타입의 JSON을 자바스크립트 객체 형태로 변환할 수 있다. string 타입의 JSON 데이터를 실제 자바스크립트 객체로 변환하는 것을 Deserialization, 우리말로는 역직렬화라고 한다. 반대로 자바스크립트 객체를 JSON 타입의 string으로 변환하는 메소드는 `JSON.stringify()`로 이를 직렬화라고 한다.

```js
// json.parse() 역직렬화
fetch('https://jsonplaceholder.typicode.com/users')
  .then((response) => response.text())
  .then((result) => {
    const users = JSON.parse(result) // JSON 타입을 자바스크립트 객체로 변환
    console.log(users.length) //10
    users.forEach((user) => {
      console.log(user.name) // 10명의 사용자 이름 출력
    })
  })

// json.stringify() 직렬화
const member = {
  name: 'Alice',
  email: 'alice@codeitmail.kr',
  department: 'marketing',
}

fetch('https://learn.codeit.kr/api/members/2', {
  method: 'PUT',
  body: JSON.stringify(member), // 자바스크립트 객체를 JSON 타입으로 변환
})
  .then((response) => response.text())
  .then((result) => {
    console.log(result)
  })
```
