---
title: C_열거형
date: '2021-10-02'
tags: ['C']
draft: false
summary: 열거형 사용법
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 확인하실 수 있습니다.

## 열거형 enum

열거형은 **정수형 상수**에 이름을 붙여 코드 이해를 돕는다. 만약 정수형 상수를 여러개 선언해야한다면 일일이 선언하기가 불편하다. 이때 열거형을 사용한다.

```c++
enum 열거형이름 {
    값1 = 초깃값,
    값2,
    값3
};
```

```c++
#include <stdio.h>

enum DayOfWeek {    // 열거형 정의
    Sunday = 0,         // 초깃값 할당
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday
};

int main()
{
    enum DayOfWeek week;    // 열거형 변수 선언
    week = Tuesday;    // 열거형 값 할당
    printf("%d\n", week);   // 2: Tuesday의 값 출력

    return 0;
}
```

열거형의 값은 처음에만 할당해주면 그 아래에 오는 값들은 1씩 증가하면서 자동으로 할당된다. (아무 값도 할당하지 않으면 0부터 시작).

문법으로 정해진 것은 아니지만 열거형 이름이나 값은 대문자만 사용하는 경우가 많다. 특히 단어와 단어 사이에는 \_을 주로 사용한다.

만약 초깃값으로 연속되지 않은 불규칙한 값을 사용하려면 모든 열거형 값에 정수를 할당하면 된다.

```c++
enum Value {
    ValueA = 1,
    ValueB = 3,
    ValueC = 7,
    ValueD = 11
};
```

## typedef 열거형

열거형도 typedef로 별칭을 정할 수 있고 익명 열거형으로도 사용할 수 있다.

```c++
typedef enum _DayOfWeek {    // 열거형 이름은 _DayOfWeek
    Sunday = 0,
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday
} DayOfWeek;                 // typedef를 사용하여 열거형 별칭을 DayOfWeek로 정의

typedef enum {    // 익명 열거형 정의
    Sunday = 0,
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday
} DayOfWeek;      // typedef를 사용하여 열거형 별칭을 DayOfWeek로 정의

DayOfWeek week;    // 열거형 별칭으로 공용체 변수 선언
```

열거형을 정의하는 동시에 변수를 선언할 수 있다. 별칭과는 다르니 구분해야한다.

```c++
enum DayOfWeek {    // 열거형 정의
    Sunday = 0,
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday
} week;             // 열거형을 정의하는 동시에 변수 week 선언
```

## 열거형과 switch문

열거형은 switch로 분기문을 사용할 때 유용하다. 열거형 변수를 switch 분기문에 사용하면 열거형 값에 따라 코드를 실행할 수 있다.

```c++
#include <stdio.h>

enum LuxSkill {
    LightBinding = 1,
    PrismaticBarrier,
    LucentSingularity,
    FinalSpark
};

int main()
{
    enum LuxSkill skill;    // 열거형 변수 선언
    skill = LightBinding;    // 열거형 값 할당

    switch (skill)
    {
    case LightBinding:         // 열거형 값이 LightBinding일 때
        printf("LightBinding\n");
        break;
    case PrismaticBarrier:     // 열거형 값이 PrismaticBarrier일 때
        printf("PrismaticBarrier\n");
        break;
    case LucentSingularity:    // 열거형 값이 LucentSingularity일 때
        printf("LucentSingularity\n");
        break;
    case FinalSpark:           // 열거형 값이 FinalSpark일 때
        printf("FinalSpark\n");
        break;
    default:
        break;
    }

    return 0;
}
```

## 열거형과 for문

열거형 변수로 열거형 개수를 나타내는 항목을 추가하면 for문을 쉽게 사용할 수 있다.

```c++
#include <stdio.h>

typedef enum _DayOfWeek {    // 열거형 이름은 _DayOfWeek
    Sunday = 0,                  // 초깃값을 0으로 할당
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday,
    DayOfWeekCount               // 열거형 값의 개수를 나타내는 항목 추가
} DayOfWeek;                 // typedef를 사용하여 열거형 별칭을 DayOfWeek로 정의

int main()
{
    // 초깃값은 Sunday, i가 DayOfWeekCount보다 작을 때까지만 반복
    for (DayOfWeek i = Sunday; i < DayOfWeekCount; i++)
    {
        printf("%d\n", i);
    }

    return 0;
}
```
