---
title: C_자료형 변환
date: '2021-10-02'
tags: ['C']
draft: false
summary: 서로 다른 자료형이 만났을 때
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 자료형 변환

C언어에서는 자료형이 같거나 크기가 큰 쪽, 표현 범위가 넓은 쪽으로 저장하면 자료형이 자동으로 변환된다. 반대로 표혀 크기가 작은 쪽으로 저장하면 컴차일 경고가 발생한다. 예를 들어 실수에서 소수점 이하 자리를 버리고자 실수를 정수로 저장하는 경우 프로그래머가 의도한 상황이지만 컴파일 경고가 발생한다.

```c++
int num1 = 10;
long long num2 = num1;

float num3 = 3.56f;
int num4 = num3; // 컴파일 경고
```

- 암시적 형 변환, 형 확장 : 자료형의 크기가 큰쪽, 표현 범위가 넓은 쪽으로 자동 변환되는 것

- 형 축소 : 자료형 크기가 작은 쪽, 표현 범위가 좁은 쪽으로 변환되는 것

- 형 변환(타입 캐스팅) : 형 축소에서 컴파일 경고가 나오지 않도록 만드는 것, 컴파일러에게 자료형을 변환한다는 의도를 명확하게 알리는 것

## 명시적 자료형 변환

```c++
#include <stdio.h>

int main()
{
    int num1 = 32;
    int num2 = 7;
    float num3;

    num3 = num1 / num2;      // 컴파일 경고 발생
    printf("%f\n", num3);    // 4.000000

    num3 = (float)num1 / num2;    // num1을 float로 변환
    printf("%f\n", num3);         // 4.571429

    return 0;
}
```

## 포인터 변환

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

int main()
{
    int *numPtr = malloc(sizeof(int));    // 4바이트만큼 메모리 할당
    char *cPtr;

    *numPtr = 0x12345678;

    cPtr = (char *)numPtr;     // int 포인터 numPtr을 char 포인터로 변환. 메모리 주소만 저장됨

    printf("0x%x\n", *cPtr);   // 0x78: 낮은 자릿수 1바이트를 가져오므로 0x78

    free(numPtr);    // 동적 메모리 해제

    return 0;
}
```

여기서 *cPtr은 *numPtr 주소에 접근하지만 주어진 바이트만큼만 데이터를 가져온다. 만약 크기가 작은 메모리 공간을 할당한 뒤 큰 자료형 포인터로 역참조하면 의도치 않은 값까지 포함된다. 크기가 작은 메모리를 할당한 뒤 큰 자료형의 포인터로 역참조하면 옆의 메모리 공간을 침범하여 값을 가져오기 때문이다.

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

int main()
{
    short *numPtr1 = malloc(sizeof(short));    // 2바이트만큼 메모리 할당
    int *numPtr2;

    *numPtr1 = 0x1234;
    numPtr2 = (int *)numPtr1;    // short 포인터 numPtr1을 int 포인터로 변환. 메모리 주소만 저장됨

    printf("0x%x\n", *numPtr2);    // 0xfdfd1234: 옆의 메모리를 침범하여 값을 가져옴
                                   // 0xfdfd는 상황에 따라서 값이 달라질 수 있음

    free(numPtr1);    // 동적 메모리 해제
    return 0;
}
```

## void 포인터 변환

`void`포인터의 경우 자료형이 정해져 있지 않아 역참조 연산이 불가능하다. 하지만 `void`포인터를 다른 자료형으로 변환하면 역참조가 가능하다.

```c++
#include <stdio.h>

int main()
{
    int num1 = 10;
    float num2 = 3.5f;
    char c1 = 'a';
    void *ptr;

    ptr = &num1;    // num1의 메모리 주소를 void 포인터 ptr에 저장
    // printf("%d\n", *ptr);         // 컴파일 에러
    printf("%d\n", *(int *)ptr);     // 10: void 포인터를 int 포인터로 변환한 뒤 역참조

    ptr = &num2;    // num2의 메모리 주소를 void 포인터 ptr에 저장
    // printf("%f\n", *ptr);         // 컴파일 에러
    printf("%f\n", *(float *)ptr);   // 3.500000: void 포인터를 float 포인터로 변환한 뒤 역참조

    ptr = &c1;      // c1의 메모리 주소를 void 포인터 ptr에 저장
    // printf("%c\n", *ptr);         // 컴파일 에러
    printf("%c\n", *(char *)ptr);    // a: void 포인터를 char 포인터로 변환한 뒤 역참조

    return 0;
}
```

## 구조체 포인터 변환

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

struct Data {
    char c1;
    int num1;
};

int main()
{
    struct Data *d1 = malloc(sizeof(struct Data));    // 포인터에 구조체 크기만큼 메모리 할당
    void *ptr;    // void 포인터 선언

    d1->c1 = 'a';
    d1->num1 = 10;

    ptr = d1;    // void 포인터에 d1 할당. 포인터 자료형이 달라도 컴파일 경고가 발생하지 않음.

    printf("%c\n", ((struct Data *)ptr)->c1);      // 'a' : 구조체 포인터로 변환하여 멤버에 접근
    printf("%d\n", ((struct Data *)ptr)->num1);    // 10  : 구조체 포인터로 변환하여 멤버에 접근

    free(d1);    // 동적 메모리 해제

    return 0;
}
```

여기서 `(struct Data*)ptr`은 Data 구조체 포인터로 변환되지만 메모리 주소를 저장할 때만 사용할 수 있고 멤버에 접근은 불가능하다. 이 경우 이를 새로운 포인터 변수에 저장하거나 괄호로 전체를 묶어사용해야 멤버 변수에 접근 가능하다.

```c++
(struct Data *)ptr->num1;                // 구조체 멤버에 접근할 수 없음. 컴파일 에러
struct Data *d2 = (struct Data *)ptr;    // 다른 포인터에 메모리 주소를 저장

((struct Data *)ptr)->num1 = 10; // 연산자 사용시 전체를 괄호로 묶어 사용
```

## typedef 구조체 포인터 자료형 변환

typedef를 사용하면 구조체 별칭 뿐만 아니라 구조체 포인터 별칭도 정의할 수 있다.

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

typedef struct _Data {
    char c1;
    int num1;
} Data, *PData;     // 구조체 별칭, 구조체 포인터 별칭 정의

int main()
{
    PData d1 = malloc(sizeof(Data));    // 구조체 포인터 별칭으로 포인터 선언
    void *ptr;   // void 포인터 선언

    d1->c1 = 'a';
    d1->num1 = 10;

    ptr = d1;    // void 포인터에 d1 할당. 포인터 자료형이 달라도 컴파일 경고가 발생하지 않음.

    printf("%c\n", ((Data *)ptr)->c1);     // 'a' : 구조체 별칭의 포인터로 변환
    printf("%d\n", ((PData)ptr)->num1);    // 10  : 구조체 포인터 별칭으로 변환

    free(d1);    // 동적 메모리 해제
    return 0;
}
```
