---
title: Linux_쉘(shell)과 커널(kernel)
date: '2021-09-20'
tags: ['Linux']
draft: false
summary: 쉘과 커널 기초
---

![](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F27552535590AB2BB0F)

- hardware

  : 메모리, ssd, cpu와 같이 컴퓨터를 구성하는 물리적인 장치들을 의미한다.

- kernel

  : 커널(kernel)은 컴퓨터의 운영 체제의 핵심이 되는 컴퓨터 프로그램의 하나로, 시스템의 모든 것을 완전히 통제한다.

- shell

  : 컴퓨터 사용자가 shell에 명령을 입력하면 shell은 해당 명령은 kernel이 이해할 수 있는 언어로 바꾸어 전달하는 인터페이스

## shell과 관련한 명령어

- 운영체제 내 사용가능한 shell 종류 확인하는 법

```s
cat /etc/shells
```

- 현재 사용중인 shell 확인하기

```sh
echo $SHELL
```

- 기본 shell 변경하기

```s
sudo chsh 사용자_이름
```
