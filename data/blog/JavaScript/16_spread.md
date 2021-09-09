---
title: JavaScript_Spread 구문
date: '2021-06-25'
tags: ['JavaScript']
draft: false
summary: 익숙해지면 너어무 편리한 자바스크립트 스프레드 구문!
---

### Spread

> 여러 개의 값을 묶어놓은 배열이나 객체와 같은 값은 바로 앞에 마침표 세 개를 붙여서 펼칠 수 있다.

```jsx
const numbers = [1, 2, 3]

console.log(...numbers) // 1 2 3
console.log(1, 2, 3) // 1 2 3
```

spread 구문의 모양은 앞서 배운 rest parameter의 모습과 흡사하다. 하지만 rest parameter는 여러개의 아규먼트를 하나의 파라미터에 묶는 방식이고 spread는 하나에 묶여 있는 값을 개별 값으로 펼치는 방식으로 둘은 엄연히 다르다.

#### 1. 복사에 사용되는 spread 구문

배열은 참조형 데이터로 주소값을 할당하기 때문에 주소값을 공유하는 두 변수 모두 값이 변한다.

```jsx
const webPublishing = ['HTML', 'CSS']
const interactiveWeb = webPublishing

interactiveWeb.push('JavaScript')

console.log(webPublishing) // ['HTML', 'CSS', 'JavaScript']
console.log(interactiveWeb) // ['HTML', 'CSS', 'JavaScript']
```

spread 구문을 사용하면 **복사가 가능**하다.

```jsx
const webPublishing = ['HTML', 'CSS']
const interactiveWeb = webPublishing.slice() // 복사

interactiveWeb.push('JavaScript')

console.log(webPublishing) // ['HTML', 'CSS']
console.log(interactiveWeb) // ['HTML', 'CSS', 'JavaScript']
```

spread 구문을 이용해 **복사와 요소추가를 동시에** 할 수 있다.

```jsx
const webPublishing = ['HTML', 'CSS']
const interactiveWeb = [...webPublishing, 'JavaScript'] // 복사와 요소추가를 동시에

console.log(webPublishing) // ['HTML', 'CSS']
console.log(interactiveWeb) // ['HTML', 'CSS', 'JavaScript']
```

#### 2. spread 구문을 활용한 배열 합치기

```jsx
const arr1 = [1, 2, 3]
const arr2 = [4, 5, 6]

const arr3 = [...arr1, ...arr2]
console.log(arr3) // [1,2,3,4,5,6]

// concat() 메소드를 사용했을 때와 같은 결과값을 가진다.
const arr4 = arr1.concat(arr2)
console.log(arr4) // [1,2,3,4,5,6]
```

#### 3. 함수를 호출할 때 argument로 사용되는 spread 배열

```jsx
const introduce = (name, birth, job) => {
  console.log('안녕하세요! 반갑습니다!')
  console.log(`저는 ${name}입니다.`)
  console.log(`${birth}년 생이고,`)
  console.log(`직업은 ${job}입니다.`)
  console.log(`잘 부탁드립니다 :)`)
}

const myArr = ['코드잇', 2017, '프로그래밍 강사']
introduce(...myArr)
```

#### 4. 객체와 spread 구문

```jsx
const members = ['태호', '종훈', '우재']
const newObject = { ...members }
console.log(newObject) // {0:"태호", 1:"종훈", 2:"우재"}
```

#### 5. spread를 이용한 객체 복사

```jsx
const codeit = {
  name: 'codeit',
}

const codeitClone = {
  ...codeit, //spread문법
}

console.log(codeit) // {name: 'codeit'}
console.log(codeitClone) // {name: 'codeit'}
```

#### 6. spread를 이용한 객체 프로퍼티 복사

```jsx
const latte = {
  espresson: '30ml',
  milk: '150ml',
}

const cafeMocha = {
  ...latte,
  chocolate: '20ml',
}

console.log(latte) // {esspresso: '30ml', milk:'150ml'}
console.log(cafeMocha) // {esspresso: '30ml', milk:'150ml', chocolate: '20ml'}
```

객체 Spread 구문을 사용해서 새로운 배열을 만든다거나 함수의 아규먼트로 사용할 수는 없다.
그렇기 때문에 객체를 spread할 때는 반드시 객체를 표현하는 중괄호 안에서 활용해야 한다.

```jsx
const latte = {
  esspresso: '30ml',
  milk: '150ml'
};

const cafeMocha = {
  ...latte,
  chocolate: '20ml',
}

[...latte]; // Error

(function (...args) {
  for (const arg of args) {
    console.log(arg);
  }
})(...cafeMocha); // Error
```
