---
title: C_printf 구현
date: '2022-01-16'
tags: ['C', '42Seoul']
draft: false
summary: printf 최소 기능 직접 구현하기
---

> 이 포스트는 42Seoul 본과정 ft_printf 과제를 위해 공부한 내용을 기록한 스터디로그입니다.

### printf

printf 는 `<stdio.h>`헤더 파일에 선언되어있는 함수이다. 다음의 형태로 정의되어있다. 반환값은 출력된 문자열의 수이다.

```c
int printf(const char *restrict format, ...)
```

## printf format

```c
%[parameter][flags][width][.precision][length]type
```

### parameter

POSIX에서만 지원되는 필드다. C99에는 없다. **`n$`**로 n에는 숫자가 들어가고 출력되는 순서를 조절할 수 있는 기능이다. 생략 가능한 옵션.

```c
printf("%2$d %2$#x; %1$d %1$#x",16,17)
//17 0x11; 16 0x10
//만약 n$이 없었다면 16 0x10이 먼저 출력되어야하지만
//n$로 출력 순서를 지정해 17과 16의 출력 순서가 변경되었다.
```

### flags

정렬, 부호, 공백, 8진수 및 16진수 접두어 출력 등을 결정하는 역할 수행 (여기서 type은 과제 기준으로 c, s, d, i, u, o, x, X, p로만 한정)

| Flag       | Meaning                                                                                                       | Default      | Example                    | type                                                               |
| ---------- | ------------------------------------------------------------------------------------------------------------- | ------------ | -------------------------- | ------------------------------------------------------------------ |
| -          | width 범위 이내에서 왼쪽 정렬                                                                                 | 오른쪽 정렬  |                            | all                                                                |
| +          | 양수면 +, 음수면 - 부호를 접두어로 붙힘                                                                       | -부호만 표시 | printf(”%+d”, 10); ⇒ “10”  | d, i,                                                              |
| ‘ ‘(blank) | output이 부호있는 양수이면 앞에 공백을 추가한다. ‘+’ flag와 동시에 사용되면 blank는 무시되고 ‘+’가 출력된다.  | 공백 없음    | printf(” %d”, 10); ⇒ “ 10” | d, i                                                               |
| #          | 0이 아닌 출력값이면서 type이 o,x,X인 경우 각각 0,0x,0X가 접두어로 붙는다. 출력값이 0인 경우 그냥 0만 출력     | 접두사없음   | printf(”%#X”, 20) ⇒ “0X14” | o, x, X, 이외 타입들과도 같이 쓰일 수 있지만 쓰임이 많이 달라 생략 |
| 0          | width 기준 선행 공백을 0으로 채움, precision이 지정되거나 -플래그가 있는 경우 0플래그는 무시되고 공백이 출력. | 공백         | printf(”%03d”, 1); ⇒ “001  | d, i, u, o, x, X, p                                                |

### width

width는 인쇄되는 최소 문자 수를 제어하는 음이 아닌 10진수 정수이다. 출력값의 문자(바이트)수가 지정된 너비보다 작으면 width에 도달할 때까지 왼쪽이나 오른쪽에 공백이 추가된다. (- 플래그 지정 여부에 따라 다름)

만약 출력값의 문자수가 지정된 너비보다 크거나 너비가 지정되지 않는 경우 값의 모든 문자가 인쇄된다. (width로 값이 잘리지는 않는다는 얘기) width는 \*를 사용해 argument로 값을 지정할 수 있다.

```c
printf("%3d", 10); // " 10"
printf("%*d", 3, 10); // " 10"
```

### precision

precision은 인쇄할 문자 수 또는 소수 자릿수를 지정하는 마침표가 앖에 오는 음이 아닌 10진수 정수이다. width와는 달리 정밀도로 인해 출력값이 잘리거나 부동 소수점이 반올림 될 수 있다.

precision도 width와 같이 \*를 사용해 인수로 값을 받을 수 있다.

