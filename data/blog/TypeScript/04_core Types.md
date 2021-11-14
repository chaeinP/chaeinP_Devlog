---
title: TypeScript_Core Types
date: '2021-11-12'
tags: ['TypeScript']
draft: false
summary: 타입스크립트의 Core Types
---

> 이 콘텐츠는 udemy에 있는 [타입스크립트 완벽히 이해하는 방법 - 21에디션](https://www.udemy.com/course/best-typescript-21/) 강의를 보고 기록한 스터디 로그입니다.

## Core Types

| 데이터 타입 | 의미                                                 |
| ----------- | ---------------------------------------------------- |
| number      | 숫자 (다른 언어와 달리 정수, 실수 등의 구분이 없다.) |
| string      | 문자열                                               |
| boolean     | True, False                                          |

## Type 할당

```ts
const number = 5

let number = 5

let number
number = 5
```

위 3방법으로 변수를 선언하고 값을 할당할 때 타입스크립트가 값을
