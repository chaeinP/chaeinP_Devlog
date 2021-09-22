---
title: Linux_Input&Output
date: '2021-09-18'
tags: ['Linux']
draft: false
summary: shell 스트립트에서 input과 output을 수행하는 대표적인 명령어
---

### echo

> 모니터에 문자를 출력하는 명령어
> echo 옵션 메시지

- `-n` : 메시지 출력후 newline 문자를 추가하지 않는다.
- `-e` : backslash excapes문자를 해석하여 특별한 의미를 지정한다.
  backslash excapes |
  -|
  \t : TAB키
  \n : 줄 바꿈
  \a : alert(bell)

```sh
$ echo "Your time is up"
Your time is up
$ echo "Your time is up" > time.txt
$ echo -n "Name:"; echo c
Name:c
$ echo -e "First\tSecond"
First Second
```

### read

> 데이터를 입력받는 명령어. 변수명을 생략하면 입력값은 REPLY 변수에 자동으로 할당된다.
> read 옵션 변수명

- `-n` : 지정한 문자수만큼 입력 받는다.
- `-t` : 지정된 시간안에 입력 받는다.
- `-s` : silent mode로 입력하는 글자가 보이지 않는다.

```sh
$ read name
kim #사용자가 입력한 값
$ echo $name
kim #read 명령어로 사용자가 입력한 값이 변수에 담김
$ read firstname lastname
kim co ding #변수는 2개 데이터는 3개인 상황
$ echo $firstname
kim
$ echo $lastname
co ding #띄어쓰기 기준 변수 개수만큼 데이터를 분배하고 남는 데이터는 마지막 변수에 몰아넣는다.
$ read -t5 -n5 name #5초안에 5글자 이내를 입력해야한다. 입력하지 않으면 빈문자열이 name에 할당
$ read -s pass
#입력값이 보이지 않는다. password 같은 데이터를 이렇게 입력받을 수도 있다.
$ echo -n "your name: "; read name
your name : 데이터입력
$ echo -n "continue(y/n)? "; read answer
continue(y/n)? 데이터 입력
# 이후 if문으로 분기 명령을 실행할 수 있게 된다.
```

### printf

> 서식에 맞춰서 출력, C언어의 printf와 동일
> printf format 메시지

| format   | 기능        |
| -------- | ----------- |
| %d or %i | 숫자        |
| %s       | 문자열      |
| %f       | 실수형 숫자 |

```bash
$ printf "Name: %s \t Score: %i\n" coding 25
Name: coding   Score: 25
$ today=`date +%Y%m%d`
$ printf "date is %s\n" "$today"
date is 20210921

#각각 주어진 10칸 안에서 왼쪽, 오른쪽 정렬된 모습
$ printf "|%10s|%10s|%10.2f|\n" ubuntu coding 10
|    ubuntu|    coding|     10.00|
printf "|%-10s|%-10s|%-10.2f|\n" ubuntu coding 10
|ubuntu    |coding    |10.00     |
```
