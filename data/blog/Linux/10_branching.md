---
title: Linux_Branching
date: '2021-09-21'
tags: ['Linux']
draft: false
summary: shell에서 branching 수행하는 대표적인 명령어
---

> 이 포스트는 [[따배셸] 8. Branching](https://www.youtube.com/watch?v=hG9T3K00qiE&list=PLApuRlvrZKog2XlvGJQh9KY8ePCvUG7Je&index=9) 학습자료를 보고 공부한 기록입니다.

### exit

모든 명령어는 종료될 때 종료코드를 발생하도록 되어있다. 이 종료코드를 직접 설정해줄 수도 있다.

- 이전 명령어의 종료 코드 확인하는 법

```sh
$ echo $?
#$?에는 항상 이전 명령어의 종료 코드가 담긴다.
```

종료 코드가 0이명 정상 종료 0 이외의 값이면 정상적으로 명령어가 실행되지 않았다는 의미이다.

- 종료 코드 종류
  숫자 | 의미
  -|-
  0 | 프로그램 또는 명령이 성공으로 종료했음을 의미
  1 | 일반에러
  2 | Syntax error
  126 | 명령을 실행할 수 없음
  127 | 명령(파일)이 존재하지 않음
  128+N | 종료 시그널 +N(kill -9 PID로 종료시 128+9 = 137)

### 비교 연산자 test

> 명령어 실행결과를 true(0) 또는 false(1)로 리턴한다.
> test <명령어> or [ 명령어 ](대괄호의 경우 내부 앞뒤 띄어쓰기 주의)

연산자 종류
연산자 | 설명
-|-
x -eq y | x = y
x -gt y | x > y
x -ge y | x >= y
x -lt y | x < y
x -le y | x <= y
x -ne | x != y
-e file | 파일이 존재하는지 여부
-d file | 파일이 디렉토리인지 여부
-f file | 파일이 디렉토리인지 여부
-x file | 파일이 디렉토리인지 여부

```bash
$ x=10
$ test $x -lt 5
$ echo $?
1

$ test $x -gt 5
$ echo $?
0

$ test -e /etc/passwd
$ echo $?
0

$ [ $x -lt 5 ]
$ echo $?
1

$ [ -f /tmp ]
$ echo $?
1
```

### 산술 명령어 let

```bash
$ let sum=5+5
$ echo $sum
10
```

### if~then~else~fin

```bash
$ x=10
$ if test $x -gt 5
then
  echo "x is greater than 5"
fi
$ echo $?
0

$ if [ -e /etc/passwd ]
then
  ls -l /etc/passwd
else
  echo "/etc/passwd file does not exist"
fi
```

### case

```bash
$ echo -n "What do you want?"
read answer
case $answer in
  [yY]es) echo "System restart.";;
  [nN]o) echo "shutdiwn the system";;
  *) echo "entered incorrectly";;
esac
```

### example

```bash
#!/bin/zsh
cat << END # END나오기 전까지 한번에 출력하라는 의미
======================
Please select a number
----------------------
1: Check disk usage
2: Check the login user list
---------------------
END
echo -n "number: "
read number
case $number in
  1) df -h ;;
  2) who ;;
  *) echo "Bad choice";
esac
```
