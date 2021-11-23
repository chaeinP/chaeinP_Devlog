---
title: C_file descriptor(fd), read함수
date: '2021-11-22'
tags: ['C', '42Seoul']
draft: false
summary: 파일 디스크립터 만들기
---

> 이 포스트는 42Seoul 본과정 get_next_line 과제를 위해 공부한 내용을 기록한 스터디로그입니다.

## fd란?

## read()

```c
//헤더
#include <unistd.h>
//프로토타입
ssize_t read(int fildes, void *buf, size_t nbyte);
```

- ssize_t 자료형은?

  size_t가 음이 아닌 정수형을 표현한다면 ssize_t는 모든 정수형을 의미한다. size_t가 unsignd int랑 비슷하다면 ssize_t는 int랑 비슷한 셈
