---
title: C_구조체
date: '2021-10-01'
tags: ['C']
draft: false
summary: 구조체란?
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 구조체(struct)

**구조체는 선언된 변수들의 묶음을 의미한다.** 구조체는 관련 정보를 하나의 의미로 묶을 때 사용한다.

만약, 한 사람의 정보를 저장하기 위해서 다음과 같은 변수들이 필요한다고 하자.

```c++
char name[20];        // 이름
int age;              // 나이
char address[100];    // 주소
```

사람이 100명이라면 100명의 정보를 모두 따로 저장하기 위해 name변수만 100개를 각각 따로 선언해주어야 한다. 이는 비효율 적이다. 이럴 때 구조체를 사용한다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

struct Person {   // 구조체 정의
    char name[20];        // 구조체 멤버 1
    int age;              // 구조체 멤버 2
    char address[100];    // 구조체 멤버 3
};

int main()
{
    struct Person p1;     // 구조체 변수 선언
    // 점으로 구조체 멤버에 접근하여 값 할당
    strcpy(p1.name, "홍길동"); // 할당 연산자로는 할당이 불가능하다.
    p1.age = 30;
    strcpy(p1.address, "서울시 용산구 한남동");

    // 구조체 변수 선언과 동시에 초기화
    struct Person p2 = { "고길동", 40, "서울시 서초구 반포동" };

    // 점으로 구조체 멤버에 접근하여 값 출력
    printf("이름: %s\n", p1.name);       // 이름: 홍길동
    printf("나이: %d\n", p1.age);        // 나이: 30
    printf("주소: %s\n", p1.address);    // 주소: 서울시 용산구 한남동

    printf("이름: %s\n", p2.name);       // 이름: 고길동
    printf("나이: %d\n", p2.age);        // 나이: 40
    printf("주소: %s\n", p2.address);    // 주소: 서울시 서초구 반포동

    return 0;
}
```

구조체를 정의할 때는 중괄호 뒤에 **반드시 세미콜론을 붙여야한다.** 구조체 변수 p1과 p2는 main함수 바깥에서 선언된 전역 변수이다.

## typedef를 사용한 구조체 alias

매번 `struct 구조체_태그 변수_이름`으로 선언해 사용하는 것이 번거로울 땐 `typedef`를 사용해 별칭을 지을 수 있다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

typedef struct _Person {   // 구조체 이름은 _Person
    char name[20];            // 구조체 멤버 1
    int age;                  // 구조체 멤버 2
    char address[100];        // 구조체 멤버 3
} Person;                  // typedef를 사용하여 구조체 별칭을 Person으로 정의

int main()
{
    Person p1;    // 구조체 별칭 Person으로 변수 선언 = struct _Person p1; 과 완전 동일
    // 점으로 구조체 멤버에 접근하여 값 할당
    strcpy(p1.name, "홍길동");
    p1.age = 30;
    strcpy(p1.address, "서울시 용산구 한남동");

    // 점으로 구조체 멤버에 접근하여 값 출력
    printf("이름: %s\n", p1.name);       // 이름: 홍길동
    printf("나이: %d\n", p1.age);        // 나이: 30
    printf("주소: %s\n", p1.address);    // 주소: 서울시 용산구 한남동

    return 0;
}
```

> 💡 typedef는 모든 자료형에 별칭을 부여할 수 있다.

```c++
typedef int MYINT;      // int를 별칭 MYINT로 정의
typedef int* PMYINT;    // int 포인터를 별칭 PMYINT로 정의

MYINT num1;        // MYINT로 변수 선언
PMYINT numPtr1;    // PMYINT로 포인터 변수 선언
numPtr1 = &num1;   // 포인터에 변수의 주소 저장
                   // 사용 방법은 일반 변수, 포인터와 같음

PMYINT* numPtr1; // PMYINT에는 이미 *가 포함되어 있어 이중포인터가 선언된다.
```

## 익명 구조체

구조체 태그를 생략하고 별칭만 있는 구조체를 익명 구조체라고 한다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일

typedef struct {   // 구조체 이름이 없는 익명 구조체
    char name[20];        // 구조체 멤버 1
    int age;              // 구조체 멤버 2
    char address[100];    // 구조체 멤버 3
} Person;          // typedef를 사용하여 구조체 별칭을 Person으로 정의

