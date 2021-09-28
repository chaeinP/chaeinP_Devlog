---
title: Error Log_git add를 했는데도 파일/폴더가 unTracked 상태일 때
date: '2021-09-28'
tags: ['Error_Log'],['Project']
draft: false
summary: utterence github OAUTH는 canonical url을 중심으로 작동한다.
---

> 이 포스트는 토이프로젝트 'signup/signin 간단 구현' 에서 발생한 에러를 기록한 것입니다.

### 문제상황

로그인 기능 구현을 연습하기 위해 리액트로 클라이언트를, nodejs로 서버를 만들어 이를 루트 디렉토리안에 위치시켰다. 문제는 대략적인 화면 구성을 리액트로 구현한 후 깃에 커밋하려하자 client폴더 전체가 계속 untracked file로 깃 추적이 안되는 것이었다. 당연히 stage상태로 올리지 못해 커밋에도 포함되지 않았다. 관련하여 검색 중 client 폴더에 이미 .git 폴더가 존재할 경우 이를 깃에서 서브 모듈로 인식해 루트 깃과 별개로 작동할 수 있다는 것을 발견했다.

```sh
$ find . -name '.git'
./.git
./client/.git
```

find 명령어로 확인해보니 예상 대로 루트 폴더 내에 git폴더가 두 개 존재했다.

### 해결

> 서브 모듈로 인식 되고 있는 깃 폴더와 캐시를 삭제한다.

이 명령어는 루트 폴더의 git을 제외한 나머지 git 폴더를 찾아준다.

```sh
$ find . -mindepth 2 -name '.git'
```

이 명령어와 rm명령어를 사용해 하위 깃 폴더를 모두 삭제한다.

```sh
$ find . -mindepth 2 -name '.git' -prune -exec rm -rf {} +
```

깃 캐시가 남아있는 경우 add가 안되니 깃 캐시도 삭제해준다.

```sh
git rm --cached 폴더이름 -f
```

이렇게 하고 `git add 폴더이름`을 입력했더니 정상적으로 작동했다.

참조

- [하위 .git 폴더 제거](https://zetawiki.com/wiki/%ED%95%98%EC%9C%84_.git_%ED%8F%B4%EB%8D%94_%EB%AA%A8%EB%91%90_%EC%A0%9C%EA%B1%B0)
