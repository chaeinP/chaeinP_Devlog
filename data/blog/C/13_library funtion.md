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
int isascii(int c);
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

💡 memset과 같이 메모리를 다루는 함수는 왜 내부 구현에서 void *를 unsigned char*로 캐스팅해서 사용할까?

memset, 이후에 나올 memcpy, memmove등 메모리를 조작하는 함수들의 원형은 모두 void *이다. 또한 첫번째 인자로 받는 포인터 주소 역시 void *로 받는다. 그러나 내부에서 이 인자를 사용할 때에는 unsigned char\*으로 타입캐스팅 한다. 이 이유는 첫번째, char 타입을 사용해야 1바이트씩 접근해서 ~~ 두번째, unsigned char를 사용하는 이유는 메모리의 주소값은 부호가 없는 양수로만 이루어졌기 때문이다. 만약 char를 사용하면 오버플로우 값이 들어갔을 시 의도지 않은 에러가 발생할 수 있다. 따라서 프로그램의 안정성을 높이기 위해서 unsigned char를 사용하며 이것이 C 표준에 해당한다.

참조 [코딩 도장](https://dojang.io/mod/forum/discuss.php?d=1459)

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

## toupper

> 소문자 알파벳을 대문자 알파벳으로 치환해주는 함수

```c
#include <ctype.h>

int toupper(int c);
```

## tolower

> 대문자 알파벳을 소문자 알파벳으로 치환해주는 함수

```c
#include <ctype.h>

int tolower(int c);
```

## strchr

> 문자열의 처음부터 탐색해 일치하는 첫번째 문자의 위치를 발견하면 그 주소를 반환하는 함수, 존재하지 않으면 null 포인터를 반환한다.

```c
#include <string.h>

char *strchr(const char *s, int c);
```

## strrchr

> 문자열의 뒤부터 탐색해 일치하는 첫번째 문자의 위치를 발견하면 그 주소를 발환하는 함수, 존재하지 않으면 null 포인터를 반환한다.

```c
#include <string.h>

char *strrchr(const char *s, int c);
```

## strncmp

> 두 문자열의 n번째 위치까지만 비교한다. 문자열이 종료되거나 두 문자열의 문자가 서로 다르면 s1[n] - s2[n]값을 리턴한다. 문자는 unsigned char로 계산한다.

```c
#include <string.h>
int strncmp(const char *s1, const char *s2, size_t n);
```

## memchr

> s에서 n까지 검색할 때 처음 나타는 c의 주소를 반환하는 함수, c는 int로 들어오지만 내부에서 검색할 때에는 unsigned char로 변환하여 검색한다. 찾는 문자가 없으면 null 포인터를 반환한다. strchr()함수와 유사하지만 strchr은 null을 만나면 검색을 종료하는 반면 memchr는 이와 상관없이 주어진 n개까지 검색한다.

```c
#include <string.h>
void *memchr(const void *s, int c, size_t n);
```

## memcmp

> strncmp와 유사하게 s1과 s2를 n까지 비교하여 서로 문자가 다를 때 s1[n] - s2[n]을 반환한다. n개 위치까지 문자가 모두 같으면 0을 반환한다. 문자는 unsigned char로 취급한다. strncmp가 null을 만나면 종료되는 반면 memcmp는 이와 관계없이 n개까지 비교한다.

```c
#include <string.h>

int memcmp(const void *s1, const void *s2, size_t n);
```

## strnstr

> haystack에 needle이 있는지 최대 len까지 탐색하고 needle이 있으면 needle의 첫 문자가 위치한 포인터를, 없으면 null 포인터를 리턴한다.

```c
#include <string.h>
char *strnstr(const char *haystack, const char *needle, size_t len);
```

## atoi

> 문자열로 이루어진 숫자를 int형으로 바꾸는 함수. 공백문자 + 부호 + 숫자로 이루어진 경우 숫자를 리턴하고 그 외에는 0을 리턴한다.

```c
#include <stdlib.h>

int atoi(const char *str);
```

## calloc

> 메모리를 동적 할당하고 모두 0으로 초기화하는 함수

```c
#include <stdlib.h>

void *calloc(size_t count, size_t size);
```

## strdup

> strcpy처럼 주어진 문자열을 복사하는 함수이지만 내부에서 새로 선언한 배열에 메모리를 동적으로 할당하고 그 포인터를 반환한다.

```c
#include <string.h>

char *strdup(const char *s1);
```