int main()
{
    Person p1;    // 구조체 별칭 Person으로 변수 선언

    // 점으로 구조체 멤버에 접근하여 값 할당
    strcpy(p1.name, "홍길동");
    p1.age = 30;
    strcpy(p1.address, "서울시 용산구 한남동");

    // 점으로 구조체 멤버에 접근하여 값 출력
    printf("이름: %s\n", p1.name);       // 이름: 홍길동
    printf("나이: %d\n", p1.age);        // 나이: 30
    printf("주소: %s\n", p1.address);    // 주소: 서울시 용산구 한남동

    return 0;
}
```

## 구조체 포인터 사용

구조체는 멤버 변수가 여러 개 들어있어서 크기가 큰 편이다. 그래서 구조체 변수를 일일이 선언해서 사용하는 것보다는 포인터에 메모리를 할당해서 사용하는 편이 효율적이다. 구조체 포인터에도 malloc함수를 사용하여 동적 메모리를 할당할 수 있다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // strcpy 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>    // strcpy 함수가 선언된 헤더 파일
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct Person {    // 구조체 정의
    char name[20];        // 구조체 멤버 1
    int age;              // 구조체 멤버 2
    char address[100];    // 구조체 멤버 3
};

int main()
{
    struct Person *p1 = malloc(sizeof(struct Person));    // 구조체 포인터 선언, 메모리 할당

    // 화살표 연산자로 구조체 멤버에 접근하여 값 할당
    strcpy(p1->name, "홍길동");
    p1->age = 30;
    strcpy(p1->address, "서울시 용산구 한남동");

    // 화살표 연산자로 구조체 멤버에 접근하여 값 출력
    printf("이름: %s\n", p1->name);       // 홍길동
    printf("나이: %d\n", p1->age);        // 30
    printf("주소: %s\n", p1->address);    // 서울시 용산구 한남동

    free(p1);    // 동적 메모리 해제

    return 0;
}
```

구조체 포인터에서 멤버에 접근하는 방법은 두가지가 있다.

- `p1->age`와 같이 화살표 연산자 사용
- `(*p1).age`와 같이 구조체 포인터를 역참조한 뒤 .으로 멤버에 접근

> 💡 만약 구조체의 멤버가 포인터일 때는 어떻게 할까?

구조체 멤버가 포인터일때는 역참조를 하려면 맨 앞에 *를 붙이면 된다. 이때 구조체 변수 앞에 붙는 *는 멤버의 역참조이지 구조체 변수의 역참조가 아니다.

- `*구조체변수.멤버`
- `*구조체포인터->멤버`

```c++
#include <stdio.h>
#include <stdlib.h>

struct Data {
    char c1;
    int *numPtr;    // 포인터
};

int main()
{
    int num1 = 10;
    struct Data d1;    // 구조체 변수
    struct Data *d2 = malloc(sizeof(struct Data));    // 구조체 포인터에 메모리 할당

    d1.numPtr = &num1;
    d2->numPtr = &num1;

    printf("%d\n", *d1.numPtr);     // 10: 구조체의 멤버를 역참조
    printf("%d\n", *d2->numPtr);    // 10: 구조체 포인터의 멤버를 역참조

    d2->c1 = 'a';
    printf("%c\n", (*d2).c1);      //  a: 구조체 포인터를 역참조하여 c1에 접근
                                   // d2->c1과 같음
    printf("%d\n", *(*d2).numPtr); // 10: 구조체 포인터를 역참조하여 numPtr에 접근한 뒤 다시 역참조
                                   // *d2->numPtr과 같음

    free(d2);

    return 0;
}
```

구조체 변수에 malloc으로 동적메모리를 할당하지 않고 선언된 구조체 변수의 메모리 주소를 할당하는 방법도 있다.

```c++
#include <stdio.h>

struct Person {    // 구조체 정의
    char name[20];        // 구조체 멤버 1
    int age;              // 구조체 멤버 2
    char address[100];    // 구조체 멤버 3
};

int main()
{
    struct Person p1;      // 구조체 변수 선언
    struct Person *ptr;    // 구조체 포인터 선언

    ptr = &p1;    // p1의 메모리 주소를 구하여 ptr에 할당

    // 화살표 연산자로 구조체 멤버에 접근하여 값 할당
    ptr->age = 30;

    printf("나이: %d\n", p1.age);      // 나이: 30: 구조체 변수의 멤버 값 출력
    printf("나이: %d\n", ptr->age);    // 나이: 30: 구조체 포인터의 멤버 값 출력

    return 0;
}

```

