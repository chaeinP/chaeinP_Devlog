---
title: Error Log_<body> height의 비밀
date: '2021-07-14'
tags: ['Error_Log']
draft: false
summary: body에서 height는 어떻게 변할까?
---

### 1. 문제 상황

노마드코더의 리액트 영화앱 클론 도중 body height가 원하는 대로 작동하고 있지 않는 상황을 마주했다. body 내부 후손요소인 `<div>`에 위 아래 padding 값을 주었는데 padding-top은 먹고 padding-bottom은 작동하지 않는 이슈였다. 때문에 아래 사진 처럼 웹하단부 컨테이너가 마진없이 웹 하단에 딱 붙어버렸다.
![](https://images.velog.io/images/pca0046/post/fdf7f594-b00f-4bf7-940c-c5f6562178f6/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-07-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%209.07.21.png)
문제의 원인을 찾던 도중 해당 `<div>`의 조상 요소인 `<body>`가 내부 콘텐츠 길이에 따라 height가 변하지 않고 뷰포트 사이즈 기준으로 fix되어있다는 사실을 발견했다. 이로 인해 height가 %로 책정되어있던 자식 요소들의 기준이 모두 뷰포트 사이즈가 되면서 콘텐츠가 오버플로우 되고 있는 상황이었다. 이 때문에 padding-bottom도 원하는 위치에서 동작하고 있지 않았다.

![](https://images.velog.io/images/pca0046/post/6e90085e-72cb-48a5-9b0c-0ba9cb292548/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-07-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%209.23.17.png)

당시 내 css 코드는 이러했다.

```css
html,
body,
#root,
.container {
  height: 100%;
  display: flex;
  justify-content: center;
}

body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell,
    'Open Sans', 'Helvetica Neue', sans-serif;
  /* background-color: #eff3f7; */
  align-items: center;
}
```

html에 height값을 100%주었을 때 나는 html의 높이가 브라우저 전체 길이를 포함할 것으로 기대했다. body에 100% height 값을 주었던 것도 같은 맥락이다. 그러나 html과 body height는 뷰포트 사이즈에 고정되어버렸다. 구글링과 갖가지 실험이 시작됐다.

### 2.알게된 몇 가지 사실 정리

#### (1) html width와 height의 %단위 값은 viewport를 기준으로 한다. (Internet Explore 에서는 다르게 작동되는 듯 하다.)

여러 문서들을 살펴본 결과 최근 브라우저 단에서 html 사이즈를 viewport로 기준하는 것으로 변화가 있었던 듯 하다. 이는 웹브라우저의 모바일 화면 최적화와 연관이 있어보이는데 구체적인 이유는 찾지 못했다.  
[Make body Take Up 100% of the Browser Height(참조)](https://www.kirupa.com/html5/make_body_take_up_full_browser_height.htm)

#### (2) 부모요소 height값이 부재하면 자식 요소 height의 %는 무시되고 auto로 작동한다.

[html and body element - height:100% ?(참조)](https://codepen.io/absolutholz/post/html-and-body-element-height-100)

#### (3)html background-color는 width나 height 크기에 상관없이 브라우저 전체를 포함한 영역에 적용된다.

![](https://images.velog.io/images/pca0046/post/9620baf9-ab60-43fc-8e9d-ed1e3c2eec78/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-07-13%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2010.44.25.png)

```css
html {
  height: 100%;
  background-color: blue;
}
```

문제의 원인을 찾기 위해 박스 모델 하나하나에 색상값을 넣어 확인하고 있었는데 위 코드처럼 html에 먹인 blue 색상이 브라우저 전체 크기에 적용되고 었다. 이 때문에 처음에 html은 브라우저 전체 높이를 가지고 있고 body만 뷰포트 기준으로 height가 잡혀있는 줄 알았다. 그러나 코드펜에서 이리저리 실습을 해본 결과 html의 background-color는 width나 height값이 달라져도 언제나 브라우저 전체에 적용됨을 확인했다.

### 3. 문제 해결 : min-height 사용

[CSS: Do not put height 100% on html, body in 2020(참조)](https://greggod.medium.com/css-do-not-put-height-100-on-the-body-html-e36bda3551b3)
body의 height가 브라우저 전체를 가지면서 콘텐츠의 길이에 맞게 자동으로 업데이트 되게 하기 위해선 height가 아니라 min-height를 사용하면 된다. min-height를 사용하면 최솟값을 뷰포트 기준으로 그 이후에는 콘텐츠 길이가 늘어남에따라 auto로 작동한다. 따라서 내가 처음에 의도 했던 대로 body height를 작동시킬 수 있다. 다만 Internet Explore에선 min-height가 지원되지 않는 것으로 알고 있다. 따라서 height만 써도 된다고 한다. Internet Explore에서 html height는 뷰포트 기준으로 움직이지 않는다고 하니 기존에 min-height를 사용하지 않았던 내 이전 코드는 Explore 웹브라우저에서는 의도한대로 동작하나보다.(해보진 않았음)

```css
html,
#root,
.container {
  height: 100%;
  display: flex;
  justify-content: center;
}

body {
  min-height: 100%;
  display: flex;
  justify-content: center;
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell,
    'Open Sans', 'Helvetica Neue', sans-serif;
  /* background-color: #eff3f7; */
  align-items: center;
}
```

> 정확하지 않은 내용이 들어있을 수 있습니다. 그러한 부분이 있다면 지적해주시고 사실을 알려주세요:)
