---
title: CSS_GRID
date: '2021-08-23'
tags: ['CSS']
draft: false
summary: 이제 반응형 웹은 GRID로 손쉽게
---

### CSS GRID

> 이 글은 Heropy님의 블로그 [css grid 완벽 가이드](https://heropy.blog/2019/08/17/css-grid/)를 보며 공부한 study log입니다.

Css 그리드는 2차원(행과 열)의 레이아웃 시스템으로 flex box가 1차원의 레이아웃 구성에 최적화되어있다면 좀 더 복잡한 레이아웃에는 grid를 사용할 수 있다.

그리드 또한 flex와 같이 container와 item이라는 두가지 개념으로 구분되어 있다.

### Grid Container Properties

| 속성                        | 의미                                            |
| --------------------------- | ----------------------------------------------- |
| display                     | 그리드 컨테이너(Container)를 정의               |
| grid-template-rows          | 명시적 행(Track)의 크기를 정의                  |
| grid-template-columns       | 명시적 열(Track)의 크기를 정의                  |
| grid-template-areas         | 영역(Area) 이름을 참조해 템플릿 생성            |
| grid-template               | grid-template-xxx의 단축 속성                   |
| row-gap(grid-row-gap)       | 행과 행 사이의 간격(Line)을 정의                |
| column-gap(grid-column-gap) | 열과 열 사이의 간격(Line)을 정의                |
| gap(grid-gap)               | xxx-gap의 단축 속성                             |
| grid-auto-rows              | 암시적인 행(Track)의 크기를 정의                |
| grid-auto-columns           | 암시적인 열(Track)의 크기를 정의                |
| grid-auto-flow              | 자동 배치 알고리즘 방식을 정의                  |
| grid                        | grid-template-xxx과 grid-auto-xxx의 단축 속성   |
| align-content               | 그리드 콘텐츠(Grid Contents)를 수직(열 축) 정렬 |
| justify-content             | 그리드 콘텐츠를 수평(행 축) 정렬                |
| place-content               | align-content와 justify-content의 단축 속성     |
| align-items                 | 그리드 아이템(Items)들을 수직(열 축) 정렬       |
| justify-items               | 그리드 아이템들을 수평(행 축) 정렬              |
| place-items                 | align-items와 justify-items의 단축 속성         |

### Grid Items Properties

| 속성              | 의미                                                             |
| ----------------- | ---------------------------------------------------------------- |
| grid-row-start    | 그리드 아이템(Item)의 행 시작 위치 지정                          |
| grid-row-end      | 그리드 아이템의 행 끝 위치 지정                                  |
| grid-row          | grid-row-xxx의 단축 속성(행 시작/끝 위치)                        |
| grid-column-start | 그리드 아이템의 열 시작 위치 지정                                |
| grid-column-end   | 그리드 아이템의 열 끝 위치 지정                                  |
| grid-column       | grid-column-xxx의 단축 속성(열 시작/끝 위치)                     |
| grid-area         | 영역(Area) 이름을 설정하거나, grid-row와 grid-column의 단축 속성 |
| align-self        | 단일 그리드 아이템을 수직(열 축) 정렬                            |
| justify-self      | 단일 그리드 아이템을 수평(행 축) 정렬                            |
| place-self        | align-self와 justify-self의 단축 속성                            |
| order             | 그리드 아이템의 배치 순서를 지정                                 |
| z-index           | 그리드 아이템의 쌓이는 순서를 지정                               |

**_(1) display_**

> 그리드를 사용하기 위해 컨테이너에 필수로 작성해야한다.

| 값           | 의미                                |
| ------------ | ----------------------------------- |
| grid         | Block 특성의 Grid Container를 정의  |
| iniline-grid | Inline 특성의 Grid Container를 정의 |

**_(2) grid-template-rows_**

> 컨테이너에 부여되는 속성으로 명시적 행의 크기를 정의한다. 동시에 라인의 이름도 정의할 수 있다. fr(fraction, 공간 비율)단위를 사용할 수 있다. repeat()함수를 사용할 수 있다.

**_(3) grid-template-columns_**

> 컨테이너에 부여되는 속성으로 명시적 열의 크기를 정의한다. 동시에 라인의 이름도 정의할 수 있다. fr(fraction, 공간 비율)단위를 사용할 수 있다. repeat()함수를 사용할 수 있다.

**_(4) grid-row / grid column_**

> 아이템에 부여되는 속성으로 그리드 선의 시작 위치와 끝 위치를 지정한다. '숫자'를 지정하거나 '선이름'을 지정하거나, span 키워드를 사용한다.

Basic 예제

<iframe height="350" width="100%" scrolling="no" title="grid_1" src="https://codepen.io/chaeinp/embed/MWogXBK?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
See the Pen <a href="https://codepen.io/chaeinp/pen/MWogXBK">
grid_1</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
on <a href="https://codepen.io">CodePen</a>.
</iframe>

Advanced(선에 이름 부여) 예제

<iframe height="350" width="100%" scrolling="no" title="grid-template:advanced" src="https://codepen.io/chaeinp/embed/powzBmG?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/powzBmG">
  grid-template:advanced</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

**_(5) grid-template-areas_**

> 컨테이너에 부여되는 속성으로 지정된 그리드 영역 이름(grid-area)를 참조해 그리드 템플릿을 생성한다. gird area는 그리드 아이템에 적용하는 속성이다.

**_(6) row_gap / column_gap_**

> 컨테이너에 부여되는 속성을로 각각 행과 행사이의 간격(명확하게는 그리드 선의 크기), 열과 열사이의 간격을 지정한다. 기존엔 grid-라는 접두사가 붙었으나 더이상 사용되지 않는다. gap이라는 단축속성을 이용하면 gap: row_gap column_gap;로 사용할 수 있다.

<iframe height="350" width="100%" scrolling="no" title="grid_area" src="https://codepen.io/chaeinp/embed/NWgKBpx?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
See the Pen <a href="https://codepen.io/chaeinp/pen/NWgKBpx">
grid_area</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
on <a href="https://codepen.io">CodePen</a>.

</iframe>

**_(7) grid-auto-rows/grid-auto-columns_**

> 컨테이너에 부여되는 속성으로 암시적 행, 열의 크기를 정의한다. `grid-template-rows`로 정의한 명시적 행, 열 외부에 배치되는 경우 암시적 행, 열의 크기가 적용된다.

<iframe height="350" width="100%" scrolling="no" title="grid-auto-rows/columns" src="https://codepen.io/chaeinp/embed/dyRbrwE?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/dyRbrwE">
  grid-auto-rows/columns</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

**_(8) grid-auto-flow_**

> 컨테이너에 부여되는 속성으로 배치하지 않은 아이템을 어떤 방식의 '자동 배치 알고리즘'으로 처리할지 정의한다.

| 값               | 의미                                      | 기본값 |
| ---------------- | ----------------------------------------- | ------ |
| row              | 각 행 축을 따라 차례로 배치               | auto   |
| column           | 각 열 축을 따라 차례로 배치               |
| row dense(dense) | 각 행 축을 따라 차례로 배치, 빈 영역 메움 |
| column dense     | 각 열 축을 따라 차례로 배치, 빈 영역 메움 |

<iframe height="350" width="100%" scrolling="no" title="grid-auto-flow" src="https://codepen.io/chaeinp/embed/OJgLGaO?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/OJgLGaO">
  grid-auto-flow</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

**_(9) grid-area_**

> 아이템에 부여되는 속성으로 `grid-row`와 `grid-column`의 단축 속성이다. 또는 `grid-template-areas`가 참조할 영역 이름을 설정할 수도 있다. 영역 이름을 설정할 경우 `grid-row`와 `grid-column`개념은 무시된다.

```css
.item{
  grid-area: <grid-row-start> / <grid-column-start> / <grid-row-end> / <grid-column-end>;
  혹은
  grid-area: 영역이름;
}
```

**_(10) grid-template_**

> 컨테이너에 부여되는 속성으로 `grid-template-rows`, `grid-template-column`, 그리고 `grid-template-areas`의 단축 속성이다.

```css
.container {
  grid-template: <grid-template-rows> / <grid-template-columns>;
  grid-template: <grid-template-areas>;
  grid-template:
    [1행시작선이름] 'AREAS' 행너비 [1행끝선이름]
    [2행시작선이름] 'AREAS' 행너비 [2행끝선이름]
    / <grid-template-columns>;
}
```

<iframe height="350" width="100%" scrolling="no" title="grid-template" src="https://codepen.io/chaeinp/embed/jOwNoVR?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/jOwNoVR">
  grid-template</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

**_(11) grid_**

> 컨테이너에 부여되는 `grid-template-xxx`와 `grid-auto-xxx`의 단축 속성

```css
.container {
  grid: <grid-template-rows> / <grid-template-columns>;
}

.container {
  grid: 100px 200px / 1fr 2fr;
}

.container {
  grid-template-rows: 100px 200px;
  grid-template-columns: 1fr 2fr;
}
```

```css
.container {
  grid: <grid-template>;
}

.container {
  grid:
    'header header header' 80px
    'main . aside' 350px
    'footer footer footer' 130px
    / 2fr 100px 1fr;
}

.container {
  grid-template:
    'header header header' 80px
    'main . aside' 350px
    'footer footer footer' 130px
    / 2fr 100px 1fr;
}
```

```css
.container {
  grid: <grid-template-rows> / <grid-auto-flow><grid-auto-columns>;
}

.container {
  grid: 100px 100px / auto-flow 150px;
}

.container {
  grid-template-rows: 100px 100px;
  grid-auto-flow: column;
  grid-auto-columns: 150px;
}
```

```css
.container {
  grid: <grid-auto-flow> <grid-auto-rows> / <grid-template-columns>;
}

.container {
  grid: auto-flow 150px / 100px 100px;
}

.container {
  grid-template-columns: 100px 100px;
  grid-auto-flow: row;
  grid-auto-columns: 150px;
}
```

```css
.container {
  grid: auto-flow dense 150px / 100px 100px;
}

.container {
  grid-template-columns: 100px 100px;
  grid-auto-flow: row dense;
  grid-auto-columns: 150px;
}
```

**_(12) align-content_**

> 컨테이너에 부여되는 속성으로 그리드 콘텐츠를 수직 정렬한다. 콘텐츠의 세로 너비가 컨테이너보다 작아야한다.

| 값            | 의미                                                                  | 기본값 |
| ------------- | --------------------------------------------------------------------- | ------ |
| normal        | stretch와 같습니다.                                                   | normal |
| start         | 시작점(위쪽) 정렬                                                     |
| center        | 수직 가운데 정렬                                                      |
| end           | 끝점(아래쪽) 정렬                                                     |
| space-around  | 각 행 위아래에 여백을 고르게 정렬                                     |
| space-between | 첫 행은 시작점에, 끝 행은 끝점에 정렬되고 나머지 여백으로 고르게 정렬 |
| space-evenly  | 모든 여백을 고르게 정렬                                               |
| stretch       | 열 축을 채우기 위해 그리드 콘텐츠를 늘림                              |

**_(13) justify-content_**

> 컨테이너에 부여되는 속성으로 그리드 콘텐츠를 수평 정렬한다. 콘텐츠의 가로 너비가 컨테이너보다 작아야한다.

| 값            | 의미                                                                  | 기본값 |
| ------------- | --------------------------------------------------------------------- | ------ |
| normal        | stretch와 같습니다.                                                   | normal |
| start         | 시작점(왼쪽) 정렬                                                     |
| center        | 수평 가운데 정렬                                                      |
| end           | 끝점(오른쪽) 정렬                                                     |
| space-around  | 각 열 좌우에 여백을 고르게 정렬                                       |
| space-between | 첫 열은 시작점에, 끝 열은 끝점에 정렬되고 나머지 여백으로 고르게 정렬 |
| space-evenly  | 모든 여백을 고르게 정렬                                               |
| stretch       | 행 축을 채우기 위해 그리드 콘텐츠를 늘림                              |

**_(14) align-items_**

> 컨테이너에 부여되는 속성으로 그리드 아이템을 수직(열 축)으로 정렬한다. 아이템의 세로 너비가 자식이 속한 그리드 행의 크기보다 작아야한다.

| 값      | 의미                                     | 기본값 |
| ------- | ---------------------------------------- | ------ |
| normal  | stretch와 같습니다.                      | normal |
| start   | 시작점(위쪽) 정렬                        |
| center  | 수직 가운데 정렬                         |
| end     | 끝점(아래쪽) 정렬                        |
| stretch | 열 축을 채우기 위해 그리드 아이템을 늘림 |

**_(15) justify-items_**

> 컨테이너에 부여되는 속성으로 그리드 아이템을 수평(행 축)으로 정렬한다. 아이템의 가로 너비가 자식이 속한 그리드 행의 크기보다 작아야한다.

| 값      | 의미                                     | 기본값 |
| ------- | ---------------------------------------- | ------ |
| normal  | stretch와 같습니다.                      | normal |
| start   | 시작점(왼쪽) 정렬                        |
| center  | 수평 가운데 정렬                         |
| end     | 끝점(오른쪽) 정렬                        |
| stretch | 행 축을 채우기 위해 그리드 아이템을 늘림 |

**_(16) align-self_**

> 아이템에 부여되는 속성으로 그리드 아이템을 수직(열 축)으로 정렬한다. 아이템의 세로 너비가 자식이 속한 그리드 행의 크기보다 작아야한다.

| 값      | 의미                                     | 기본값 |
| ------- | ---------------------------------------- | ------ |
| normal  | stretch와 같습니다.                      | normal |
| start   | 시작점(위쪽) 정렬                        |
| center  | 수직 가운데 정렬                         |
| end     | 끝점(아래쪽) 정렬                        |
| stretch | 열 축을 채우기 위해 그리드 아이템을 늘림 |

**_(17) justify-self_**

> 아이템에 부여되는 속성으로 그리드 아이템을 수평(행 축)으로 정렬한다. 아이템의 가로 너비가 자식이 속한 그리드 열의 크기보다 작아야한다.

| 값      | 의미                                     | 기본값 |
| ------- | ---------------------------------------- | ------ |
| normal  | stretch와 같습니다.                      | normal |
| start   | 시작점(왼쪽) 정렬                        |
| center  | 수평 가운데 정렬                         |
| end     | 끝점(오른쪽) 정렬                        |
| stretch | 행 축을 채우기 위해 그리드 아이템을 늘림 |

**_(18) order_**

> 아이템에 부여되는 속성으로 아이템이 html작성 순서에 따라 자동 배치되는 순서를 변경할 수 있다. 숫자가 작을수록 앞서 배치된다. 음수도 사용 가능하다. 기본값은 0이다.

```css
.item:nth-child(1) {
  order: 1;
}
```

**_(19) z_index_**

> 아이템에 부여되는 속성으로 아이템이 쌓이는 순서를 변경할 수 있다. 기본값은 0이다.

### grid functions

**_(1) repeat_**

> repeat 함수는 행/열 크기 정의를 반복한다. 반복되는 횟수와 행/열의 크기 정의를 인수로 사용한다. grid-template-rows와 grid-template-columns에서 사용한다.

```css
.container {
  grid-template-columns: repeat(반복횟수, 열 크기);
}
```

**_(2) minmax_**

> 행/열의 최소/최대 크기를 정의한다. 첫번째 인수는 최솟값이고 두번째 인수는 최댓값이다. grid-template-rows, grid-template-columns, grid-auto-rows, grid-auto-columns에서 사용한다. 일반 요소에 min-width나 max-width를 동시 지정하는 것과 유사하다.

```css
.container {
  grid-template-columns: minmax(100px, 1fr) minmax(200px, 1fr);
  /*컨테이너의 크기가 각아이템의 너비를 더한것보다 작아지면 아이템들은 오버플로우하게 된다.*/
}
```

**_(3) fit-content_**

> 행/열 크기를 그리드 아이템이 포함하는 콘텐트 내용의 크기에 맞춘다. 내용의 최대 크기를 인수로 사용한다. minmax(auto, max-content)와 유사하게 동작하지만 같진 않다.

```css
.container {
  grid-template-columns: fit-content(300px) fit-content(300px);
}
```

<iframe height="350" width="100%" scrolling="no" title="fit-content" src="https://codepen.io/chaeinp/embed/YzQKoQB?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/YzQKoQB">
  fit-content</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

### grid 단위

**_(1) fr_**

> fr은 사용 가능한 공간에 대한 비율을 의미한다 다음의 예제는 3번째 컬럼이 100px, 네번째 컬럼이 25%를 차지하고 남은공간을 1/3, 2/3만큼 사용한다.

```css
.container {
  grid-template-columns: 1fr 2fr 100px 25%;
}
```

**_(2) min-content / max-content_**

> 그리드 아이템이 포함하는 내용의 최소/최대 크기를 의미한다.

```css
.container {
  grid-template-columns: min-content 1fr;
}

.container {
  grid-template-columns: max-content 1fr;
}
```

<iframe height="350" width="100%" scrolling="no" title="min/max-content" src="https://codepen.io/chaeinp/embed/yLXBdEo?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/yLXBdEo">
  min/max-content</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

**_(3) auto-fill / auto-fit_**

> 행/열 개수를 컨테이너 및 행/열 크기에 맞게 자동으로 조정한다. repeat()함수와 같이 사용하며, 행/열과 아이템 개수가 명확할 필요가 없거나 명확하지 않은 경우 유용하다(반응형 그리드) auto-fill과 auto-fit은 간단한 차이점을 제외하면 동일하게 동작한다. auto-fill과 auto-fit의 차이는 컨테이너가 모든 아이템을 수용하고 남는 공간이 있을 때 발생한다. auto-fill은 남는 공간을 그대로 유지하고 auto-fit은 축소한다.

```css
.container {
  grid-template-columns: repeat(4, minmax(120px, 1fr));
}
```

<iframe height="350" width="100%" scrolling="no" title="auto-fill/fit" src="https://codepen.io/chaeinp/embed/abwogQa?default-tab=css%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/chaeinp/pen/abwogQa">
  auto-fill/fit</a> by chaeinP (<a href="https://codepen.io/chaeinp">@chaeinp</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>