## 구조체 멤버 정렬

CPU가 메모리에 접근할 땐 4바이트(32비트 CPU기준) 또는 8바이트(64비트 CPU기준) 으로 접근한다. 만약 4바이트, 8바이트 보다 작은 데이터에 접근할 경우 내부적으로 빈공간을 채우기 위한 시프트 연산이 발생해 CPU 효율이 떨어진다.

이에 C언어 컴파일러는 CPU가 메모리 데이터에 효율적으로 접근할 수 있도록 구조체를 일정한 크기로 정렬한다. 구조체 크기가 15나 17바이트가 되면 2,4,8,16바이트 단위로 정렬하는 것이다.

이러한 정렬을 수행하면 데이터가 원래의 모양을 유지하지 않아 데이터 무결성을 유지하기가 어려워진다. 따라서 데이터 손실, 순서가 중요한 데이터의 경우에는 구조체 정렬을 수행해서는 안된다.

## 구조체 크기

구조체 크기는 sizeof 연산자를 사용하면 알 수 있다.

- sizeof(struct 구조체\_태그)
- sizeof(구조체\_별칭)
- sizeof(구조체\_변수)
- sizeof 구조체\_변수

```c++
#include <stdio.h>

struct PacketHeader {
    char flags;    // 1바이트
    int seq;       // 4바이트
};

int main()
{
    struct PacketHeader header;

    printf("%d\n", sizeof(header.flags));           // 1: char는 1바이트
    printf("%d\n", sizeof(header.seq));             // 4: int는 4바이트
    printf("%d\n", sizeof(header));                 // 8: 구조체 전체 크기는 8바이트
    printf("%d\n", sizeof(struct PacketHeader));    // 8: 구조체 이름으로 크기 구하기

    return 0;
}
```

위 예시에서 구조체 멤버 flags와 seq의 크기를 더하면 5가 된다. 그러나 구조체의 크기는 8이다. 그 이유는 C언어에서 구조체를 정렬할 때 멤버 중 가장 클 자료형 크기의 배수로 정렬하기 때문이다. 여기서 int의 크기가 4로 가장 큰 자료형이기 때문에 int 크기의 배수로 정렬한다.

4바이트를 기준으로 정렬하였을 때 구조체 멤버가 모두 들어가는 크기의 최소는 8바이트다. 이때 3바이트가 남는 공간으로 존재하는데 이를 패딩이라고 한다.

구조체에서 멤버의 위치를 구하는 `offsetof` 매크로를 사용하면 구조체가 어떻게 정렬되었는지를 쉽게 파악할 수 있다.

```c++
#include <stdio.h>
#include <stddef.h>   // offsetof 매크로가 정의된 헤더 파일

struct PacketHeader {
    char flags;    // 1바이트
    int seq;       // 4바이트
};

int main()
{
    printf("%d\n", offsetof(struct PacketHeader, flags));    // 0
    printf("%d\n", offsetof(struct PacketHeader, seq));      // 4

    return 0;
}
```

`offsetof` 매크로를 사용하면 구조체 멤버의 상대 위치가 나온다. 첫멤버인 flags의 상대위치는 0, seq의 위치는 4에서 시작되므로 그 사이에 빈공간이 있음을 알 수 있다.

## 구조체 정렬 크기 조절

- Visual Studio, GCC 4.0 이상

  ```c++
  #pragma pack(push, 정렬크기)
  #pragma pack(pop)
  ```

- GCC 4.0 미만

  ```c++
  __attribute__((aligned(정렬크기), packed))
  ```

```c++
#include <stdio.h>

#pragma pack(push, 1)    // 1바이트 크기로 정렬
struct PacketHeader {
    char flags;    // 1바이트
    int seq;       // 4바이트
};
#pragma pack(pop)        // 정렬 설정을 이전 상태(기본값)로 되돌림

int main()
{
    struct PacketHeader header;

    printf("%d\n", sizeof(header.flags));    // 1: char는 1바이트
    printf("%d\n", sizeof(header.seq));      // 4: int는 4바이트
    printf("%d\n", sizeof(header));          // 5: 1바이트 단위로 정렬했으므로
                                             // 구조체 전체 크기는 5바이트

    return 0;
}
```

