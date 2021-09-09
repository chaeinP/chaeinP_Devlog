---
title: JavaScript_Promisify
date: '2021-08-02'
tags: ['JavaScript']
draft: false
summary: 프로미스 객체 직접 만들기!
---

리턴된 프로미스의 객체가 rejected 상태일 때 해당 에러를 핸들링 할 수 있는 방법은 다음과 같다.

### 1. then 메소드의 두번째 인자 활용하는 법

### new Promise()

> 생성자 키워드 new와 Promise 를 함께 쓰면 프로미스 객체를 직접 만들 수 있다. 프로미스 객체 안에 위치하는 콜백함수는 resolve와 reject라는 두개의 파라미터를 가진다. resolve 와 reject는 프로미스 객체 생성시 자동으로 연결되는 메소드로 resolve는 생성될 프로미스 객체를 fulfilled 상태로 만들수 있는 함수가 연결되고 reject는 프로미스 객체 상태를 rejected로 만들수 있는 함수가 연결된다.

```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('success')
  }, 2000) // p라는 프로미스 객체가 2초뒤 fulfilled 상태가 되고 'success'라는 작업 성공 결과를 갖는다.
})

p.then((result) => console.log(result)) // 프로미스 객체 상태 확인

let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(new Error('fail'))
  }, 2000) // p라는 프로미스 객체가 2초뒤 rejected 상태가 되고 'fail'라는 작업 실패 결과를 갖는다.
})

p.catch((error) => {
  console.log(error)
})
```

### Promise 객체를 직접 만들어 사용하는 이유

**1. 전통적인 형식의 비동기 실행 함수를 사용하는 코드를, Promise 기반의 코드로 변환하기 위해**

```js
function wait(text, milliseconds) {
  setTimeout(() => text, milliseconds)
}

fetch('https://jsonplaceholder.typicode.com/users')
  .then((response) => response.text())
  .then((result) => wait(`${result}`, 2000))
  .then((result) => {
    console.log(result)
  })
```

이렇게 쓰면 두번째 위치한 then 메소드가 2초 후에 리스폰스의 내용 뒤에 result를 리턴할 것 같지만 코드를 실행해보면 undefined가 출력된다. 여기서 쓰인 wait 함수는 setTimeout 함수를 실행할 뿐 아무것도 리턴하지 않기 때문이다. setTimeout 함수의 콜백이 리턴하는 text는 wait 함수의 리턴값이 아니다. 이처럼 promise chaining 안에 이렇게 비동기 실행되는 함수를 바로 사용하면, 나중에 실행되는 부분의 리턴값을 Promise Chain 에서 사용할 수 없게 된다. 이때 Promise 객제를 직접 생성하면 이러한 문제를 해결할 수 있다.

```js
function wait(text, milliseconds) {
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(text)
    }, 2000)
  })
  return p
}

fetch('https://jsonplaceholder.typicode.com/users')
  .then((response) => response.text())
  .then((result) => wait(`${result}`, 2000))
  .then((result) => {
    console.log(result)
  })
```

이처럼 전통적인 형식의 비동기 실행함수를 Promise 객체로 감싸서 그 Promise 객체를 리턴하는 형식으로 만드는 작업을 `Promisify` 라고 한다.

**2. 콜백 헬을 개선하기 위해**
콜백을 파라미터에 집어넣는 전통적인 형식의 비동기 실행함수들은 순자적으로 비동기 함수들을 실행하려고 하면 콜백 안에 콜백 안에 콜백이 있는 콜백 헬 현상이 나타난다.

```js
fs.readFile('file1.txt', 'utf8', (error1, data1) => {
  if (error1) {
    console.log(error1)
  } else {
    console.log(data1)
    fs.readFile('file2.txt', 'utf8', (error2, data2) => {
      if (error2) {
        console.log(error2)
      } else {
        console.log(data2)
        fs.readFile('file3.txt', 'utf8', (error3, data3) => {
          if (error3) {
            console.log(error3)
          } else {
            console.log(data3)
          }
        })
      }
    })
  }
})
```

이러한 코드들은 코드 가독성이 크게 떨어져 에러감지 및 코드 유지 보수에 매우 비효율 적이다. 이러한 문제를 Promisify로 해결할 수 있다.

```js
function readFile_promisified(filename) {
  const p = new Promise((resolve, reject) => {
    fs.readFile(filename, 'utf8', (error, data) => {
      if (error) {
        reject(error) // 에러 발생 시 -> rejected
      } else {
        resolve(data) // 파일 내용 읽기 완료 -> fulfilled
      }
    })
  })
  return p
}

readFile_promisified('file1.txt')
  .then((data) => {
    console.log(data)
    return readFile_promisified('file2.txt')
  })
  .then((data) => {
    console.log(data)
    return readFile_promisified('file3.txt')
  })
  .then((data) => {
    console.log(data)
  })
  .catch((error) => {
    console.log(error)
  })
```

이렇게 Promise 객체를 활용하면 콜백헬을 방지하고 가독성 높은 코드 작성이 가능해진다.

### Promisify 하면 안되는 함수들

전통적인 비동기 실행함수라고 해서 모두 Promisify해서 사용해도 되는 것은 아니다. 기존의 비동기 실행함수들 중에서도 콜백을 한번만 실행하는 함수(setTimeout, readFile)들만 `Promisify`해서 사용한다. `setInterval()` 이나 `addEventListener()`와 같이 콜백을 여러번 실행하는 함수들은 Promisify해서 사용할 수 없다. Promise 객체는 한번 pending 상태에서 fulfilled 또는 rejected가 되면 그 뒤로는 상태와 결과값이 변하지 않기 때문이다.

```js
const box = document.getElementById('test')
let count = 0

// 박스를 클릭하면 count가 늘어나는 코드
function addEventListener_promisified(obj, eventName) {
  const p = new Promise((resolve, reject) => {
    obj.addEventListener(eventName, () => {
      count += 1
      resolve(count)
    })
  })
  return p
}

addEventListener_promisified(box, 'click').then((eventCount) => {
  console.log(eventCount)
})
```

이 코드를 실행하고 box를 클릭하면 처음에 1이 출력되고 그 뒤로는 작동하지 않는다. pending 상태의 promise 객체가 한번 fulfilled가 되고 나면 그 이후 상태 및 결과가 고정되어 바뀌지 않기 때문이다.

### 이미 상태가 결정된 promise 객체 만드는 법

(1) fulfilled 상태의 Promise 객체 만들기

```js
const p = Promise.resolve('success')
```

Promise의 resolve메소드를 사용하면 바로 fulfilled 상태의 promise 객체를 만들 수 있다. 위와 같이 쓰면 fulfilled 상태이면서 작업 성공 결과로 문자열 'success'를 가진 Promise 객체를 만들 수 있다.

(2)rejected 상태의 promise 만들기

```js
const p = Promise.reject(new Error('fail'))
```

위와 같이 쓰면 rejected 상태이면서 작업 실패 결과로 , fail이라는 메시지를 가진 Error 객체를 가진 Promise 객체를 만들 수 있다.
