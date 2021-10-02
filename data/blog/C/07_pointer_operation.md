---
title: C_포인터 연산
date: '2021-10-02'
tags: ['C']
draft: false
summary: 포인터는 연산을 하면 어떤 값이 나올까
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 포인터 연산

포인터 변수에는 메모리 주소가 들어있다. 포인터 변수에 어떤 숫자를 더하거나 빼면 메모리 주소가 증가하거나 감소한다. 즉, **포인터 연산을 하면 다른 메모리 주소에 접근할 수 있으며 메모리 주소를 손쉽게 옮겨다닐 수 있다.**

```c++
#include <stdio.h>

int main()
{
    int numArr[5] = { 11, 22, 33, 44, 55 };
    int *numPtrA;
    int *numPtrB;
    int *numPtrC;

    numPtrA = numArr;    // 배열 첫 번째 요소의 메모리 주소를 포인터에 저장

    numPtrB = numPtrA + 1;    // 포인터 연산
    numPtrC = numPtrA + 2;    // 포인터 연산

    printf("%p\n", numPtrA);    // 00A3FC00: 메모리 주소. 컴퓨터마다, 실행할 때마다 달라짐
    printf("%p\n", numPtrB);    // 00A3FC04: sizeof(int) * 1이므로 numPtrA에서 4가 증가함
    printf("%p\n", numPtrC);    // 00A3FC08: sizeof(int) * 2이므로 numPtrA에서 8이 증가함

    return 0;
}
```

포인터는 선언된 자료형의 크기의 배수로 증감한다. 위의 예에서 포인터는 모두 int로 선언되어 4바이트만큼 주소가 달라지고 있다.

char, short, long long 자료형 포인터를 연산하면 다음과 같다.

```c++
#include <stdio.h>

int main()
{
    char *cPtr1 = NULL;
    short *numPtr1 = NULL;
    long long *numPtr2 = NULL;

    printf("%p\n", cPtr1 + 1);      // 00000001: 0x000000에서 1바이트만큼 순방향으로 이동
    printf("%p\n", numPtr1 + 1);    // 00000002: 0x000000에서 2바이트만큼 순방향으로 이동
    printf("%p\n", numPtr2 + 1);    // 00000008: 0x000000에서 8바이트만큼 순방향으로 이동

    return 0;
}
```

포인터 연산을 역참조하면 해당 메모리에 접근해 값을 가져올 수 있다.

```c++
#include <stdio.h>

int main()
{
    int numArr[5] = { 11, 22, 33, 44, 55 };
    int *numPtrA;
    int *numPtrB;
    int *numPtrC;

    numPtrA = numArr;    // 배열 첫 번째 요소의 주소를 포인터에 저장

    numPtrB = numPtrA + 1;    // 포인터 연산. numPtrA + 4바이트
    numPtrC = numPtrA + 2;    // 포인터 연산. numPtrA + 8바이트

    printf("%d\n", *numPtrB);    // 22: 역참조로 값을 가져옴, numArr[1]과 같음
    printf("%d\n", *numPtrC);    // 33: 역참조로 값을 가져옴, numArr[2]와 같음

    return 0;
}
```

증감 연산자도 사용할 수 있다.

```c++
#include <stdio.h>

int main()
{
    int numArr[5] = { 11, 22, 33, 44, 55 };
    int *numPtrA;
    int *numPtrB;
    int *numPtrC;

    numPtrA = &numArr[2];    // 배열 세 번째 요소의 주소를 포인터에 저장

    numPtrB = numPtrA;
    numPtrC = numPtrA;

    printf("%d\n", *(++numPtrB));    // 44: numPtrA에서 순방향으로 4바이트만큼 떨어진
                                     // 메모리 주소에 접근
    printf("%d\n", *(--numPtrC));    // 22: numPtrA에서 역방향으로 4바이트만큼 떨어진
                                     // 메모리 주소에 접근

    return 0;
}
```

여기서 주의할 점은 _(++numPtrB)과 _(numPtrB++)는 값이 다르다는 것이다. 전자는 44가 출력되지만 후자는 출력 이후 연산이 진행되기 때문에 33이 나온다.

## void 포인터 연산

void 포인터는 자료형의 크기가 정해져 있지 않기 때문에 +, -로 연산을 해도 얼마만큼 이동할지 알 수가 없다. 따라서 void 포인터는 포인터 연산을 할 수 없다. 만약 void 포인터로 포인터 연산을 하고 싶다면 다른 포인터로 변환한 뒤 연산을 해야한다.

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일

