---
title: TypeScript_타입스크립트 타입 복습
date: '2021-11-12'
tags: ['TypeScript']
draft: true
summary: 타입스크립트의 Core Types
---

> 이 콘텐츠는 udemy에 있는 [타입스크립트 완벽히 이해하는 방법 - 21에디션](https://www.udemy.com/course/best-typescript-21/) 강의를 보고 기록한 스터디 로그입니다.

## Core Types

| 데이터 타입 | 의미                                                                          |
| ----------- | ----------------------------------------------------------------------------- |
| number      | 숫자 (다른 언어와 달리 정수, 실수 등의 구분이 없다.)                          |
| string      | 문자열                                                                        |
| boolean     | True, False                                                                   |
| object      | 자바스크립트의 객체                                                           |
| Array       | 자바스크립트의 배열                                                           |
| tuple       | 자바스크립트에는 없는 타입, 고정된 길이, 고정된 타입 배열                     |
| enum        | 자바스크립트에는 없는 열거형 타입                                             |
| any         | 모든 타입을 any로 치환할 수는 없지만 많은 타입을 대체할 수 있는 유연성을 갖음 |
| union       | 하나의 변수에 복수의 타입 값이 들어올 가능성이 있을 때 사용                   |
| literal     | 변수나 매개변수에 정확한 값을 타입으로 설정하는 것                            |
| Function    | 함수의 타입, 매개변수와 리턴타입을 지정                                       |
| unknown     | 사용자가 어떤 값을 입력할지 모를때 사용하는 타입으로 any와는 다름             |

## number, string, boolean 타입 추론

```ts
const number1 = 5
```

const 상수로 선언한 값은 변하지 않기 때문에 그 값이 지닌 type을 바로 추론할 수 있다. `상수의 경우 따로 type을 명시해주지 않아도 타입 추론이 된다.` 더 구체적으로 살펴보면 number1의 type은 숫자 5가 된다. number1는 다른 숫자로 바꿀 수 없는 상수이기 때문에 type 또한 `number1 : 5`로 인식한다.

```ts
let number1 = 5
```

여기서 number1의 type은 number로 추론된다. 상수 number1의 type을 숫자 5로 인식했던것과는 차이가 있지만 여전히 number로 올바르게 추론하고 있다.

따라서 `let number1 : number = 5;`처럼 올바르게 추론되고 있는 타입을 다시 명시적으로 지정해주는 것은 중복되는 것이고 이러한 명시적 표시 없이도 typescript가 정확하게 추론할 수 있기 때문에 이 방법은 권장되지 않는다.

```ts
let number: number
number = 5
```

그러나 위처럼 선언과 할당이 구분되어있을 때에는 type을 명시해주어야한다. TypeScript에서 타입추론은 할당된 초기값을 기준으로 이루어지기 때문이다. 위처럼 선언과 할당이 분리되어있는 경우에는 type추론을 할 수 없기 때문에 명시적으로 type을 표시해주어야한다.

💡 type추론 정리

변수 선언만 이루어져있을 때에는 타입을 명시적으로 지정해주어야한다. 선언과 할당이 함께 이루어진경우에는 TypeScript에서 타입을 정확하게 추론하기 때문에 굳이 중복해서 type을 명시할 필요가 없다. 상수의 경우에는 값이 변할 수 없기 때문에 추론된 Type은 그 값 자체가 된다.

## object 타입 추론

```ts
const person = {
  name: 'Lyli',
  age: 30,
}
```

위처럼 person이라는 객체를 선언하면 타입스크립트는 자동으로 person의 타입을 아래와 같이 추론한다.

```js
const person = {
	name: string;
	age: number;
}
```

자칫 객체와 유사해보이지만 내부 요소에 ;가 붙어있음에 유의한다.

객체의 타입을 만약 이렇게 지정하면 어떻게 될까?

```ts
const person: object = {
  name: 'Lyli',
  age: 30,
}

console.log(person.name) //에러
```

이때 person은 object타입을 갖지만 내부 키 type에 대한 정보가 없기 때문에 어떠한 키 값에도 접근할 수가 없다.
object타입을 명시적으로 표현하려면 아래와 같이 작성하면 된다.

```ts
const person: {
	name: string;
	age: number;
} = {
	name : 'Lyli';
	age : 30;
}
```

그러나 위에서 언급했든 타입스크립트가 완벽하게 추론가능한 타입은 굳이 중복해서 명시적으로 타입을 표시할 필요가 없다.
만약, 중첩된 객체의 타입은 어떻게 될까?

```ts
const product = {
  id: 'abc1',
  price: 12.99,
  tags: ['great-offer', 'hot-and-new'],
  details: {
    title: 'Red Carpet',
    description: 'A great carpet - almost brand-new!',
  },
}
```

위 객체의 타입은 아래와 같다.

```js
{
  id: string;
  price: number;
  tags: string[];
  details: {
    title: string;
    description: string;
  }
}
```

## Array 타입 추론

```ts
const product = {
  id: 'abc1',
  price: 12.99,
  tags: ['great-offer', 'hot-and-new'],
}
```

위 코드의 경우 tags는 `tags: string[]`이다. Array의 타입을 명시적으로 표시하고 싶다면 다음과 같이 작성하면 된다.

```ts
let favoriteActivities: string[]
favoriteActivities = ['sports']
```

만약 타입이 혼합된 배열은 어떻게 선언해야할까? 그럴 땐 any 타입을 선택할 수 있다. 하지만 any타입을 사용하는 것은 주의를 기울여야 한다. any는 모든 타입을 지칭할 수 있어 매우 유연하지만 그만큼 타입스크립트를 사용하는 목적을 상쇄시키기도 한다.

```ts
let favoriteActivities: any[]
favoriteActivities = ['sports', 1]
```

타입 추론은 꽤 편리하다.

```ts
for (const tag of person.tags) {
  console.log(tag.toUpperCase()) //정상 작동
  console.log(tag.map()) //에러
}
```

위에서 person.tags는 `string[]`로 타입 추론되었다. 따라서 내부 값들도 자동으로 string으로 타입이 추론되어 string 메소드인 toUpperCase를 사용할 수 있지만 배열 메소드인 map은 사용할 수 없다.

## tuple 타입 추론

```ts
const product = {
  id: 'abc1',
  price: 12.99,
  role: [2, 'author'],
}
```

위 코드에서 role을 tuple 자료형으로 선언하고 싶다고 하자. 튜플은 지정된 위치에 항상 그 자료형의 값만 올 수 있다. 여기서 타입스크립트는 role의 타입을 `role: (string | number)[]`다음과 같이 정의한다. string이나 number타입이 올 수 있는 배열이라는 뜻이다. 이런것을 `union type`이라고 한다. 이러한 타입 추론을 보니 아직 타입스크립트는 role이 tuple자료형이라는 것을 인지하지 못하고 있다. 이 경우 `product.role.push('admin')` 처럼 값을 변경할 수 있다. tuple 자료형이라면 이러한 조작은 허용되선 안된다.

_tuple 자료형은 type 추론이 되지 않기 때문에 명시적으로 작성해주어야 한다._

```ts
const product: {
  id: string
  price: number
  role: [number, string]
} = {
  id: 'abc1',
  price: 12.99,
  role: [2, 'author'],
}

product.role[1] = 10 // 에러
product.role.push('admin') // 가능
```

위 코드에서 튜플 자료형을 명시적으로 선언해주었다. 따라서 1번째 인덱스에는 string만 올 수 있기 때문에 숫자로 교체하려고 할 때 컴파일 에러가 발생한다. 그러나 문자열을 추가하려고 하자 에러가 발생하지 않는다. 실제로 타입스크립트에선 tuple에 push가 허용된다. 그러나 `product.role = [3, 'hello', 'hi']` 처럼 새로운 값을 할당할 경우에는 에러가 발생된다. 따라서 이 부분은 의도하지 않은 결과가 나오지 않게 신경써서 코딩해야한다.

## enum 타입 추론

```ts
enum Role {
  ADMIN,
  READ_ONLY,
  AUTHOR,
}

const person: {
  name: 'Lily'
  age: 30
  hobbies: ['Sports', 'Cooking']
  role: ROLE.ADMIN
}
```

enum은 변수들의 집합을 정의하는데 쓰여진다. 만약 주어진 초기값이 없다면 각 변수들은 앞에서부터 0, 1, 2순으로 숫자 값을 갖는다. 만약 초기값을 설정하고 싶다면 `enum Role { ADMIN = 5, READ_ONLY, AUTHOR };`처럼 가장 맨 앞 변수에 시작할 숫자를 명시해주면 된다. 이렇게 작성하면 그 뒤에 값에 순차적으로 숫자값이 부여된다.

값은 숫자 뿐만 아니라 문자로 줄 수 있다. `enum Role {ADMIN = 'ADMIN', READ_ONLY = 100}`처럼 숫자와 문자를 혼재할 수도 있다.

## any 타입 추론

```ts
let str: any
str = ['string']

let str: any[]
str = [1, 'string', 3]
```

any는 기본적인 타입들을 대체할 수 있다. 그러나 any를 사용하는 것은 타입스크립트의 장점을 사용하지 않고 기존 바닐라 자바스크립트를 사용하는 것과 같은 결과를 낳기 때문에 지양해야한다.

## union 타입

```ts
function combine(intput1: number | string, input2: number | string) {
  const result = input1 + input2 // 에러
  return result
}
```

위 함수의 경우 인자로 들어오는 input1, input2를 union 자료형으로 명시했다. 그러나 내부에 연산 부분에서 에러가 발생하는데 이는 타입스크립트가 input을 union type으로 인식하긴 했으나 실제 값이 어떤 타입으로 들어올지 모르기 때문이다. 이때는 if else 문으로 조건을 나누어 연산해주어야 한다.

```ts
function combine(intput1: number | string, input2: number | string) {
  let result
  if (typeof input1 === 'number' && typeof input2 === 'number') {
    result = input1 + input2 // 정상 작동
  } else {
    result = input1.toString() + input2.toString()
  }
  return result
}
```

## literal 타입

```ts
function combine(
  intput1: number | string,
  input2: number | string,
  resultConversion: 'as-number' | 'as-text'
) {
  let result
  if (
    (typeof input1 === 'number' && typeof input2 === 'number') ||
    resultConbersion === 'as-number'
  ) {
    result = +input1 + +input2 // 정상 작동
  } else {
    result = input1.toString() + input2.toString()
  }
  return result
}
```

위 코드에서 resultConversion은 'as-number'와 'as-text'라는 두개의 리터럴 타입을 가질 수 있다. 이때 리터럴 타입은 일반 타입으로 설정된 값이다. 특정 값 자체가 타입이 되는 것을 리터럴 타입이라고 한다. 이전에 `const num = 5;`에서 num의 타입이 5로 특정된걸 언급했는데 여기서 5도 리터럴 타입이다.

## type alias

```ts
function combine(intput1: number | string) {
  return input1
}

type Combinable = number | string
function combine(input1: Combinable) {
  return input1
}
```

복잡한 type을 type alias를 통해 별칭을 지어 편리하게 사용할 수 있다.

```ts
// alias 적용 전
function greet(user: { name: string; age: number }) {
  console.log('Hi, I am ' + user.name)
}

function isOlder(user: { name: string; age: number }, checkAge: number) {
  return checkAge > user.age
}

// alias 적용 후
type User = { name: string; age: number }

function greet(user: User) {
  console.log('Hi, I am ' + user.name)
}

function isOlder(user: User, checkAge: number) {
  return checkAge > user.age
}
```

## 함수의 리턴 타입

```ts
function add(n1: number, n2: number) {
  return n1 + n2
}
```

위 add 함수에서 리턴타입은 자동으로 number타입으로 추론된다.

```ts
function add(n1: number, n2: number) {
  return n1.toString() + n2.toString()
}
```

위 경우 리턴타입은 string으로 추론된다.
함수의 리턴타입을 명시적으로 적어줄 때는 아래처럼 명시하면 된다.

```ts
function add(n1: number, n2: number): number {
  return n1 + n2
}
```

하지만 이 방식도 마찬가지로 굳이 타입을 명시해줄 필요가 없다면 명시하지 않는 것이 좋다.

함수 타입에는 자바스크립트에 없는 타입이 있다. void타입이다.

```ts
function printResult(num: number) {
  console.log('Result:' + num)
}
```

위 함수는 return 값이 없다. 이 경우 함수의 타입은 void로 추론된다. 자바스크립트에서 return값이 없는 함수를 실행시켜 console에 출력하면 undefined가 뜬다. undefined 또한 타입스크립트에서 하나의 타입으로 간주한다.

```ts
let value: undefined
```

이처럼 변수에 타입으로 undefined를 선언할 수 있다. 그러나 함수의 경우 undefined를 타입으로 갖는 경우가 따로 있다.

```ts
function printResult(num: number): undefined {
  console.log('Result:' + num)
  return
}
```

위처럼 return이 있지만 값이 없는 경우 undefined 타입을 갖는다. 이를 제외하고는 void타입을 사용한다. 하지만 위와 같은 상황에서도 void를 쓸수 있기 때문에 undefined는 꼭 써야하는 경우나, 확신할 수 있을 때만 사용한다.

함수는 그 자체로 타입이 될 수 있다.

```ts
function add(n1: number, n2: number) {
  return n1 + n2
}

let combineValues // 함수 타입을 갖는 변수
combineValues = add
combineValues = 5 // 에러가 발생하지 않음
```

위처럼 함수를 할당받을 변수는 함수타입이라고 지정해주지 않으면 타입이 any가 돼 어떤 값도 받을 수 있게 된다. 따라서 이러한 상황에서는 아래처럼 명시적으로 Function타입을 부여해야 한다.

```ts
let combineValues: Function
```

하지만 위와 같은 경우 모든 함수 형식을 다 할당할 수 있기 때문에 구체적으로 어떤 형태의 함수를 받을 것인지를 명시하는 것이 좋다.

```ts
let combineValues: (a: number, b: number) => number
```

이 경우엔 number타입의 인자가 두개이면 return 값이 number인 함수만을 할당할 수 있다.

만약, 인자로 콜백함수가 들어간다면 이 또한 같은 형시으로 함수 타입을 지정해주어야 한다.

```ts
function addAndHandle(n1: number, n2: number, cb: (num: number) => void) {
  const result = n1 + n2
  cb(result)
}

addAndHandle(10, 20, (result) => {
  console.log(result)
})
```

addAndHandle 함수를 실행할때 들어간 인자 result는 타입스크립트에서 자동으로 number로 타입을 추론한다. 그 이유는 위에 함수 선언에서 콜백함수의 인자를 이미 number타입으로 지정해줬기 때문이다.

여기서 중요한 점은 콜백 함수의 리턴 타입이 void일지라도 콜백함수는 값을 반환할 수 있다.

```ts
function sendRequest(data: string, cb: (response: any) => void) {
  // ... sending a request with "data"
  return cb({ data: 'Hi there!' })
}

sendRequest('Send this!', (response) => {
  console.log(response)
  return true
})
```

이 함수를 보면 콜백의 리턴 타입은 void이지만 실행문에서 true를 리턴하고 있다. 하지만 컴파일 에러는 발생하지 않는다.

## unknown type

```ts
let userInput: unknown

userInput = 5
userInput = 'Max'
```

변수에 unknown 타입을 지정하면 모든 타입 값을 할당할 수 있다. 이는 마치 any와 동작방식이 같아 보인다. 하지만 unknown타입은 any와는 다르다.

```ts
let userInput: unknown
let userName: string

userName = userInput // 에러

let userInput: any
let userName: string

userName = userInput // 정상 작동
```

unknown타입의 변수를 사용하기 위해선 값을 먼저 할당한 뒤 타입 가드를 통해 타입을 확인해야 한다.

```ts
let userInput: unknown
let userName: string

userInput = 'MAX'
if (typeof userInput === 'string') {
  // 타입 가드
  userName = userInput
}
```

unknown은 타입가드를통해 타입을 한정시키고 확인하는 과정을 거쳐 사용된다는 점에서 any보다 안전하다. 이 때문에 any를 써양하는 상황에서는 unknown을 사용하기를 권장한다.
