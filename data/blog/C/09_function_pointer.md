---
title: C_함수 포인터
date: '2021-10-03'
tags: ['C']
draft: false
summary: 함수 포인터 사용하기
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 직접 확인하시기를 권합니다.

## 함수 포인터

함수를 배열 또는 구조체에 넣거나, 함수 자체를 함수의 매개변수로 넘겨주고, 반환 값으로 가져오기 위해 함수 포인터를 사용한다.

```c++
void hello()
{
    printf("Hello, world!\n");
}

int main()
{
    printf("%p\n", hello); // 00D1137F(32비트): 함수 이름도 포인터이므로 메모리 주소가 출력됨
    return 0;
}
```

함수 이름도 배열처럼 그 자체로 포인터이기 때문에 출력해보면 메모리 주소가 나온다. 함수 포인터를 선언할 때는 함수 포인터와 저장될 함수의 반환값 자료형, 매개변수 자료형과 개수가 일치해야 한다.

```c++
#include <stdio.h>

// 덧셈 함수
int add(int a, int b)    // int형 반환값, int형 매개변수 두 개
{
    return a + b;
}

// 곱셈 함수
int mul(int a, int b)    // int형 반환값, int형 매개변수 두 개
{
    return a * b;
}

int main()
{
    int (*fp)(int, int);    // int형 반환값, int형 매개변수 두 개가 있는 함수 포인터 fp 선언

    fp = add;                      // add 함수의 메모리 주소를 함수 포인터 fp에 저장
    printf("%d\n", fp(10, 20));    // 30: 함수 포인터로 add 함수를 호출하여 합을 구함

    fp = mul;                      // mul 함수의 메모리 주소를 함수 포인터 fp에 저장
    printf("%d\n", fp(10, 20));    // 200: 함수 포인터로 mul 함수를 호출하여 곱을 구함

    return 0;
}
```

## 함수 포인터 배열

```c++
#define _CRT_SECURE_NO_WARNINGS    // scanf 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>
#include <string.h>

int add(int a, int b)
{
    return a + b;
}

int sub(int a, int b)
{
    return a - b;
}

int mul(int a, int b)
{
    return a * b;
}

int div(int a, int b)
{
    return a / b;
}

int main()
{
    int funcNumber;    // 함수 번호
    int num1, num2;
    int (*fp[4])(int, int);    // int형 반환값, int형 매개변수 두 개가 있는 함수 포인터 배열 선언

    fp[0] = add;    // 첫 번째 요소에 덧셈 함수의 메모리 주소 저장
    fp[1] = sub;    // 두 번째 요소에 뺄셈 함수의 메모리 주소 저장
    fp[2] = mul;    // 세 번째 요소에 곱셈 함수의 메모리 주소 저장
    fp[3] = div;    // 네 번째 요소에 나눗셈 함수의 메모리 주소 저장

    printf("함수 번호와 계산할 값을 입력하세요: ");
    scanf("%d %d %d", &funcNumber, &num1, &num2);    // 함수 번호와 계산할 값을 입력받음

    printf("%d\n", fp[funcNumber](num1, num2));    // 함수 포인터 배열을 인덱스로 접근하여 함수 호출

    return 0;
}
```

만약 배열에 들어있는 모든 함수를 호출하고 싶다면 반복문을 사용할 수 있다.

```c++
// 배열의 요소 개수만큼 반복(요소 개수: 배열의 전체 크기 / 요소 한 개의 크기)
for (int i = 0; i < sizeof(fp) / sizeof(fp[0]); i++)
{
    printf("%d\n", fp[i](num1, num2));    // 함수 포인터 배열을 인덱스로 접근하여 함수 호출
}
```

## 함수 포인터와 구조체

```c++
#include <stdio.h>

struct Calc {
    int (*fp)(int, int);    // 함수 포인터를 구조체 멤버로 지정
};

int add(int a, int b)    // int형 반환값, int형 매개변수 두 개
{
    return a + b;
}

int main()
{
    struct Calc c;
    c.fp = add;    // add 함수의 메모리 주소를 구조체 c의 멤버에 저장
    printf("%d\n", c.fp(10, 20));    // 30: 구조체 멤버로 add 함수 호출

    return 0;
}
```

보통 C언어는 객체지향 문법을 지원하지 않는다고 알려져 있다. 하지만 C 언어에서도 구조체와 함수 포인터를 활용하면 충분히 객체지향으로 프로그래밍을 할 수 있다.

## 함수 포인터를 함수 반환값으로

```c++
#include <stdio.h>

int add(int a, int b)    // int형 반환값, int형 매개변수 두 개
{
    return a + b;
}

int (*getAdd(int x, int y))(int, int)    // 함수 포인터 반환, int형 매개변수 두 개
{
    printf("%d %d\n", x, y);    // x, y는 getAdd 함수의 매개변수
    return add;
}

int main()
{
    printf("%d\n", getAdd(8, 9)(10, 20));    // 8, 9는 getAdd에 전달
                                             // 10, 20은 getAdd에서 반환된 add에 전달

    return 0;
}
```

## typedef와 함수 포인터

```c++
#include <stdio.h>

int add(int a, int b)    // int형 반환값, int형 매개변수 두 개
{
    return a + b;
}

typedef int (*FP)(int, int);    // FP를 함수 포인터 별칭으로 정의

struct Calc {
    FP fp;   // 함수 포인터 별칭을 구조체 멤버 자료형으로 사용
};

void executer(FP fp)    // 함수 포인터 별칭을 매개변수 자료형으로 사용
{
    printf("%d\n", fp(70, 80));
}

int main()
{
    FP fp1;      // 함수 포인터 별칭으로 변수 선언
    fp1 = add;
    printf("%d\n", fp1(10, 20));     // 30

    FP fp[10];   // 함수 포인터 별칭으로 배열 선언
    fp[0] = add;
    printf("%d\n", fp[0](30, 40));   // 70

    struct Calc c;
    c.fp = add;
    printf("%d\n", c.fp(50, 60));   // 110

    executer(add);    // 150: executer를 호출할 때 add 함수의 메모리 주소를 전달

    return 0;
}
```