int main()
{
    void *ptr = malloc(100);    // 100바이트만큼 메모리 할당

    printf("%p\n", ptr);               // 00FADD20: 메모리 주소. 컴퓨터마다, 실행할 때마다 달라짐
    printf("%p\n", (int *)ptr + 1);    // 00FADD24: 다른 포인터로 변환한 뒤 포인터 연산
    printf("%p\n", (int *)ptr - 1);    // 00FADD1C: 다른 포인터로 변환한 뒤 포인터 연산

    void *ptr2 = ptr;    // 메모리 주소를 변화시킬 때는 다른 포인터에 보관
    printf("%p\n", ++(int *)ptr2);     // 00FADD24: 다른 포인터로 변환한 뒤 포인터 연산
    printf("%p\n", --(int *)ptr2);     // 00FADD20: 다른 포인터로 변환한 뒤 포인터 연산

    printf("%p\n", ((int *)ptr2)++);   // 00FADD20: 다른 포인터로 변환한 뒤 포인터 연산
    printf("%p\n", ((int *)ptr2)--);   // 00FADD24: 다른 포인터로 변환한 뒤 포인터 연산

    free(ptr);

    return 0;
}
```

> ❗️동적 메모리를 할당받은 포인터를 ++, -- 연산자로 포인터 연산을 하게 되면 포인터에 저장된 메모리 주소 자체가 바뀌게 된다. 이때 free 함수에서 메모리 주소가 바뀐 포인터로 메모리 해제를 하면 에러가 발생하므로 주의합니다. 따라서 메모리 주소가 바뀌는 연산은 다른 포인터에 주소를 보관하여 수행한다.

void 포인터를 포언터 연산한 뒤 역참조 하려면 다른 포인터로 변환하여 연산한 뒤 연산 전체를 괄호로 묶고 앞에 역참조 연산자(\*)를 추가한다.

```c++
#include <stdio.h>

int main()
{
    int numArr[5] = { 11, 22, 33, 44, 55 };
    void *ptr = &numArr[2];    // 두 번째 요소의 메모리 주소

    printf("%d\n", *(int *)ptr);    // 33: 포인터 연산을 하지 않은 상태에서 역참조

    // void 포인터를 다른 포인터로 변환하여 포인터 연산을 한 뒤 역참조
    printf("%d\n", *((int *)ptr + 1));    // 44
    printf("%d\n", *((int *)ptr - 1));    // 22

    printf("%d\n", *(++(int *)ptr));      // 44
    printf("%d\n", *(--(int *)ptr));      // 33

    printf("%d\n", *(((int *)ptr)++));    // 33
    printf("%d\n", *(((int *)ptr)--));    // 44

    return 0;
}
```

💡 마이크로소프트 전용 언어 확장
변수의 자료형을 다른 자료형으로 변환환 뒤 변수의 값을 변경하는것은 마이크로소프트 전용 언어 확장이다. 따라서 GCC에서 컴파일하려면 포인터를 다른 자료형의 포인터에 할당한 뒤 연산자를 사용해야 한다.

```c++
void *ptr = malloc(100);

int *ptr2 = ptr;    // void 포인터를 int 포인터에 할당
printf("%p\n", ++ptr2);
printf("%p\n", --ptr2);

printf("%d\n", *(++ptr2));
printf("%d\n", *(--ptr2));

free(ptr);
```

## 구조체 포인터 연산

```c++
#include <stdio.h>
#include <stdlib.h>    // malloc, free 함수가 선언된 헤더 파일
#include <string.h>    // memcpy 함수가 선언된 헤더 파일

struct Data {
    int num1;
    int num2;
};

int main()
{
    void *ptr = malloc(sizeof(struct Data) * 3);    // 구조체 3개 크기만큼 동적 메모리 할당
    struct Data d[3];

    ((struct Data *)ptr)->num1 = 10;        // 포인터 연산으로 메모리에 값 저장
    ((struct Data *)ptr)->num2 = 20;        // 포인터 연산으로 메모리에 값 저장

    ((struct Data *)ptr + 1)->num1 = 30;    // 포인터 연산으로 메모리에 값 저장
    ((struct Data *)ptr + 1)->num2 = 40;    // 포인터 연산으로 메모리에 값 저장

    ((struct Data *)ptr + 2)->num1 = 50;    // 포인터 연산으로 메모리에 값 저장
    ((struct Data *)ptr + 2)->num2 = 60;    // 포인터 연산으로 메모리에 값 저장

    memcpy(d, ptr, sizeof(struct Data) * 3);    // 동적 메모리가 구조체 배열의 형태와 같은지
                                    // 확인하기 위해 동적 메모리의 내용을 구조체 배열에 복사

    printf("%d %d\n", d[1].num1, d[1].num2);    // 30 40: 구조체 배열의 멤버 출력
    printf("%d %d\n", ((struct Data *)ptr + 2)->num1, ((struct Data *)ptr + 2)->num2);
                                                // 50 60: 포인터 연산으로 메모리의 값 출력

    free(ptr);    // 동적 메모리 해제

    return 0;
}
```
