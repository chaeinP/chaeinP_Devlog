---
title: Vim_단축키 모음
date: '2021-10-02'
tags: ['vim']
draft: false
summary: Vim으로 개발하기
---

> 이 포스트는 [Vi (Vim) 시작하기](https://www.youtube.com/watch?v=GWo_MxMlJJ4)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 모드 변경

- i : INSERT 모드로 변경
- esc : INSERT 모드에서 명령 모드로 변경
- a : 명령모드에서 커서 다음에 바로 작성할 수 있도록 커서 이동 후 INSERT모드로 변경
- o : 명령모드에서 커서 다음줄에 바로 작성할 수 있도록 커서 이동 후 INSERT 모드로 변경
- shift + i : 명령모드에서 커서가 있는 줄 맨 앞에서 바로 작성할 수 있도록 커서 이동 후 INSERT 모드로 변경
- shift + a : 명령모드에서 현재 라인에서 가장 뒤에서 바로 작성할 수 있도록 커서 이동 후 INSERT 모드로 변경
- cw : 명령모드에서 커서가 위치한 단어를 지우고 INSERT 모드로 작성할 수 있도록 변경

## 커서 위치 이동

- h, j, k, l : 명령모드에서 좌하상우 방향키
- shift + h : 화면 맨위
- shift + l : 화면 맨 아래
- shift + m : 화면 중간
- ctrl + u , d : 반페이지씩 스크롤
- w, b : 단어 단위로 좌우 이동
- `{, }` : 문단 단위로 좌우 이동
- shift + $ : 명령모드에서 커서를 커서가 있는 줄 맨 뒤로 이동
- shift + ^ : 명령모드에서 커서를 커서가 있는 줄 맨 앞으로 이동
- ctrl + f : 보이는 화면의 다음 라인으로 커서 이동
- ctrl + b : 보이는 화면의 이전 첫라인으로 커서 이동

## 드래그

- v : 커서가 위치한 곳에서 상하 좌우 이동한만큼 드래그
- shift + v : 줄 선택 모드 (V-Line 모드), 방향키를 더해 선택할 줄을 조절할 수 있음
- `<>` : 줄 선택후 들여쓰기 내어쓰기
- y : 줄선택 모드에서 선택된 줄을 복사
- yy : 한 줄만 복사, 복사하고 싶은 라인에 커서를 두고 yy연타
- p : 복사한 줄 붙여넣붙 (내부 복붙)
- = : v-line모드에서 정리할 라인을 선택한뒤 = 키를 눌러 코드 줄을 정리한다.

## 삭제

- x : 문자 하나만 삭제, 숫자와 같이 누르면 해당 숫자만큼 삭제
- d : 커서가 위치한 문자 삭제, 주로 드래그와 함께 쓴다.
- dd : 커서가 올려져있는 줄 삭제, 앞에 삭제하고 싶은 줄 라인 수를 입력하고 dd를 누르면 입력한 수만큼 줄 삭제
- shift + d : 커서가 있는 줄, 커서가 있는 위치 이후 글자들 모두 삭제

## 검색

- shift + 8 : 명령모드에서 커서가 위치해있는 단어와 동일한 단어들이 어디에 있는지 찾아준다.
  - n : 다음 단어로 이동
  - N : 이전 단어로 이동

## 그외 편집키

- shift + j : 커서위치 다음줄을 그 전 줄 끝에 이어붙임
- ctrl + v : 내외부 붙여넣기, 입력모드에서 사용해야한다.
- =% : 중괄호 위에서(닫는거 여는거 상관없음) = 누르고 % 누르면 해당 코드 블록 안 코드가 정리된다.
- u : undo
- ctrl + r : redo

## :명령어

- /단어 : 단어를 입력하면 해당하는 단어들을 찾아준다. shift 8과 동일한 기능
- :숫자 : 입력 숫자에 해당하는 라인으로 바로 이동
- :set nonumber : 라인 숫자 삭제
- :set number : 라인 숫자 보여주기
- :set paste : 내외부 붙여넣기시 insert모드를 paste모드로 넣으면 코드 정리된채로 붙여넣기 가능
- :set nopaste : 붙여넣기 후에는 해당 설정을 해제해주어야한다.
- :%s찾을문자열/바꿀문자열 : 찾을 문자열을 모두 바꿀 문자열로 바꿔준다.
- :r 파일이름 : 해당 파일내용을 자동으로 복붙해줌
- :wq : 저장하고 나가기
- :w : 저장
- :q! : 저장안하고 나가기
- :쉘명령어도 사용 가능
