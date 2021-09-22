---
title: Linux_looping
date: '2021-09-22'
tags: ['Linux']
draft: false
summary: shell에서 looping 수행하는 대표적인 명령어
---

> 이 포스트는 [[따배셸] 9. looping](https://www.youtube.com/watch?v=SChc2ye4gcg&list=PLApuRlvrZKog2XlvGJQh9KY8ePCvUG7Je&index=10) 학습자료를 보고 공부한 기록입니다.

### 산술연산명령어 expr / let

> expr과 let 은 둘다 산술연산을 지원하지만 expr은 기본 명령어로 모든 쉘에서, let은 bash 쉘 내에서만 작동한다.

| 연산자           | expr                  | let              |
| ---------------- | --------------------- | ---------------- |
| 정수형 산술 연산 | (+, -, \*, /, %)      | expr과 동일      |
| 논리연산         | ( \| , &)             | ( \|\| , &&)     |
| 관계연산         | (=, !=, >, >=, <, <=) | -                |
| bit 연산         | -                     | (<<, >>, &, \| ) |
| 단항 연산        | -                     | (++, +=, -=)     |

```sh
expr 10 + 5
expr 10 - 5
expr 5 '*' 2 # *는 따옴표로 감싸줘야 한다.
expr 25 '/' 5 # /도 따옴표로 감싸줘야 한다.
expr 25 % 4
x=5
expr $x > 4
expr $x = 8
sum=`expr $x + 10`
type expr
```

```sh
let sum=x+5 # expr과 비교하였을 때 연산자 앞뒤로 블랭크가 없고 $()이나 ``로 갑싸주지 않아도 된다.
x=1
let x=x+1
let x++
((x=x+1)) # let으로 한 것과 결과는 같다.
```

### while and until

- while : 조건 명령이 성공하는 동안 do ~ done사이의 명령어 반복 실행

  ```bash
  $ num=1
  while test $num -le 5
  do
    echo Number:$num
    ((num++))
  done

  Number:1
  Number:2
  Number:3
  Number:4
  Number:5

  ```

  예제

  ```bash
  $ num=0
  while [ $num -lt 5 ]
  do
    echo "Number:$num"
    ((num++))
    if [ "$num" = '2' ]
    then
      break
    fi
  done
  Number:0
  Number:1
  ```

- until : 조건 명령이 성공할 때 까지 반복 실행

  ```bash
  $ num=1
  until test $num -lt 5
  do
    echo Number:$num
    ((num++))
  done
  # 아무것도 출력되지 않음. 이미 처음부터 조건이 충족된 상태

  $ num=1
  until test $num -gt 5
  do
    echo Number:$num
    ((num++))
  done

  Number:1
  Number:2
  Number:3
  Number:4
  Number:5
  ```

- for-loop
  > 주어진 list 만큼 do~done 사이의 명령어 반복 실행

```sh
for NUM in 1 2 3 4 5
do
  echo -n $NUM
done
12345

for NUM in $(seq 4)
do
  echo -n $NUM
done
1234

for file in *
do
  ls $file
done
# 현재 디렉토리내에 모든 파일이 ls명령으로 실행된다.

if [ ! -d ~/backup ]
  then mkdir ~/backup
fi
for FILE in *
do
  cp $FILE ~/backup/$FILE.old
done
```
