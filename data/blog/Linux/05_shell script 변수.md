---
title: Linux_Shell Script 변수
date: '2021-09-21'
tags: ['Linux']
draft: false
summary: 쉘 스크립트로 일반변수, 환경변수 만들기
---

## 일반변수

### 변수 만들기

> [변수명]=[변수값] (띄어쓰기 주의할 것)

```sh
fname=chaein
```

### 변수 확인

> echo $변수이름

```sh
echo $fname
```

### 변수 목록 보기

> set (grep을 통해 필터링)

```bash
set|grep fname
```

### 변수 제거

> unset 변수명

```bash
unset fname
```

## 환경변수

> 동작 되는 프로그램에 영향을 주는 변수, 이후에 실행되는 애플리케이션에서 사용할 수 있다.

### 환경 변수 설정

> export varName=value

```bash
export NAME=chaein
echo $NAME(변수 확인)
```

### 환경 변수 목록

> env

```bash
env | grep NAME(대소문자 구분할 것)
```

### 기억해야할 환경변수

| 환경변수 | 값                 |
| -------- | ------------------ |
| PATH     | 명령어 탐색 경로   |
| HOME     | 홈디렉토리 경로    |
| USER     | 로그인 사용자 이름 |
| SHELL    | 로그인 shell 이름  |

✅ 환경변수는 덮어쓰기가 가능하기 때문에 위처럼 중요한 환경변수들은 덮어쓰지 않도록 유의해야한다.