`pack(push, 정렬크기)`에서 중요한 부분은 정렬 크기 이다. C언어 자료형의 크기는 바이트 단위이고 가장 작은 크기는 1바이트이다. 따라서 정렬크기를 1로 설정하면 남는 공간 없이 자료형 크기 그대로 메모리에 올라간다. 정렬 크기는 1, 2, 4, 8, 16중 하나로 설정할 수 있다. 주로 1을 많이 사용한다.

`#pragma pack(push)`을 사용하면 이후 모든 구조체에 영향을 미치기 때문에 꼭 `#pragma pack(pop)`으로 설정을 종료해야한다.

`__attribute__((aligned(1), packed)); `은 각 구조체의 닫는 중괄호 다음에 넣는다.

```c++
struct PacketHeader {
    char flags;    // 1바이트
    int seq;    // 4바이트
} __attribute__((aligned(1), packed));    // GCC 4.0 미만: 1바이트 크기로 정렬
```

## 구조체 메모리에 값 설정하기

> memset(구조체변수주소, 값, 메모리크기)

만약 구조체변수의 멤버에 모두 0이라는 값을 주어야 한다고 하자.

```c++
구조체변수.멤버1 = 0;
구조체변수.멤버2 = 0;
구조체변수.멤버3 = 0;
구조체변수.멤버4 = 0;
```

일일히 모든 멤버에 값을 초기화하는 것은 매우 비효율적이다.

```c++
struct 구조체이름 변수이름 = { 0, };  // 구조체 변수의 내용을 모두 0으로 초기화
                                      // 단 malloc 함수로 할당한 메모리에는 사용할 수 없음
```

위처럼 중괄호를 사용해 변수의 모든 멤버 값을 0으로 초기화할 수 있으나 malloc으로 메모리를 할당한 경우에는 사용할 수 없다.

> 중괄호를 사용하지 않고, 구조체 변수나 메모리의 내용을 한번에 설정하려면 `memset`을 사용하면 된다.

```c++
#include <stdio.h>
#include <string.h>    // memset 함수가 선언된 헤더 파일

struct Point2D {
    int x;
    int y;
};

int main()
{
    struct Point2D p1;

    memset(&p1, 0, sizeof(struct Point2D));    // p1을 구조체 크기만큼 0으로 설정

    printf("%d %d\n", p1.x, p1.y);    // 0 0: memset을 사용하여 0으로 설정했으므로
                                      // x, y 모두 0

    return 0;
}
```

동적 메모리를 할당한 경우에는 다음과 같이 사용한다.

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일
#include <string.h>    // memset 함수가 선언된 헤더 파일

struct Point2D {
    int x;
    int y;
};

int main()
{
    struct Point2D *p1 = malloc(sizeof(struct Point2D));    // 구조체 크기만큼 메모리 할당

    memset(p1, 0, sizeof(struct Point2D));    // p1을 구조체 크기만큼 0으로 설정

    printf("%d %d\n", p1->x, p1->y);    // 0 0: memset을 사용하여 0으로 설정했으므로
                                        // x, y 모두 0

    free(p1);    // 동적 메모리 해제

    return 0;
}
```

## 구조체 메모리 값 복사

> memcpy(목적지주소, 원본주소, 메모리크기)

```c++
#include <stdio.h>
#include <string.h>    // memcpy 함수가 선언된 헤더 파일

struct Point2D {
    int x;
    int y;
};

int main()
{
    struct Point2D p1;
    struct Point2D p2;

    p1.x = 10;    // p1의 멤버에만 값 저장
    p1.y = 20;    // p1의 멤버에만 값 저장

    memcpy(&p2, &p1, sizeof(struct Point2D));    // Point2D 구조체 크기만큼 p1의 내용을 p2로 복사

    printf("%d %d\n", p2.x, p2.y);    // 10 20: p1의 내용을 p2로 복사했으므로 10 20

    return 0;
}

