---
title: C_주요 라이브러리 함수
date: '2021-11-08'
tags: ['C']
draft: false
summary: 주요 라이브러리 함수 약 30개를 분석해보자
---

## isalpha

> 주어진 문자가 알파벳이면 0이 아닌 수, 알파벳이 아니면 0을 리턴하는 함수. 컴파일러나 버전에 따라 리턴값이 다르다. **보통 아스키 코드표 'A-Z'(65-90)에 해당하는 문자가 들어오면 1, 'a-z'(97-112)에 해당하는 문자가 들어오면 2를 반환한다.**

```c
// 헤더
#include <ctype.h>

// prototype
int isalpha(int c);
```

## isdigit

> 주어진 문자가 '0 ~ 9'인지 판별하는 함수, 숫자면 0이 아닌 수, 숫자가 아니면 0을 리턴한다.

```c
// 헤더
#include <ctype.h>

// prototype
int isdigit(int c);
```

## isalnum

> 주어진 문자가 알파벳 또는 숫자면 0이 아닌 수를, 그 외엔 0을 리턴한다.

```c
// 헤더
#include <ctype.h>

// prototype
int isalnum(int c);
```

## isascii

> 주어진 문자가 아스키코드표 기준 0과 127사이에 존재하면 0이 아닌 수를, 그렇지 않으면 0을 리턴한다.

```c
// 헤더
#include <ctype.h>

// prototype
int isalnum(int c);
```

## isprint

> 주어진 문자가 ascii decimal 32 ~ 126 사이의 문자면 0이 아닌 수를, 그렇지 않으면 0을 리턴한다.

```c
//헤더
#include <ctype.h>

//prototype
int isprint(int c);
```

## strlen

> 주어진 문자열의 길이를 반환한다.

```c
#include <string.h>

size_t strlen(const char *s);
```

## memset

> memset은 메모리의 시작지점 부터 주어진 사이즈까지 어떤 값(바이트 단위)으로 모두 초기화 할 때 사용한다. 첫번째 인자를 리턴한다.

```c
#include <string.h>

void *memset(void *b, int c, size_t len);
```

`b` : 채우고자 하는 메모리 주소
`c` : 메모리에 채우고자하는 값. int형이지만 내부에서는 unsigned char(1 byte)로 변환되어서 저장
`len` : 채우고자 하는 바이트 수

## bzero

> 메모리의 시작지점부터 주어진 크기만큼 0으로 초기화한다.

```c
#include <strings.h>
void bzero(void *s, size_t n);
```

## memcpy

> 메모리의 값을 정해진 크기만큼 복사한다.

```c
#include <string.h>

void *memcpy(void *restrict dst, const void *restrict src, size_t n);
```

src에 있는 값을 n길이 만큼 dst에 복사

## memmove

> memmove는 인접한 메모리를 복사할 때 overlap 현상이 일어나지 않도록 복사할 원본을 미리 copy하고 이 값을 dst에 다시 복사한다.

```c
#include <string.h>

void *memmove(void *dst, const void *src, size_t len);
```

참고 :
[memcpy와 memmove 차이](https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=sharonichoya&logNo=220510332768)

## strlcpy

> src 값은 dstsize-1만큼 복사해 dst에 복사하고 마지막에 null값을 추가한다. src의 길이를 리턴한다.

```c
#include <string.h>
size_t strlcpy(char * restrict dst, const char * restrict src, size_t dstsize);
```

## strlcat

> src의 값을 dstsize - dst길이 - 1만큼 dst뒤에 복사해 붙이고 마지막에 null을 추가해 원하는 길이를 만드는 함수. **dstsize가 dst길이보다 작거나 같으면 문자열을 붙여넣지 않는다.** 리턴값은 src와 dst길이의 합이다.

```c
#include <string.h>
size_t strlcat(char * restrict dst, const char * restrict src, size_t dstsize);
```

참고: [strlcat은 어떤 함수일까?](https://velog.io/@meong9090/42seoul-strlcat%EC%9D%80-%EC%96%B4%EB%96%A4-%ED%95%A8%EC%88%98%EC%9D%BC%EA%B9%8C)
