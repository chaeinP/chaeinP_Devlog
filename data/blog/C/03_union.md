---
title: C_공용체
date: '2021-10-01'
tags: ['C']
draft: false
summary: 공용체 이론부터 활용 예제까지
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 공용체 union

공용체는 구조체와 정의 방법이 같지만 **모든 멤버가 멤버 중 가장 큰 자료형의 공간을 공유한다는 점**에서 구조체와 큰 차이가 있다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

union Box {    // 공용체 정의
    short candy;     // 2바이트
    float snack;     // 4바이트
    char doll[8];    // 8바이트
};

int main()
{
    union Box b1;   // 공용체 변수 선언

    printf("%d\n", sizeof(b1));  // 8: 공용체의 전체 크기는 가장 큰 자료형의 크기

    strcpy(b1.doll, "bear");     // doll에 문자열 bear 복사

    printf("%d\n", b1.candy);    // 25954
    printf("%f\n", b1.snack);    // 4464428256607938511036928229376.000000
    printf("%s\n", b1.doll);     // bear

    return 0;
}
```

위의 예시에서 값을 초기화한 b1.doll을 제외한 다른 멤버들의 값은 엉망이다. 이는 모든 멤버들이 가장 큰 자료형의 메모리를 공유하기 때문이다. 어느 한 멤버에 값이 저장되면 나머지 멤버는 값을 사용할 수 없다. **따라서 공용체의 멤버는 한번에 하나씩 쓰면 정상적으로 사용할 수 있다.**

```c++
b1.candy = 10;
printf("%d\n", b1.candy);    // 10: 사용(출력)

b1.snack = 60000.0f;
printf("%f\n", b1.snack);    // 60000.00000000: 사용(출력)

strcpy(b1.doll, "bear");
printf("%s\n", b1.doll);     // bear: 사용(출력)
```

공용체는 여러 멤버에 동시에 접근하지 않는 경우 같은 메모리 레이아웃에 멤버를 모아둘 때 사용한다. 특히 공용체는 임베디드 시스템이나 커널 모드 디바이스 드라이버 등에서 주로 사용하며 보통은 거의 쓰지 않는다.

## 공용체와 엔디언

```c++
#include <stdio.h>

union Data {    // 공용체 정의
    char c1;
    short num1;
    int num2;
};

int main()
{
    union Data d1;    // 공용체 변수 선언

    d1.num2 = 0x12345678;    // 리틀 엔디언에서는 메모리에 저장될 때 78 56 34 12로 저장됨

    printf("0x%x\n", d1.num2);    // 0x12345678: 4바이트 전체 값 출력
    printf("0x%x\n", d1.num1);    // 0x5678: 앞의 2바이트 값만 출력
    printf("0x%x\n", d1.c1);      // 0x78: 앞의 1바이트 값만 출력

    printf("%d\n", sizeof(d1));   // 4: 공용체의 전체 크기는 가장 큰 자료형의 크기

    return 0;
}
```

위 예시를 보면 d1.num2를 제외하고 나머지 멤버들은 저장된 값의 일부만 가져오고 있다. 이는 멤버들이 한 저장소를 공유하고 있기 때문이다. 특히 일부 값이 뒤에서부터 가져와지고 있는데 이는 **리틀엔디언** 저장 방식 때문이다.

💡 리틀 엔디언이란?
데이터를 1바이트씩 쪼개 낮은 자릿수를 앞에 배치하는 저장방식이다. 이와 반대로 데이터의 순서대로 저장하는 방식을 빅엔디언이라고 한다.

## typedef와 공용체

공용체도 구조체와 마찬가지로 typedef로 별칭을 붙일 수 있다.

```c++
typedef union _Box {    // 공용체 이름은 _Box
    short candy;
    float snack;
    char doll[8];
} Box;
```

공용체는 정의하는 동시에 변수 선언이 가능하다.

```c++
union Box {    // 공용체 정의
    short candy;
    float snack;
    char doll[8];
} b1;          // 공용체를 정의하는 동시에 변수 b1 선언
```

## 공용체 포인터

공용체도 포인터로 선언할 수 있으며 malloc함수로 동적 메모리를 할당할 수 있다.

```c++
#define _CRT_SECURE_NO_WARNINGS     // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

union Box {    // 공용체 정의
    short candy;
    float snack;
    char doll[8];
};

int main()
{
    union Box *b1 = malloc(sizeof(union Box));    // 공용체 포인터 선언, 메모리 할당

    printf("%d\n", sizeof(union Box));    // 8: 공용체의 전체 크기는 가장 큰 자료형의 크기

    strcpy(b1->doll, "bear");     // doll에 문자열 bear 복사

    printf("%d\n", b1->candy);    // 25954
    printf("%f\n", b1->snack);    // 4464428256607938511036928229376.000000
    printf("%s\n", b1->doll);     // bear

    free(b1);    // 동적 메모리 해제

    return 0;
}

// 공용체 포인터와 공용체변수 사용
int main()
{
    union Box b1;      // 공용체 변수 선언
    union Box *ptr;    // 공용체 포인터 선언

    ptr = &b1;    // b1의 메모리 주소를 구하여 ptr에 할당

    strcpy(ptr->doll, "bear");     // doll에 문자열 bear 복사

    printf("%d\n", ptr->candy);    // 25954
    printf("%f\n", ptr->snack);    // 4464428256607938511036928229376.000000
    printf("%s\n", ptr->doll);     // bear

    return 0;
}
```
