---
title: CSS_FLEX BOX
date: '2021-08-22'
tags: ['CSS']
draft: false
summary: 매번 공부해도 또 까먹는 FLEX BOX의 모든것!
---

### CSS FLEX

> 이 글은 Heropy님의 블로그 ["css Flex 완벽 가이드"](https://heropy.blog/2018/11/24/css-flexible-box/)를 보며 공부한 study log입니다.

FLEX는 크게 `container` 와 `item`이라는 개념으로 나뉜다.
Container는 item의 부모요소이며 item 정렬을 위해선 container가 반드시 필요하다.

container와 item에 올 수 있는 속성은 다음과 같다.

| container                             | item                                      |
| ------------------------------------- | ----------------------------------------- |
| display                               | order                                     |
| flex-flow (flex-direction, flex-wrap) | flex (flex-grow, flex-shrink, flex-basis) |
| align-content                         | align-self                                |
| align-items                           | -                                         |
| justify-content                       | -                                         |

### Container 속성

#### (1) Display

| 값          | 의미                                            |
| ----------- | ----------------------------------------------- |
| flex        | Block 특성의 Flex Container를 정의 (수직 쌓임)  |
| inline-flex | Inline 특성의 Flex Container를 정의 (수평 쌓임) |

여기서 말하는 수직, 수평 쌓임은 item이 아닌 container의 쌓임을 의미한다.

#### (2) flex-flow

| 값             | 의미                | 기본값 |
| -------------- | ------------------- | ------ |
| flex-direction | item의 주 축을 설정 | row    |
| flex-wrap      | item의 줄 바꿈 설정 | nowrap |

**_flex-direction_**
값 | 의미 | 기본값
-|-|-
row|items를 수평축(왼쪽에서 오른쪽으로) 표시 | row
row-reverse | row의 반대(오른쪽에서 왼쪽으로)
column | items를 수직축(위에서 아래로)으로 표시 |
column-reverse | column의 반대(아래에서 위로)

**_flex-wrap_**
값 | 의미 | 기본값
-|-|-
nowrap |줄바꿈 하지 않음 | nowrap
wrap | 줄바꿈 함
wrap-reverse | wrap의 역방향으로 줄바꿈

#### (3) justify-content

> 주 축의 정렬 방법을 설정한다.

| 값            | 의미                                                                              | 기본값     |
| ------------- | --------------------------------------------------------------------------------- | ---------- |
| flex-start    | items을 시작점 기준으로 정렬                                                      | flex-start |
| flex-end      | items를 끝점 기준으로 정렬                                                        |
| center        | 가운데 정렬                                                                       |
| space-between | 시작 item은 시작점에 마지막 item은 끝점에 정렬되고 나머지는 그 사이에 고르게 정렬 |
| space-aroud   | items를 균등한 여백을 포함하여 정렬                                               |

#### (4) align-content

> 교차축의 정렬방법을 설정한다. 주의할 점은 flex-wrap이 wrap인 상태(줄바꿈 상태)이고 여백이 있을 경우에만 사용할 수 있다. items이 한줄일 경우 align-items를 사용할 수 있다.

| 값            | 의미                                                                            | 기본값  |
| ------------- | ------------------------------------------------------------------------------- | ------- |
| stretch       | Container의 교차 축을 채우기 위해 items을 늘림                                  | stretch |
| flex-start    | Items를 시작점(flex-start)로 정렬                                               |
| flex-end      | Items를 끝점(flex-end)로 정렬                                                   |
| center        | Items를 가운데 정렬                                                             |
| space-between | 시작 item은 시작점에, 마지막 item은 끝점에 정렬되고 나머지는 사이에 고르게 정렬 |
| space-around  | items를 균등한 여백을 포함하여 정렬                                             |

#### (5) align-items

> 교차축에서 items의 정렬방법을 설정한다. 한 줄을 기준으로 움직인다는 것을 염두해야한다. 주로 items가 한 줄일 경우 많이 사용한다. items가 여러중일 경우 align-content 속성이 우선한다. align-items를 사용하려면 align-content속성을 기본값인 (stretch) 로 설정해야한다.

| 값         | 의미                                           | 기본값  |
| ---------- | ---------------------------------------------- | ------- |
| stretch    | container의 교차 축을 채우기 위해 items를 늘림 | stretch |
| flex-start | items를 각 줄의 시작점으로 정렬                |
| flex-end   | items를 각 줄의 끝점으로 정렬                  |
| center     | items를 가운데 정렬                            |
| baseline   | items를 문자 기준선에 정렬                     |

**_item 내 글자 정렬_**

> 정렬된 item안의 글자를 중앙정렬 하고 싶을 때 아이템 요소에 다시 diplay:flex속성을 부여하고 justify-content : center, align-items: center를 설정하면 된다.

<iframe height="400" width="100%" scrolling="no" src="https://codepen.io/chaeinp/embed/dyRvvxG?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/dyRvvxG">
  </a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### item 속성

#### (1) order

> item의 순서를 설정한다. item에 숫자를 지정하고 숫자가 클수록 순서가 밀리며 음수가 허용된다. HTML구조와 상관없이 순서를 변경할 수 있다.

| 값   | 의미               | 기본값 |
| ---- | ------------------ | ------ |
| 숫자 | item의 순서를 설정 | 0      |

#### (2) flex-grow

> item의 증가 너비 비율을 설정한다. 숫자가 크면 더 많은 너비를 가진다. item이 가변 너비가 아니거나 값이 0일경우 효과가 없다. 모든 items의 총너비에서 각 item의 증가 너비 비율 만큼 item은 너비를 가질 수 있다. 예를 들어 item이 3개고 증가너비가 각각 1,2,1이면 첫번째는 1/4인 25% 두번째 item은 1/2인 50%, 세번째는 1/4인 25% 너비를 갖는다.

| 값   | 의미                         | 기본값 |
| ---- | ---------------------------- | ------ |
| 숫자 | item의 증가 너비 비율을 설정 | 0      |

item 3개의 너비가 각 100px이고 하나만 flex-grow:1설정이 되어있다면 그 외 두개는 100픽셀을 차지하고 나머지 아이템만 콘테이너에서 차지할 수 있는 최대 너비를 갖게 된다. 같은 상황에서 100픽셀 설정된 다른 아이템을 flex-grow:2로 설정하면 정확하게 1:2의 비율로 너비가 나뉘어지지 않는데 정확한 비율로 나눠지게 하기 위해선 기존 너비값인 100픽셀을 삭제해야한다. 브라우저의 크기에 따라 너비가 가변하는 아이템과 너비가 고정되어있는 아이템이 한 줄에 정렬되어있는 경우 flex-grow를 사용하면 설정이 용이하다.

#### (3) flex-shrink

> item의 감소하는 너비 비율을 설정한다. 숫자가 크면 더 많은 너비가 감소한다. flex-grow와 마찬가지로 너비가 가변값이 아니거나 0일 경우 효과가 없다.

| 값   | 의미                         | 기본값 |
| ---- | ---------------------------- | ------ |
| 숫자 | item의 증가 감소 비율을 설정 | 1      |

#### (4) flex-basis

> item(공간 배분 전) 기본 너비를 설정한다. 값이 auto인 경우 width, height등의 속성으로 Item의 너비를 설정할 수 있다. 하지만 단위 값이 주어질 경우 설정할 수 없다.

| 값   | 의미                     | 기본값 |
| ---- | ------------------------ | ------ |
| auto | 가변 item과 같은 너비    | auto   |
| 단위 | px, em, cm등 단위로 지정 |

#### (5) flex

> flex-grow, flex-shrink, flex-basis를 아우르는 단축속성이다. flex: 증가너비 감소너비 기본너비; 순으로 작성한다.

| 값          | 의미                         | 기본값                                                |
| ----------- | ---------------------------- | ----------------------------------------------------- |
| flex-grow   | item의 증가 너비 비율을 설정 | 0                                                     |
| flex-shrink | item의 감소 너비 비율을 설정 | 1                                                     |
| flex-basis  | 아이템의 기본 너비 설정      | 0 (단축속성일 때와 따로 사용할 때 기본값이 다름 주의) |

#### (6) align-self

> 교차축에서 개별 item의 정렬 방법을 설정한다. align-items는 Container 내 모든 items의 정렬방법을 설정한다. 필요에 의해 일부 item만 변경하고 싶은 경우 align-self를 사용할 수 있다. 이 속성은 align-items보다 우선한다.

| 값         | 의미                                         | 기본값 |
| ---------- | -------------------------------------------- | ------ |
| auto       | container의 align-items 속성을 상속받음      | auto   |
| stretch    | container의 교차축을 채우기 위해 item을 늘림 |
| flex-start | item을 각 줄의 시작점으로 정렬               |
| flex-end   | item을 각 줄의 끝점으로 정렬                 |
| center     | item을 가운데 정렬                           |
| baseline   | item을 문자 기준선에 정렬                    |
