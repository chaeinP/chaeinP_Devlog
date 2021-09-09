---
title: JavaScript_async와 await
date: '2021-08-04'
tags: ['JavaScript']
draft: false
summary: 비동기 가독성을 높이는 async와 await
---

> 프로미스 객체를 다루는 더 간편한 방법으로 `async`와 `await`가 있다. `async` `await`은 then 메소드나 콜백을 사용하지 않고도 비동기 실행 코드를 작성할 수 있으며 마치 동기 실행 코드처럼 코드를 훨씬 더 간단하고 편하게 작성할 수 있다.

```js
fetch('https://jsonplaceholder.typicode.com/users')
  .then((response) => response.text())
  .then((result) => {
    console.log(result)
  })

async function fetchAndPrint() {
  // 함수 앞에 붙은 async는 이 함수 안에 비동기적으로 실행이 필요한 코드가 있다는 것을 암시
  const response = await fetch('https://jsonplaceholder.typicode.com/users')
  const result = await response.text()
  // await은 뒤에 붙은 코드가 실행되고 fulfilled 혹은 rejected 상태의 프로미스 객체를
  // 리턴할 때까지 기다렸다가 작업이 완료된 객체를 리턴한다.
  // await은 async 함수 내부에서만 사용할 수 있다.
  console.log(result)
}

fetchAndPrint()
```

### async 함수의 실행 순서

async await은 코드가 보이는 순서대로 실행되지 않는다는 점을 명심해야한다.

```js
async function fetchAndPrint() {
  console.log(2) // 3. 2가 출력되고
  const response = await fetch('https://jsonplaceholder.typicode.com/users') //4. fetch 함수가 실행되면 실행 순서는 함수 바깥으로 이동한다.
  console.log(7) // 6. fetch 함수가 fulfilled 상태가 되어 해당 값이 response에 할당되면 이후 7이 출력된다.
  const result = await response.text() // 7. 두번째 fetch 함수가 실행되면 실행 순서는 다시 바깥으로 나가서
  console.log(result) // 9. 두번째 await 작업이 완료되면 result 가 출력된다.
}

console.log(1) // 1. 1이 가장 먼저 출력되고
fetchAndPrint() // 2. async 함수가 출력된다.
console.log(3) // 5. 나머지 코드들을 다 실행시킨다. 그 이후로는 await 뒤 fetch 함수의 프로미스 객체가 fullfilled 상태가 되기를 기다렸다가
console.log(4)
console.log(5)
console.log(6)
// 8. 밖에는 더이상 실행을 기다리는 코드가 없고 다시 await 뒤 함수의 프로미스 객체가 fulfilled 상태가 되기를 기다렸다가

// 결과 : 1,2,3,4,5,6,7,result
```

### async 에러 핸들링

async await 구문에서 rejected된 promise 객체를 try catch구문으로 핸들링 할 수 있다.

```js
async function fetchAndPrint() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.commmm/users') // 존재하지 않는 url로 rejected상태의 프로미스 객체가 리턴
    const result = await response.text()
    console.log(result)
  } catch (err) {
    // try 구문안의 코드 중 프로미스 객체가 rejected 되는 순간 catch 문으로 코드흐름 이동하고 err 파라미터로 작업실패정보가 넘어온다
    console.log(err)
  } finally {
    // 작업 결과에 상관없이 항상 실행
    console.log('exit')
  }
}

fetchAndPrint()

// 중요! async 함수는 항상 promise 객체를 리턴한다.
async function fetchAndPrint() {
  return 3
}

fetchAndPrint() // 3을 작업 성공 결과로 가진 fulfilled 상태의 프로미스 객체 리턴

async function fetchAndPrint() {
  return fetch('https://jsonplaceholder.typicode.com/users').then((response) => response.text())
}

fetchAndPrint() // then 메소드가 리턴하는 프로미스 객체와 동일한 작업 성공 결과와 상태를 가진 프로미스 객체 리턴
```

### async함수의 리턴값

**Promise 객체를 리턴하는 경우**
async 함수 안에서 promise 객체를 리턴하는 경우 해당 promise 객체와 동일한 상태와 작업 결과를 가진 promise 객체를 리턴한다.

```js
async function fetchAndPrint() {
  return Promise.resolve('Success') // 프로미스 객체 리턴
}

fetchAndPrint() // fulfilled 상태, 작업성공결과 Success 인 프로미스 객체 리턴
```

**Promise 객체의 이외의 값을 리턴하는 경우**
fulfilled 상태이면서, 리턴된 값을 작업 성공 결과로 가진 Promise 객체를 리턴한다

```js
async function fetchAndPrint() {
  return 3
}

fetchAndPrint()
```

**아무값도 리턴하지 않는 경우**
fulfilled 상태이면서, undefined를 작업 성공 결과로 가진 Promise 객체가 리턴한다.

```js
async function fetchAndPrint() {
  console.log('Hello Programming!')
}

fetchAndPrint()
```

**async 함수 내부에서 에러가 발생했을 때**
rejected 상태이면서, 해당 에러 객체를 작업 실패 정보로 가진 Promise 객체를 리턴한다.

```js
async function fetchAndPrint() {
  throw new Error('Fail')
}

fetchAndPrint()
```

### async안의 async 함수

async 함수는 프로미스 객체를 리턴하기 때문에 async 함수 안에서는 또다른 aync 함수를 await와 함께 자유롭게 사용할 수 있다.

```js
const applyPrivacyRule = async function (users) {
  const resultWithRuleApplied = users.map((user) => {
    const keys = Object.keys(user)
    const userWithoutPrivateInfo = {}
    keys.forEach((key) => {
      if (key !== 'address' && key !== 'phone') {
        userWithoutPrivateInfo[key] = user[key]
      }
    })
    return userWithoutPrivateInfo
  })

  const p = new Promise((resolve) => {
    setTimeout(() => {
      resolve(resultWithRuleApplied)
    }, 2000)
  })
  return p
}

async function getUsers() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    const result = await response.text()
    const users = JSON.parse(result)
    const resultWithPrivacyRuleApplied = await applyPrivacyRule(users)
    return resultWithPrivacyRuleApplied
  } catch (err) {
    console.log(err)
  } finally {
    console.log('exit')
  }
}

getUsers().then((result) => {
  console.log(result)
})
```

> **아직까지 callback, promisify, async await은 상호 보완적이다**
> 콜백을 함수의 파라미터로 바로 전달하는 전통적인 방식의 비동기 실행 함수들 중에서도 setInterval, addEventListener처럼 그 콜백이 단 한번이 아니라 여러 번 실행되어야 하는 것들은 Promisify해서 사용하면 안 된다. Promise 객체는 한번 fulfilled 또는 rejected 상태가 되고나면 그 상태와 결과가 다시는 바뀌지 않기 때문이다.
> async/await 구문의 경우, await은 async 함수 안에서만 사용할 수 있고, 코드의 top-level(어떤 함수 블록 안에 포함된 것이 아닌 코드의 최상위 영역)에서는 사용될 수는 없다. 그래서 코드의 top-level에서 async 함수가 리턴한 Promise 객체의 작업 성공 결과를 가져오려면 await을 사용할 수는 없고, 여전히 then 메소드를 사용해야한다.
