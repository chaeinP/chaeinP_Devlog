---
title: C_파일 처리
date: '2021-10-04'
tags: ['C']
draft: false
summary: C언어로 파일 처리하기
---

> 이 포스트는 [코딩 도장-C언어](https://dojang.io)을 보고 정리한 study log 입니다. 보다 자세한 내용은 링크에서 직접 확인하시기를 권합니다.

## 서식에 따라 파일에 문자열 쓰기 fprintf()

파일에 문자열을 쓸 때는 먼저 fopen 함수로 파일을 열어서 파일 포인터를 얻은 뒤 fprintf 함수를 사용한다.

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // fopen, fprintf, fclose 함수가 선언된 헤더 파일

int main()
{
    FILE *fp = fopen("hello.txt", "w");     // hello.txt 파일을 쓰기 모드(w)로 열기.
                                            // 파일 포인터를 반환
    fprintf(fp, "%s %d\n", "Hello", 100);   // 서식을 지정하여 문자열을 파일에 저장
    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

파일 모드는 다음과 같은 종류가 있다.

| 파일 모드 | 기능            | 설명                                                                                                                                                     |
| --------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "r"       | 읽기 전용       | 파일을 읽기 전용으로 엽니다. 단, 파일이 반드시 있어야 합니다.                                                                                            |
| "w"       | 쓰기 전용       | 새 파일을 생성합니다. 만약 파일이 있으면 내용을 덮어씁니다.                                                                                              |
| "a"       | 추가            | 파일을 열어 파일 끝에 값을 이어 씁니다. 만약 파일이 없으면 파일을 생성합니다.                                                                            |
| "r+"      | 읽기/쓰기       | 파일을 읽기/쓰기용으로 엽니다. 단, 파일이 반드시 있어야 하며 파일이 없으면 NULL을 반환합니다.                                                            |
| "w+"      | 읽기/쓰기       | 파일을 읽기/쓰기용으로 엽니다. 파일이 없으면 파일을 생성하고, 파일이 있으면 내용을 덮어씁니다.                                                           |
| "a+"      | 추가(읽기/쓰기) | 파일을 열어 파일 끝에 값을 이어 씁니다. 만약 파일이 없으면 파일을 생성합니다. 읽기는 파일의 모든 구간에서 가능하지만, 쓰기는 파일의 끝에서만 가능합니다. |
| t         | 텍스트 모드     | 파일을 읽거나 쓸 때 개행문자 \n와 \r\n을 서로 변환합니다. ^Z 파일의 끝으로 인식하므로 ^Z까지만 파일을 읽습니다(^Z는 Ctrl+Z 입력을 뜻합니다).             |
| b         | 바이너리 모드   | 파일의 내용을 그대로 읽고, 값을 그대로 씁니다.                                                                                                           |

`fprintf` 함수로도 화면에 문자열을 출력할 수 있다. `stdout`이라는 매크로를 활용하면 된다.

```c++
fprintf(stdout, "%s %d\n", "Hello", 100);
```

💡 파일 명과 파일 경로
fopen 함수에 파일명만 지정하면 현재 작업 디렉터리에서 파일을 연다. fopen에는 파일 경로까지 함께 지정할 수 있다. 경로는 절대 경로, 상대 경로 모두 사용 가능하다.

## 서식에 따라 파일에 있는 문자열 읽기

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // fopen, fscanf, fclose 함수가 선언된 헤더 파일

int main()
{
    char s1[10];
    int num1;
    FILE *fp = fopen("hello.txt", "r");    // hello.txt 파일을 읽기 모드(r)로 열기.
                                           // 파일 포인터를 반환

    fscanf(fp, "%s %d", s1, &num1);   // 서식을 지정하여 파일에서 문자열 읽기
    printf("%s %d\n", s1, num1);      // Hello 100: 파일에서 읽은 값을 출력
    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

fscanf 함수는 파일 포인터를 넣는다는 점 말고는 scanf 함수와 사용법이 같다. 즉, 파일에서 문자열을 읽은 뒤 서식에 맞추어서 값을 변수에 저장한다.

fscanf 함수는 stdin 매크로를 활용하면 사용자가 입력한 값을 변수에 저장할 수 있다.

```c++
char s1[10];
int num1;
fscanf(stdin, "%s %d", s1, &num1);
```

## 파일에 문자열 쓰기 fputs(), fwrite()

- fputs()로 문자열 쓰기

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // fopen, fputs, fclose 함수가 선언된 헤더 파일

int main()
{
    FILE *fp = fopen("hello.txt", "w");    // hello.txt 파일을 쓰기 모드(w)로 열기.
                                           // 파일 포인터를 반환
    fputs("Hello, world!", fp);   // 파일에 문자열 저장
    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

fputs 함수도 파일 포인터 대신 stdout을 지정하면 문자열일 화면에 출력된다.

```c++
fputs("Hello, world!", stdout);
```

- fwrite()으로 문자열 쓰기

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // fopen, fwrite, fclose 함수가 선언된 헤더 파일
#include <string.h>    // strlen 함수가 선언된 헤더 파일

int main()
{
    char *s1 = "Hello, world!";

    FILE *fp = fopen("hello.txt", "w");    // hello.txt 파일을 쓰기 모드(w)로 열기.
                                           // 파일 포인터를 반환

    fwrite(s1, strlen(s1), 1, fp);    // strlen으로 문자열의 길이를 구함.
                                      // 문자열의 길이만큼 1번 파일에 저장

    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

fwrite 함수도 파일 포인터 대신 stdout을 지정하면 문자열일 화면에 출력된다.

```c++
char *s1 = "Hello, world!";
fwrite(s1, strlen(s1), 1, stdout);
```

## 파일에서 문자열 읽기 fgets(), fread()

- fgets()

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>     // fopen, fgets, fclose 함수가 선언된 헤더 파일

int main()
{
    char buffer[20];    // 파일을 읽을 때 사용할 임시 공간
    FILE *fp = fopen("hello.txt", "r");    // hello.txt 파일을 읽기 모드로 열기.
                                           // 파일 포인터를 반환

    fgets(buffer, sizeof(buffer), fp);    // hello.txt에서 문자열을 읽음
    printf("%s\n", buffer);    // Hello, world!: 파일의 내용 출력

    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

buffer는 문자열 배열을 선언해도 되고 malloc으로 동적 메모리를 할당해도 된다. fgets함수는 딱 버퍼 크기만큼만 파일을 읽는다. 하지만 내용에 줄바꿈이 있는 경우 버퍼 크기와 상관없이 줄바꿈까지만 읽는다.(줄바꿈 포함) 이처럼 fgets 함수는 줄바꿈에 따라 읽은 결과가 달라지기 때문에 유의해야한다.

fgets함수도 stdin을 지정하면 사용자가 입력한 문자열을 버퍼에 저장한다.

```c++
fgets(buffer, sizeof(buffer), stdin);
```

- fread()

```c++
#define _CRT_SECURE_NO_WARNINGS    // fopen 보안 경고로 인한 컴파일 에러 방지
#include <stdio.h>      // fopen, fread, fclose 함수가 선언된 헤더 파일

int main()
{
    char buffer[20] = { 0, };    // 파일을 읽을 때 사용할 임시 공간, 미리 0으로 전부 초기화
    FILE *fp = fopen("hello.txt", "r");     // hello.txt 파일을 읽기 모드로 열기.
                                            // 파일 포인터를 반환

    fread(buffer, sizeof(buffer), 1, fp);   // hello.txt에서 버퍼 크기(20바이트)만큼 1번 값을 읽음
    printf("%s\n", buffer);    // Hello, world!: 파일의 내용 출력
    fclose(fp);    // 파일 포인터 닫기

    return 0;
}
```

**buffer는 반드시 초기화해야한다.** 그렇지 않으면 원하는 문자열 외에 다른 값까지 출력될 가능성이 높아진다. 왜냐하면 C언어로 만든 프로그램의 문자열 포인터, 배열에 저장된 문자열은 끝에 NULL이 들어가 있지만 파일에 저장된 문자열은 끝에 NULL 이 들어가있지 않기 때문이다. 그래서 문자열을 buffer에 담아 가져오면 이전에 buffer에 담겨있던 값까지 같이 출력하게 된다.

fread 함수에서 파일을 읽는 크기는 읽기 크기 \* 읽기 횟수이다. 위 함수를 읽어보면 hello.txt파일에서 버퍼크기(20)만큼 한번 값을 읽는 다는 뜻이다.

fread 함수는 fgets 함수와는 달리 줄바꿈이 있든 없든 무조건 지정된 크기만큼 읽는다. 만약 정해진 버퍼 크기보다 큰값을 지정해도 파일에서 읽어오고 싶은 크기보다 버퍼 크기가 크기만 하면 모두 출력된다.

fread 함수도 stdin을 지정하면 사용자가 입력한 문자열을 버퍼에 저장할 수 있다. 단, \*\*fread 함수는 무조건 지정된 크기만큼만 읽기 때문에 버퍼 끝에 NULL 이 들어갈 수 있도록 읽기 크기는 버퍼 크기보다 1이 적도록 만들어주어야 한다.

```c++
char buffer[20] = { 0, };
fread(buffer, sizeof(buffer) - 1, 1, stdin);
```

✅ stdin, stdout, stderr
stdin은 키보드 입력, stdout과 strerr는 콘솔 출력하는 포인터이다.
