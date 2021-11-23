---
title: C_file descriptor(fd)
date: '2021-11-22'
tags: ['C', '42Seoul']
draft: false
summary: 파일 디스크립터 만들기
---

> 이 포스트는 42Seoul 본과정 get_next_line 과제시 공부했던 내용을 기록한 스터디로그입니다.

## fd란?

## read()

```c
//헤더
#include <unistd.h>
//프로토타입
ssize_t read(int fildes, void *buf, size_t nbyte);
```

- ssize_t 자료형은?
