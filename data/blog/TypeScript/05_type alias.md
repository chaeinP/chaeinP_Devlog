---
title: TypeScript_타입 alias
date: '2021-11-18'
tags: ['TypeScript']
draft: false
summary: type alias
---

## Type alias

- interface랑 유사하지만 엄연히 다르다
- 타입의 이름을 지정하여 사용할 수 있다.
- 만들어진 타입에 별명을 붙여 사용하는 것일 뿐 새로운 타입을 만드는 것이 아니다.

## Aliasing Primitive

primitive 타입은 이름 자체가 간단하고 직관적이기 때문에 따로 alias로 별칭을 지어 사용할 일은 거의 없다.

```ts
type MyStringType = string
const str = 'world'
let myStr: MyStringType = 'hello'
myStr = str
```

## Aliasing Union Type

반복되는 union type의 경우 매번 길게 적어주는 것이 불편하기 때문에 alias를 활용하는 것이 좋다.

```ts
let person: string | number = 0
person = 'Mark'

type StringOrNumber = string | number //union type에 별칭 부여

let another: StringOrNumber = 0
another = 'Anna'
```

## Aliasing Tuple

```ts
let person: [string, number] = ['Mark', 35] //일반적인 방식

type PersonTuple = [string, number] //tuple에 aliasing
let another: PersonTuple = ['Anna', 24]
```

## Aliasing Function

```ts
type EatType = (food: string) => void
```
