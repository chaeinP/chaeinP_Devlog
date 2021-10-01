---
title: C_문자열 함수
date: '2021-09-30'
tags: ['C']
draft: false
summary: C언어에서 사용하는 문자열 함수 한눈에 보기
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다.

## 문자열 길이 구하기 strlen(문자열 포인터 || 문자 배열)

```c++
#include <stdio.h>
#include <string.h>    // strlen 함수가 선언된 헤더 파일

int main(void) {
    char *s1 = "Hello";       // 포인터에 문자열 Hello의 주소 저장
    char s2[10] = "Hello";    // 크기가 10인 char형 배열을 선언하고 문자열 할당

    printf("%d\n", strlen(s1));    // 5: strlen 함수로 문자열의 길이를 구함
    printf("%d\n", strlen(s2));    // 5: strlen 함수로 문자열의 길이를 구함

    return 0;
}

/*
5
5
*/
```

## 문자열 비교 strcmp(문자열1, 문자열2)

```c++
#include <stdio.h>
#include <string.h>    // strcmp 함수가 선언된 헤더 파일

int main()
{
    char s1[10] = "Hello";
    char *s2 = "Hello";

    int ret = strcmp(s1, s2);    // 두 문자열이 같은지 문자열 비교

    printf("%d\n", ret);         // 0: 두 문자열이 같으면 0

    return 0;
}

// 0
```

ASCII 코드표 기준 s1이 s2보다 크면 1, s1과 s2가 같으면 0, s1보다 s2가 크면 -1을 반환한다.

## 문자열 복사 strcpy(대상 문자열, 원본 문자열)

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

int main()
{
    char s1[10] = "Hello";    // 크기가 10인 char형 배열을 선언하고 문자열 할당
    char s2[10];              // 크기가 10인 char형 배열을 선언

    strcpy(s2, s1);        // s1의 문자열을 s2로 복사

    printf("%s\n", s2);    // Hello

    return 0;
}

// Hello
```

문자열 포인터에 복사하기 위해선 `malloc()`으로 메모리를 할당하고 복사해줘야 에러가 나지 않는다.

## 문자열 합치기 strcat(최종 문자열, 붙일 문자열)

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcat 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcat 함수가 선언된 헤더 파일

int main()
{
    char s1[10] = "world";
    char s2[20] = "Hello"; // s2 뒤에 붙일 것이므로 배열 크기를 크게 만듦

    strcat(s2, s1);        // s2 뒤에 s1를 붙임

    printf("%s\n", s2);    // Helloworld

    return 0;
}

//Helloworld
```

만약 문자열 포인터에 합치고자 한다면 malloc()으로 메모리를 할당하고 strcpy()로 초기화를 한 뒤 수행한다. 이미 다른 문자열로 초기화된 포인터의 경우 읽기 전용이기 때문에 strcat 함수 사용이 불가능하다.

## 문자열 서식 만들기 sprintf(배열, 서식, 값)

```c++
#define _CRT_SECURE_NO_WARNINGS    // sprintf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // sprintf 함수가 선언된 헤더 파일

int main()
{
    char s1[20];    // 크기가 20인 char형 배열을 선언

    sprintf(s1, "Hello, %s", "world!");    // "Hello, %s"로 서식을 지정하여 s1에 저장

    printf("%s\n", s1);    // Hello, world!: 문자열 s1 출력

    return 0;
}

// Hello, world!
```

서식 지정자를 사용하여 다양한 값도 문자열로 만들 수 있다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // sprintf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // sprintf 함수가 선언된 헤더 파일

int main()
{
    char s1[30];    // 크기가 30인 char형 배열을 선언

    sprintf(s1, "%c %d %f %e", 'a', 10, 3.2f, 1.123456e-21f);    // 문자, 정수, 실수를 문자열로 만듦

    printf("%s\n", s1);    // a 10 3.200000 1.123456e-21: 문자열 s1 출력

    return 0;
}

//a 10 3.200000 1.123456e-21
```

문자열 포인터에 sprintf()를 사용하기 위해선 먼저 malloc()으로 메모리를 할당해주어야한다.

## 문자 검색 strchr,strrchr(대상문자열, 검색할 문자)

`strchr`은 왼쪽부터 검색해 해당하는 문자를 발견하면 해당 문자를 포함해 문자열 끝까지 리턴한다.

```c++
#include <stdio.h>
#include <string.h>    // strchr 함수가 선언된 헤더 파일

