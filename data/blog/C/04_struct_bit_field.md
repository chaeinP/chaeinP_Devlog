---
title: C_구조체 비트 필드 사용
date: '2021-10-01'
tags: ['C']
draft: false
summary: 구조체 멤버를 비트 단위로 저장하는 법
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 비트 필드

C99 표준에서는 비트 필드로 사용할 수 있는 자료형을 \_Bool, signed int, unsigned int, int로 규정하고 있지만 대부분의 컴파일러에서는 모든 정수 자료형을 사용할 수 있다. 보통 비트필드에 부호 없는(unsigned) 자료형을 주로 사용한다. 단, 실수 자료형(float)은 비트 필드로 사용할 수 없다.

```c++
#include <stdio.h>

struct Flags {
    unsigned int a : 1;     // a는 1비트 크기
    unsigned int b : 3;     // b는 3비트 크기
    unsigned int c : 7;     // c는 7비트 크기
};

int main()
{
    struct Flags f1;    // 구조체 변수 선언

    f1.a = 1;      //   1: 0000 0001, 비트 1개
    f1.b = 15;     //  15: 0000 1111, 비트 4개
    f1.c = 255;    // 255: 1111 1111, 비트 8개

    printf("%u\n", f1.a);    //   1:        1, 비트 1개만 저장됨
    printf("%u\n", f1.b);    //   7:      111, 비트 3개만 저장됨
    printf("%u\n", f1.c);    // 127: 111 1111, 비트 7개만 저장됨

    return 0;
}
```

각 메버에는 선언할 때 주어진 비트 수보다 많은 비트가 할당되었다. 이를 출력해보면 할당한 값과 실제로 저장된 값이 다른 것을 알 수 있다. **비트 필드에는 지정한 비트 수만큼 저장되며 나머지 비트는 버려진다.** 비트 필드의 각 멤버는 최하위 비트 부터 차례대로 배치된다.

위 Flags구조체의 크기를 보면 4바이트이다. 이는 멤버를 unsigned int로 선언했기 때문이다. **비트의 크기는 구조체의 크기보다 큰 값으로 설정할 수 없다.**

## 비트 필드와 공용체

```c++
#include <stdio.h>

struct Flags {
    union {    // 익명 공용체
        struct {    // 익명 구조체
            unsigned short a : 3;    // a는 3비트 크기
            unsigned short b : 2;    // b는 2비트 크기
            unsigned short c : 7;    // c는 7비트 크기
            unsigned short d : 4;    // d는 4비트 크기
        };                           // 합계 16비트
        unsigned short e;    // 2바이트(16비트)
    };
};

int main()
{
    struct Flags f1 = { 0, };    // 모든 멤버를 0으로 초기화

    f1.a = 4;     //  4: 0000 0100
    f1.b = 2;     //  2: 0000 0010
    f1.c = 80;    // 80: 0101 0000
    f1.d = 15;    // 15: 0000 1111

    printf("%u\n", f1.e);    // 64020: 1111 1010000 10 100

    return 0;
}
```
