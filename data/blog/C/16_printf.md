---
title: C_printf 구현
date: '2022-01-16'
tags: ['C', '42Seoul']
draft: false
summary: printf 최소 기능 직접 구현하기
---

> 이 포스트는 42Seoul 본과정 ft_printf 과제를 위해 공부한 내용을 기록한 스터디로그입니다.

## printf

printf 는 `<stdio.h>`헤더 파일에 선언되어있는 함수이다. 다음의 형태로 정의되어있다. 반환값은 출력된 문자열의 수이다.

```
int printf(const char *restrict format, ...)
```

printf format은 다음과 같다.

```
%[parameter][flags][width][.precision][length]type
```

### parameter

POSIX에서만 지원되는 필드다. C99에는 없다. `n$`로 n에는 숫자가 들어가고 출력되는 순서를 조절할 수 있는 기능이다.

```c
printf("%2$d %2$#x; %1$d %1$#x",16,17)
//17 0x11; 16 0x10
//만약 n$이 없었다면 16 0x10이 먼저 출력되어야하지만 n$로 출력 순서를 지정해 17과 16의 출력 순서가 변경되었다.
```

선택 옵션이며 생략해도 무방하다. non_POSIX에서는

### flags

생략가능
| character | description |
| --------- | ------------------------------------------------------------------------------------------------------------ |
| `-` | 출력을 왼쪽 정렬한다. (기본값은 오른쪽 정렬이다.) |
| `+` | 양수일 때 +, 음수일 때 -표시를 추가한다. (기본값은 양수 앞에 아무것도 추가하지 않는다.) |
| ` `(공백) | 양수일 땐 공백, 음수일 땐 -를 추가한다. (ex. +100 => 100) |
| `0` | width 옵션이 지정되면 숫자 타입에 대해선 0을 앞에 추가한다. |
| ` | 정수 또는 십진수의 경우 천단위 기준으로 ,를 표시한다. (ex. 1,000) | |`#` | 진법 형식에 맞게 0, 0x 0X를 함께 출력 한다. 사용시에는 #o, #x, #X로 사용한다.(ex. printf("%#o", 20); => 024) |

### width

생략가능
출력할 문자열의 최소 길이를 의미한다. 출력할 문자열이 width보다 길면 무시된다. \*를 사용하면 width를 동적으로 입력할 수 있다.

```c
printf("%05d", 12);
// 00012
printf("%0*d", 5, 12);
// 00012
```

### .precision

생략가능
precision의 의미는 conversion에 따라 달라진다. \*를 사용하면 동적으로 인자를 받을 수 있다. 만약 동적으로 받은 인자가 음수이면 precision을 지정하지 않은 것과 같다.

- `d, i, o, u, x, X`: 출력 문자 개수, 만약 input 숫자 길이가 더 작으면 앞에 0이 추가된다.
- `a, A, e, E, f, F` : 소수점 이후에 오는 숫자의 개수를 의미하며 반올림 되어 나타난다.
- `g, G`: 유효숫자의 개수
- `s`: 바이트 최대 숫자

```c
printf("%.2f", 12.015);
// 12.02
printf("%.3s", "abcd");
// abc
printf("%.*s", 3, "abcd");
//abc
```

### length.

length modifier가 존재한다면 표시될 아이템의 형식이 특정 conversion specification의 일반적인 형식보다 길거나 짧다는 것을 나타낸다. (예를 들어, %d는 보통 int 값을 표시하고, %hd는 short int를, %ld는 long int를 표시할 때 사용된다.)

![Length modifier](https://t1.daumcdn.net/cfile/tistory/2276194658544EE12B)
출처: [프로그래밍 공부노트](https://ziegler.tistory.com/88)

### Conversion specifier

![Conversion specifier](https://t1.daumcdn.net/cfile/tistory/246AB546585460CB03)
출처: [프로그래밍 공부노트](https://ziegler.tistory.com/88)

## 과제 요구사항

Mandatory

- 다음의 Conversion specifier를 구현한다.
  - %c, %s, %p, %d, %i, %u, %x, %X, %%

Bonus

- `-0# +` 플래그 구현
- .precision 구현
- minimum width field 구현

## 가변인자

## 바이트 패딩

참고자료 : [위키피디아](https://en.wikipedia.org/wiki/Printf_format_string)