int main()
{
    char s1[30] = "A Garden Diary";  // 크기가 30인 char형 배열을 선언하고 문자열 할당

    char *ptr = strchr(s1, 'a');     // 'a'로 시작하는 문자열 검색, 포인터 반환

    while (ptr != NULL)              // 검색된 문자열이 없을 때까지 반복
    {
        printf("%s\n", ptr);         // 검색된 문자열 출력
        ptr = strchr(ptr + 1, 'a');  // 포인터에 1을 더하여 a 다음부터 검색
    }

    return 0;
}

/*
arden Diary
ary
*/
```

`strrchr`은 strchr와는 반대로 오른쪽부터 검색한다.

```c++
#include <stdio.h>
#include <string.h>   // strrchr 함수가 선언된 헤더 파일

int main()
{
    char s1[30] = "A Garden Diary";    // 크기가 30인 char형 배열을 선언하고 문자열 할당

    char *ptr = strrchr(s1, 'a');      // 문자열 끝에서부터 'a'로 시작하는 문자열 검색. 포인터 반환

    printf("%s\n", ptr);   // ary

    return 0;
}

//ary
```

## 문자열 검색 strstr(대상 문자열, 검색할 문자열)

```c++
#include <stdio.h>
#include <string.h>    // strstr 함수가 선언된 헤더 파일

int main()
{
    char s1[100] = "A Garden Diary A Garden Diary A Garden Diary";

    char *ptr = strstr(s1, "den");     // den으로 시작하는 문자열 검색, 포인터 반환

    while (ptr != NULL)                // 검색된 문자열이 없을 때까지 반복
    {
        printf("%s\n", ptr);           // 검색된 문자열 출력
        ptr = strstr(ptr + 1, "den");  // den 포인터에 1을 더하여 e부터 검색
    }
    return 0;
}
/*
den Diary A Garden Diary A Garden Diary
den Diary A Garden Diary
den Diary
*/
```

## 문자열 자르기 strtok(대상 문자열, 기준 문자)

💡기준 문자는 꼭 쌍타옴표로 묶는다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strtok 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strtok 함수가 선언된 헤더 파일

int main()
{
    char s1[30] = "The Little Prince";  // 크기가 30인 char형 배열을 선언하고 문자열 할당

    char *ptr = strtok(s1, " ");      // " " 공백 문자를 기준으로 문자열을 자름, 포인터 반환

    while (ptr != NULL)               // 자른 문자열이 나오지 않을 때까지 반복
    {
        printf("%s\n", ptr);          // 자른 문자열 출력
        ptr = strtok(NULL, " ");      // 다음 문자열을 잘라서 포인터를 반환
    }

    return 0;
}
/*
The
Little
Prince
*/
```

strtok 함수를 사용할 때는 처음에만 자를 문자열을 넣어주고, 그 다음부터는 NULL을 넣어주어야 그 다음 문자열부터 다시 실행된다. strtok은 기준 문자를 NULL로 대체하기 때문에 **원본 문자열이 훼손된다는 점에서 주의해야 한다.**

또, 기준문자는 한번에 여러개 지정이 가능하다. 아래 예는 `-, T, :`을 기준으로 문자를 자른다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strtok 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strtok 함수가 선언된 헤더 파일

int main()
{
    char s1[30] = "2015-06-10T15:32:19";    // 크기가 30인 char형 배열을 선언하고 문자열 할당

    char *ptr = strtok(s1, "-T:");    // -, T, 콜론을 기준으로 문자열을 자름
                                      // 포인터 반환

    while (ptr != NULL)               // 자른 문자열이 나오지 않을 때까지 반복
    {
        printf("%s\n", ptr);          // 자른 문자열 출력
        ptr = strtok(NULL, "-T:");    // 다음 문자열을 잘라서 포인터를 반환
    }

    return 0;
}
/*
2015
06
10
15
32
19
*/
```

## 숫자문자열 -> 정수 atoi(문자열)

`atoi`는 ASCII string to integer의 줄임말이다.

```c++
#include <stdio.h>
#include <stdlib.h>    // atoi 함수가 선언된 헤더 파일

