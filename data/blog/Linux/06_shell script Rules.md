---
title: Linux_Shell Script Rules
date: '2021-09-15'
tags: ['Linux']
draft: false
summary: 쉘 스크립트가 가지고 있는 규칙들
---

### Metacharacters

> shell에서 특별히 의미를 정해놓은 문자들

- `*` : All characters

  ```bash
  echo a*
  # a로 시작하는 모든 글자
  ```

- `?` : Any Single Character

  ```bash
  echo ????
  # 네글자로 이루어진 모든 문자
  ```

- `{}` : 집합과 같은 의미

  ```bash
  touch myfile{1..3}
  # myfile1 myfile2 myfile3 생성
  ```

- `[]` : 문자의 범위를 지정

  ```bash
  # a에서부터 z까지 모든 문자 중에 하나를 가지는 파일 출력
  ls [a-z]*

  # 0에서 9까지 모든 문자 중에 하나를 가지는 파일 출력
  ls [0-9]*

  # a와 b를 포함하는 파일 출력
  ls [ab]*
  ```

- `#` : 주석

- `&` : 백그라운드 실행

- blank : command와 arguments와 options을 분리

## Quoting Rule

> 메타 문자의 의미를 제거하고 단순 문자로 변경

- `\` : 바로 뒤 메타 문자의 특별한 의미를 제거
- `""`: `""`내 모든 메타 문자의 의미 제거, 단 `$`와 `백틱`은 제외
- `''`:`''`내 모든 메타 문자의 의미 제거

## nesting commands

> 명령어 안 명령어 실행

```bash
echo 'Today is date'
# date라는 명령어 실행없이 'Today is date' 출력

echo 'Today is $(date)'
# Today is (date 명령어 실행 결과) 출력
echo 'Today is `date`'
# 위와 동일한 결과

touch report-$(date +%Y%m%d)_v1
# report-년월일_v1 파일 생성
```

## alias

> 명령에 새로운 이름을 부여, 명령의 조합에도 별칭 부여 가능

- alias 등록

  > alias name='command'

  ```bash
  alias c=clear
  alias rm='rm -i'
  # blank가 포함된 명령어는 ''로 묶어주어야 한다.
  ```

- alias 확인

  > alias 또는 alias name

- alias 삭제
  > unalias name

## prompt

> 프롬프트 설정을 위해선 escape character들을 바탕으로 설정해야 한다.

- 대표적인 escape characters
  특수문자 | 의미
  -|-
  \h | 호스트이름
  \u|사용자이름
  \w|작업 디렉토리 - 절대경로
  \W|작업 디렉토리 - 상대경로
  \d | 오늘 날짜
  \t | 현재 시간
  \$ | $ 또는 # 프롬프트 모양

- prompt 확인

  > echo $PS1

- prompt 변경
  > PS1=원하는\_prompt모양

💡 환경변수나 alias등록과 같은 설정은 서버가 종료되면 함께 휘발된다. 영구적으로 해당 설정을 저장하고 싶다면 .bashrc나 .zshrc 파일 안에 기록해야한다.

## redirection

이전에 작성한 [IO redirection](https://chaeinp.vercel.app/blog/Linux/03_IO%20redirection) 포스트 참고

## pipeline

> 명령의 실행결과를 다음 명령의 입력으로 전달하는 것

```bash
man ls | grep sort
```

`|`는 두개의 명령어를 잇는 역할을 한다. man ls로 출력된 데이터를 grep sort로 넘겨줘서 처리하고 있다. 이에 따라 ls manual중 sort를 포함한 행만 출력된다.

```bash
# sort가 포함되어 있으면 file이 포함되어있는 행 출력
man ls | grep sort | grep file
```
