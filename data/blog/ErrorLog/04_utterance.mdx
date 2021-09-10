---
title: Error Log_utterence 도입 중 github OAUTH가 제대로 작동하지 않을 때
date: '2021-09-10'
tags: ['Error_Log']
draft: false
summary: utterence github OAUTH는 canonical url을 중심으로 작동한다.
---

### 문제상황

이번에 기술 블로그를 벨로그에서 next.js 기반 블로그로 옮겨오면서 새롭게 댓글 기능을 추가하고자 하였다. 내가 선택한 오픈 소스는 [utterence](https://utteranc.es/) 였는데 깃허브 OAUTH와 레포 연동으로 작동한다. utterence에 안내된 모든 과정에 따라 설치를 했는데 댓글 UI창까지 정상적으로 삽입되었으나 OAUTH 페이지로 이동하는 과정에서 계속 404 페이지가 떴다.

### 해결

> 블로그의 canonical태그 url이 블로그 실제 url 주소와 달라서 발생한 문제!

canonical 태그를 알기 전엔 먼저 중복 url 개념을 알아야 한다.

💡 <b>중복 url</b>

중복 url 은 구글어 검색 순위 결정 요소를 설명할 때 사용하는 용어로 다음과 같다.

> 여러 개의 URL로 액세스할 수 있는 하나의 페이지가 있거나 콘텐츠가 유사한 서로 다른 페이지(예: 모바일 버전 및 데스크톱 버전이 있는 페이지)가 있는 경우, Google은 이러한 페이지를 같은 페이지의 중복 버전으로 간주합니다. Google은 URL 중 하나를 표준 버전으로 선택하여 크롤링하고, 나머지 모든 URL은 중복 URL로 간주하고 빈도를 줄여 크롤링합니다.

만약 내 웹사이트 주소가 중복 url 로 간주 될 경우 검색 엔진 최적화에 악영향을 미칠 수 있다. 이때 검색봇에 이 사이트의 표준 url이 무엇인지 정확하게 알려주는 태그가 바로 Canonical 태그이다. 만약 Canonical 태그가 없으면 검색엔진은 사용자 대신 임의의 기준에 맞춰 표준 버전을 선택한다. 따라서 Canonical 태그에 사이트의 표준이 될 url을 명시하면 검색 엔진 최적화에 도움이 된다.

```html
<link rel="canonical" href="표준 url" />
```

다시 utterence 에러에 대한 이야기로 돌아오면 utterence는 oauth 연결을 canonical url을 기준으로 처리하는데 이게 실제 사이트 url과 다르면 에러가 발생한다. 나도 개발당시 테스트 url을 canonical url에 적어놓은 것을 깜빡 잊고 있었는데 다시 살펴보니 배포 이후 url과 canonical url이 상이했다. 이를 같은 주소로 맞춰주자 OAUTH가 정상적으로 작동했다.

<Image alt="Maple" src="/static/images/blog/utterance.png" width="800" height="320" />

문제 해결

[참조: JekyII에 Utterances 댓글적용하기](https://www.hahwul.com/2020/08/08/jekyll-utterances/)