| type                | meaning                                                                                                                                                                                                                                    | default                                                                                                 |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| i, d, u, o, x, X, p | 인쇄할 최소 자릿수를 지정한다. 인수의 자릿수가 precision보다 작으면 왼쪽이 0으로 채워진다. 인수의 자릿수가 precision보다 클 경우 자릿수가 잘리지 않고 전체 다 출력된다. precision이 음수가 들어오면 precision이 아예 없는 것처럼 동작한다. | precision이 0이거나 완전히 생략된 경우, 또는 마침표 (.) 뒤에 숫자가 없는 경우 precision은 1로 설정된다. |
| c                   | no effect                                                                                                                                                                                                                                  | chracter 출력                                                                                           |
| s                   | precision은 인쇄할 최대 바이트 수를 지정한다. precision을 초과하는 바이트는 인쇄되지 않는다.                                                                                                                                               |                                                                                                         |

precision 0과 -1 차이 예

```c
printf("%020.*p\n", -1, ptr);  // "0x0000007ffd90451d40"
printf("%020.*p\n", 0, ptr);   // "      0x7ffd90451d40"
```

precision 0 일 때 인수가 0이면 아무것도 출력되지 않는다.

```c
printf("%.*d\n", 0, 0); // ""
```

### type

| type | conversion type | description                                                                     |
| ---- | --------------- | ------------------------------------------------------------------------------- |
| c    | int             | 문자                                                                            |
| s    | char \*         | 첫번째 null 문자가 나올때까지 문자열, or precision 범위에 도달할때까지의 문자열 |
| d, i | int             | 부호 있는 십진수 정수                                                           |
| u    | unsigned int    | 부호 없는 십진수 정수                                                           |
| x, X | unsigned int    | 부호 없는 16진수 정수                                                           |
| p    | void \*         | 포인터                                                                          |
| %    |                 | 무조건 %만 출력                                                                 |

[printf() - Print Formatted Characters](https://www.ibm.com/docs/en/i/7.4?topic=functions-printf-print-formatted-characters)

[printf](https://www.cplusplus.com/reference/cstdio/printf/)

## 가변인자

함수에 들어가는 인수(argument)의 개수가 변하는 것. 가변인자를 정의할 때는 고정 매개변수가 한 개 이상 있어야 한다. 고정 매개변수 뒤에 ...을 붙여 매개변수의 개수가 정해지지 않았다는 표시를 해준다. ...뒤에는 다른 매개변수를 지정할 수 없다.

```c
void example(int args, ...);
```

### 가변인자 매크로

가변인자와 관련한 매크로는 `<stdargs.h>` 헤더에 정의되어 있다.

- va_list : 가변 인자 목록 포인터 선언. 가변인자 목록 포인터란 가변인자의 메모리 주소를 저장하는 포인터.
- va_start : 가변 인자 목록 포인터 지정
- va_arg : 특정 자료형 크기만큼 가변 인자 목록 포인터에서 값을 가져옴
- va_end : 가변 인자 목록 포인터를 NULL로 초기화

va_arg는 int, char, double, float 형을 모두 같은 메모리 크기로 읽어온다. 이 부분은 va_arg 매크로의 size 계산법에 의해 동작하며 cpu 성능 향상을 위해 4바이트로 크기를 고정시킨 것이 아닐까 추측

```c
void printNumbers(int args, ...)    // 가변 인자의 개수를 받음, ...로 가변 인자 설정
{
    va_list ap;    // 가변 인자 목록 포인터

    va_start(ap, args);    // 가변 인자 목록 포인터 설정
    for (int i = 0; i < args; i++)    // 가변 인자 개수만큼 반복
    {
        int num = va_arg(ap, int);    // int 크기만큼 가변 인자 목록 포인터에서 값을 가져옴
                                      // ap를 int 크기만큼 순방향으로 이동
        printf("%d ", num);           // 가변 인자 값 출력
    }
    va_end(ap);    // 가변 인자 목록 포인터를 NULL로 초기화

    printf("\n");    // 줄바꿈
}
```

[출처 : C 언어 코딩 도장](https://dojang.io/mod/page/view.php?id=577)

## 과제 요구사항

Mandatory

- 다음의 Conversion specifier를 구현한다.
  - %c, %s, %p, %d, %i, %u, %x, %X, %%

Bonus

- `-0# +` 플래그 구현
- .precision 구현
- minimum width field 구현