// 동적 메모리의 경우
int main()
{
    struct Point2D *p1 = malloc(sizeof(struct Point2D));
    struct Point2D *p2 = malloc(sizeof(struct Point2D));

    p1->x = 10;    // p1의 멤버에만 값 저장
    p1->y = 20;    // p1의 멤버에만 값 저장

    memcpy(p2, p1, sizeof(struct Point2D));    // Point2D 구조체 크기만큼 p1의 내용을 p2로 복사

    printf("%d %d\n", p2->x, p2->y);    // 10 20: p1의 내용을 p2로 복사했으므로 10 20

    free(p2);
    free(p1);

    return 0;
}
```

## 구조체 배열

만약 구조체를 100개 만들어야 한다면 구조체 변수를 100개 선언해야한다. 이때 구조체 배열을 사용하면 편리하다.

```c++
#include <stdio.h>

struct Point2D {
    int x;
    int y;
};

int main()
{
    struct Point2D p[3];    // 크기가 3인 구조체 배열 생성

    p[0].x = 10;    // 인덱스로 요소에 접근한 뒤 점으로 멤버에 접근
    p[0].y = 20;
    p[1].x = 30;
    p[1].y = 40;
    p[2].x = 50;
    p[2].y = 60;

    printf("%d %d\n", p[0].x, p[0].y);    // 10 20
    printf("%d %d\n", p[1].x, p[1].y);    // 30 40
    printf("%d %d\n", p[2].x, p[2].y);    // 50 60

    return 0;
}

// 구조체 배열과 동시에 초기화하기
int main()
{
    // 구조체 배열을 선언하면서 초기화
    struct Point2D p1[3] = { { .x = 10, .y = 20 }, { .x = 30, .y = 40 }, { .x = 50, .y = 60 } };

    printf("%d %d\n", p1[0].x, p1[0].y);    // 10 20
    printf("%d %d\n", p1[1].x, p1[1].y);    // 30 40
    printf("%d %d\n", p1[2].x, p1[2].y);    // 50 60

    // 구조체 배열을 선언하면서 초기화
    struct Point2D p2[3] = { { 10, 20 }, { 30, 40 }, { 50, 60 } };

    printf("%d %d\n", p2[0].x, p2[0].y);    // 10 20
    printf("%d %d\n", p2[1].x, p2[1].y);    // 30 40
    printf("%d %d\n", p2[2].x, p2[2].y);    // 50 60

    return 0;
}
```

만약 모든 요소의 멤버를 0으로 초기화하고 싶다면 `{0,}`을 할당하면 된다.

## 구조체 포인터 배열

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct Point2D {
    int x;
    int y;
};

int main()
{
    struct Point2D *p[3];    // 크기가 3인 구조체 포인터 배열 선언

    // 구조체 포인터 배열 전체 크기에서 요소(구조체 포인터)의 크기로 나눠서 요소 개수를 구함
    for (int i = 0; i < sizeof(p) / sizeof(struct Point2D *); i++)    // 요소 개수만큼 반복
    {
        p[i] = malloc(sizeof(struct Point2D));    // 각 요소에 구조체 크기만큼 메모리 할당
    }

    p[0]->x = 10;    // 인덱스로 요소에 접근한 뒤 화살표 연산자로 멤버에 접근
    p[0]->y = 20;
    p[1]->x = 30;
    p[1]->y = 40;
    p[2]->x = 50;
    p[2]->y = 60;

    printf("%d %d\n", p[0]->x, p[0]->y);    // 10 20
    printf("%d %d\n", p[1]->x, p[1]->y);    // 30 40
    printf("%d %d\n", p[2]->x, p[2]->y);    // 50 60

    for (int i = 0; i < sizeof(p) / sizeof(struct Point2D *); i++)    // 요소 개수만큼 반복
    {
        free(p[i]);    // 각 요소의 동적 메모리 해제
    }

    return 0;
}
```

💡 size_t 자료형이란?

> size_t은 부호 없는 정수 자료형인데 sizeof 연산자나 offsetof 매크로의 결과가 size_t 자료형으로 리턴된다.

```c++
#include <stdio.h>
#include <stddef.h>

struct Data {
    char mode;
    int count;
};

int main()
{
    size_t size = sizeof(int);
    size_t offset = offsetof(struct Data, count);

    // size_t를 출력할 때는 서식 지정자에 z를 붙임
    printf("%zd %zd\n", size, offset);

    return 0;
}
```
