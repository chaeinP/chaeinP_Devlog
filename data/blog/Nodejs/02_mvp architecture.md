---
title: Nodejs_MVP 디자인 패턴
date: '2021-11-16'
tags: ['NODEJS']
draft: false
summary: 디자인 패턴 비교 분석하기 (1)
---

## 디자인 패턴의 목적

- 기능별 모듈화(기능별 독립성 증진)
- 코드 가독성 증진 및 협업에 용이
- 유닛 테스트 기반 개발 가능

## MVP 디자인 패턴이란?

MVP패턴은 MVC모델의 단점을 보완하기 위해 고안된 디자인 패턴이다.

### MVC 모델의 단점

- Model - View - Controller 간 경계가 모호해 사람들마다 해석과 사용의 차이가 발생
- observer가 Model의 데이터 변화를 감지해 View를 업데이트 하면서 둘 사이의 의존성이 높아짐
- Model과 View를 중개하는 Controller에 코드가 몰리면서 Controller의 비중이 높아짐(비대화)

MVC모델이 디자인 패턴이 해결하고자 했던 문제점들을 제대로 해결하지 못하자 모듈화를 더 높인 MVP 모델 도입

### MODEL - VIEW - PRESENTER

![mvc, mvp 모델](https://i2.wp.com/blog.dramancompany.com/wp-content/uploads/2016/05/MVC_MVP.png?ssl=1)

- MODEL : 데이터를 관리 및 조작, 처리하는 역할을 수행. (ex. Ajax 호출, 서버 통신)
- VIEW : 화면에 보여지는 모든 이벤트를 관리, 발생한 이벤트를 감지해 PRESENTER를 호출. VIEW와 PRESENTER는 1:1 관계로 움직인다. (ex. 돔 조작)
- PRESENTER : MODEL과 VIEW를 중개하는 역할, VIEW에서 전달된 이벤트를 처리하고 MODEL에 전달한다. MODEL의 변경은 PRESENTER가 감지해 VIEW로 전달한다. 이로 인해 MODEL과 VIEW를 철저히 분리시킬 수 있다. (ex. 이벤트 처리)

💡 MVP 모델에서 VIEW - PRESENTER가 1대1 관계를 가져야하는 이유

MVP모델에서 이벤트 또는 데이터의 전달은 `VIEW -> PRESENTER -> MODEL -> PRESENTER -> VIEW` 순으로 순차적으로 이루어진다. 여기서 PRESENTER는 이벤트 혹은 데이터를 전달받은 경로를 기억해야 해당하는 VIEW 또는 MODEL에 데이터를 전달할 수 있기 때문에 1 : 1관계가 형성된다. 이러한 이유로 MODEL또한 PRESENTER의 로직에 따라 여러개로 나뉘어질 수 있다.

### 작동 과정의 예

시나리오 : 사용자가 화면의 버튼을 누르면 버튼 밑에 현재시간이 뜬다.

1.  M, V, P 초기화 : 앱이 생성되면서 Model, View, Presenter가 생성되고 등록됨
2.  P -> V : Presenter가 View한테 버튼을 그리라고 시킴
3.  V 동작 : 버튼을 그려놓고 대기
4.  사용자가 버튼을 눌러버림
5.  V -> P :View가 Presenter한테 버튼이 눌렸다고 알림
6.  P -> M : Presenter가 Model한테 지금 몇 시냐고 물음
7.  M 동작 : Model이 시계를 봐서 현재 시간을 구함
8.  M -> P : Model이 Presenter한테 현재 시간을 알려줌
9.  P -> V : Presenter가 View한테 현재 시간을 버튼 밑에 그리라고 시킴
10. V 동작 : View가 버튼 밑에 현재 시간을 그림

참조 ) [안드로이드 개발자를 위한 MVP 패턴 #2 MVP 제대로 이해하기](https://blog.naver.com/wowterry/221042520987)

### MVC 와 MVP 차이 한눈에 보기

| MVC                                         | MVP                                                                |
| ------------------------------------------- | ------------------------------------------------------------------ |
| MODEL - VIEW - CONTROLLER가 서로 상호작용함 | MODEL - PRESENTER, PRESENTER - VIEW가 각각 상호작용함              |
| VIEW와 CONTROLLER는 다대일 관계             | VIEW와 PRESENTER는 일대일 관계 (VIEW 마다 각각의 PRESENTER를 갖음) |
| 모듈화 낮음                                 | 모듈화 높음                                                        |
| 유닛 테스트 기반 개발이 어려움              | 유닛 테스트 기반 개발 가능                                         |

### MVP 모델의 한계

- VIEW와 MODEL의 분리로 인해 PRESENTER에 더 많은 코드가 몰리게 됨.
  (기존 MVC 패턴이 갖는 controller 비대화 문제가 해결되지 않음)

- VIEW와 PRESENTER를 일대일 대응관계로 인해 여러 VIEW에서 발생하는 공통된 로직을 공유하지 못함. 이로 인해 오히려 코드의 양은 늘어나고 비효율적인 코드가 생성됨.

  📌 MVVM 패턴은 이러한 MVP 모델의 한계를 개선하기 위해 개발되었다.

[타입스크립트 MVP패턴 구현 예제](https://medium.com/@rohitkhatana/presenter-first-mvp-using-typescript-javascript-245a4fe041ef)