int main()
{
    char *s1 = "283";   // "283"은 문자열
    int num1;

    num1 = atoi(s1);        // 문자열을 정수로 변환하여 num1에 할당

    printf("%d\n", num1);   // 283

    return 0;
}
// 283
```

문자열은 정수로 되어있어야 하며 알파벳, 특수 문자가 포함되면 해당 문자부터는 변환을 하지 않는다. 또, 처음부터 숫자가 아니면 모두 0으로 변환된다.

## 문자열 -> 실수 atof(문자열)

`atof`는 ASCII to float의 줄임말이다.

```c++
#include <stdio.h>
#include <stdlib.h>    // atof 함수가 선언된 헤더 파일

int main()
{
    char *s1 = "3.e5";   // "3.e5"는 문자열
    float num1;

    num1 = atof(s1);     // 문자열을 실수로 변환하여 num1에 할당

    printf("%e %f\n", num1, num1);    // 3.000000e+05 300000.000000

    return 0;
}
// 3.000000e+05 300000.000000
```

atof도 atoi와 마찬가지로 문자열은 실수로 되어있어야 하며 알파벳, 특수 문자가 포함되면 해당 문자부터는 변환을 하지 않는다. 또, 처음부터 숫자가 아니면 모두 0으로 변환된다.

## 특정 진법으로 표현된 문자열 -> 정수 strtol(문자열, 끝포인터, 진법);

```c++
#include <stdio.h>
#include <stdlib.h> // strtol 함수가 선언된 헤더 파일

int main()
{
    char *s1 = "0xaf10 42 0x27C 9952"; // "0xaf10 42 0x27C 9952"는 문자열
    int num1;
    int num2;
    int num3;
    int num4;
    char *end;    // 이전 숫자의 끝 부분을 저장하기 위한 포인터

    num1 = strtol(s1, &end, 16);     // 16진법으로 표기된 문자열을 정수로 변환
    num2 = strtol(end, &end, 10);    // 10진법으로 표기된 문자열을 정수로 변환
    num3 = strtol(end, &end, 16);    // 16진법으로 표기된 문자열을 정수로 변환
    num4 = strtol(end, NULL, 10);    // 10진법으로 표기된 문자열을 정수로 변환

    printf("%x\n", num1);    // af10
    printf("%d\n", num2);    // 42
    printf("%X\n", num3);    // 27C
    printf("%d\n", num4);    // 9952

    return 0;
}
/*
af10
42
27c
9952
*/
```

## 정수 -> 문자열 sprintf(문자열, "%d", 정수)

정수를 문자열로 바꿀 땐 서식 지정자 `sprintf`를 사용한다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // sprintf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // sprintf 함수가 선언된 헤더 파일

int main()
{
    char s1[10], s2[10];       // 변환한 문자열을 저장할 배열
    int num1 = 283;    // 283은 정수

    sprintf(s1, "%d", num1);    // %d를 지정하여 정수를 문자열로 저장
    printf("%s\n", s1);         // 283

    sprintf(s2, "0x%x", num1);    // %x를 지정하여 정수를 16진법으로 표기된 문자열로 저장, %X로 대문자 표기하면 0x11B로 출력
                                  // 16진수라는 것을 나타내기 위해 앞에 0x를 붙임
    printf("%s\n", s2);           // 0x11b

    return 0;
}
```

## 실수 -> 문자열 sprintf(문자열, "%f", 실수)

실수를 문자열로 바꿀 때에도 sprintf()를 사용한다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // sprintf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // sprintf 함수가 선언된 헤더 파일

int main()
{
    char s1[10], s2[10];               // 변환한 문자열을 저장할 배열
    float num1 = 38.972340f;   // 38.972340은 실수

    sprintf(s1, "%f", num1);   // %f를 지정하여 실수를 문자열로 저장
    printf("%s\n", s1);        // 38.972340

    sprintf(s2, "%e", num1);    // %e를 지정하여 실수를 지수 표기법으로 된 문자열로 저장
    printf("%s\n", s2);         // 3.897234e+01

    return 0;
}
```
