---
title: C_주요 라이브러리 함수 구현 시 궁금한 점 정리
date: '2021-11-14'
tags: ['C']
draft: false
summary: 왜?의 향연...
---

> 이 포스트는 42Seoul 본과정 libft함수 구현시 궁금했던 점을 기록한 포스트입니다.

## strlen

- size_t 자료형은 무엇일까?

  '이론상 가장 큰 사이즈를 담을 수 있는 unsigned 데이터 타입'. 즉, 32비트 머신에서는 32비트 사이즈의 unsigned 정수형(int가 아니라 그냥 '정수'를 의미한다), 64비트 머신에서는 64비트 사이즈의 unsigned 정수형(unsigned long long)이다. 향후 등장할지도 모르는 128비트 머신이라던가 더 큰 머신이 존재한다면 그에 따라 더 큰 사이즈가 될 수 있다.

  - int / unsigned int와 다른 점은?

    **int와 unsigned int는 환경에 따라 그 크기가 가변적이다.** int와 unisgned int는 4byte(32비트)의 메모리를 갖는다고 알려져있지만 이는 32비트 환경을 기준으로 이야기했을 때를 의미한다. int와 unsigned int는 환경에 따라 그 크기가 달라질 수 있다. **그에 반해 size_t의 경우는 항상 4byte로 그 크기가 고정되어있다.** 따라서 어떤 환경에서 사용하든 4byte로 고정되어있기 때문에 int와 unsigned int를 사용했을 때보다 더 안정적인 코드를 작성할 수 있다.

- strlen의 반환값은 왜 size_t자료형일까?

  다양한 환경에서 사용될 가능성이 높은 libc라이브러리(C 표준 함수)의 경우 어떤 환경에서도 동일한 결과를 반환할 수 있도록 하기 위해(프로그램의 이식성을 높이기 위해) unsigned int 대신 size_t 타입을 사용했다.

참고

- [Further insights into size_t](https://www.embedded.com/further-insights-into-size_t/)

- [size_t 타입의 이해](https://seoulforest.tistory.com/entry/sizet-%ED%83%80%EC%9E%85%EC%9D%98-%EC%9D%B4%ED%95%B4)

## memset

- memset의 prototype은 왜 void *형식으로 첫번째 인자를 받고 void *로 리턴할까?

  memset은 기본적으로 포인터 주소에 할당된 값을 초기화하는 역할을 수행한다. 이 함수는 특정 자료형의 배열만 초기화하지 않고 모든 자료형 배열을 초기화할 수 있어야하기 때문에 (void \*)로 인자를 받고 리턴한다.

- void *로 전달된 값은 왜 내부에서 unsigned char *로 타입 캐스팅해야할까?

  memset 함수의 기능 정의는 다음과 같다.

  `첫번째 인자인 포인터에 세번째 인자인 num byte만큼 두번째 인자 value로 채운다.`

  memset의 값 초기화는 byte단위로 이루어져야하기 때문에 크기가 1byte인 char 형 데이터로 array를 초기화해주어야한다.

  그렇다면 일반 char형이 아닌 unisgned char형으로 타입캐스팅한 이유는 무엇일까? 이에 대해 42 슬랙에서 좋은 답글을 찾았다.

  **첫번째 이유, C표준 명세에서 보증하는 자료형은 unsigned char 뿐**

  ```
  C언어 표준 명세서의 '자료형의 표현' 절에 따르면 n바이트로 이루어진
  모든 타입의 값은 unsigned char [n] 이라는 배열 형태의 메모리에 복사 가능헤야 합니다.
  즉, unsigned char 만이 메모리 상의 바이트 복사를 표준 명세 상 보증합니다. Signed char는 명세 차원에서 이를 보증해주지 않습니다.

  출처 : 42 서울 슬랙
  ```

  **두번째 이유, 패딩 문제가 발생하지 않기 때문에**

  unsigned char는 명세상 유일하게 padding이 없는 자료형이다.

  💡 패딩 or 패딩 비트란?

  컴퓨터는 데이터를 읽고 접근할 때 bit 단위가 아닌 byte단위(8bits)를 사용한다. 따라서 1byte보다 작은 bit 단위의 데이터를 저장할 때에도 byte단위로 맞추기 위해 유효하지 않은 비트를 추가하는데 이것을 padding 이라고 한다. padding여부에 따라 메모리에 값이 다르게 저장되면서 char형과 unsigned char형의 동작이 다르게 나타날 수 있다.

  **세번째 이유, -0을 null로 인식하는 문제에서 자유롭기 때문에**

  슬랙에 어떤 분이 공유해주셨던 글을 그대로 첨부한다.

  ```
  예전에 이곳에서도 비슷한 논의가 있었던 것으로 기억합니다만,
  꼭 padding bit 가 아니어도 null character 의 정의에 의해
  문제가 될 수 있는 경우가 있습니다.

  signed char 는 1s' complement 표현이나 sign-magnitude
  표현에서 -0 을 가질 수도 있습니다. - 이 경우 sign bit 가 1 이 됩니다.

  하지만, null character 의 정의는 (sign bit 포함해) 모든 비트가
  0 이길 요구하고 있습니다. 즉, -0 은 null character 가 될 수 없는 것입니다.

  따라서 이를 올바르게 구분해내기 위해서는 unsigned char 로 접근해야
  하는 것입니다.

  출처 : 42 서울 슬랙
  ```

  너무 잘 설명해주셔서 덧붙일 설명이 없다... 관련해서 참조할 문서는 [여기](https://kldp.org/node/75686)

  💡 mem함수를 구현하면서 몇가지 소스코드를 찾아봤는데 이 때 unsigned char*타입 캐스팅 되어야한다고 생각된 부분들이 char*로 캐스팅 되어있음을 발견했다. 왜일까?

  특정 cpu환경에서는 char를 애초에 unsigned char로 인식하도록 설정되어있기도 하고 컴파일 옵션자에 따라 char를 unsigned char로 인식하도록 설정해줄 수도 있다고 한다. 내가 찾은 소스코드의 출처는 char를 unsigned char로 인식하는 cpu 환경에서 구동되는 함수였고 이 때문에 char로 캐스팅 되어있던 것.

  > 결론적으로 unsigned char를 사용하는 것이 함수의 이식성을 높이기 때문에 unsigned char로 타입캐스팅 해주는 것이 바람직해보인다.

## memcpy

- memcpy와 memove의 차이

  두 함수 모두 주어진 크기만큼 메모리를 복사한다는 공통점이 있으나 dst와 src가 overlap되는 부분이 있다면 서로 다르게 동작한다.

  만약 'aabbcc'라는 값이 있다고 치자. src첫번째 a의 주소, dst가 두번째 a의 주소를 갖고 있고 size는 3byte만큼 복사할 때 memcpy의 경우 dst결과는 'aaacc'가 되고 memove의 경우 'aabcc'가 나온다. memcpy는 복사하는 부분이 overlap 되었을 때 덮어씌운 값을 계속 복사하지만 memove는 가장 원본의 데이터를 복사하기 때문이다.

## strlcpy

- strcpy, strncpy, strlcpy

  strcpy는 주어진 src문자를 dst에 복사하고 자동으로 끝에 null을 붙여주는 함수이다. 그런데 이 함수는 memory corruption이라는 취약점을 갖고 있다.

  💡 memory corruption이란?

  Memory Corruption이란, Program이 개발자가 의도하지 않은 방식으로 동작하여, 접근해서는 안되는 메모리 영역에 Write기능을 수행하여 Memory 내용이 corruption(변형)되는 것을 의미 합니다. 대부분 Programming error로 인해 Memory Corruption이 발생합니다.

  **[출처]** [Memory Corruption 과 Use-After-Free(UAF)](https://blog.naver.com/aepkoreanet/221644359105)

  💡 취약점 공격(exploit)이란?

  **취약점 공격** 또는 **익스플로잇** (exploit)이란 [컴퓨터](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0)의 [소프트웨어](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)나 [하드웨어](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%95%98%EB%93%9C%EC%9B%A8%EC%96%B4) 및 컴퓨터 관련 전자 제품의 [버그](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EB%B2%84%EA%B7%B8), [보안 취약점](https://ko.wikipedia.org/wiki/%EB%B3%B4%EC%95%88_%EC%B7%A8%EC%95%BD%EC%A0%90) 등 설계상 결함을 이용해 공격자의 의도된 동작을 수행하도록 만들어진 절차나 일련의 [명령](<https://ko.wikipedia.org/wiki/%EB%AA%85%EB%A0%B9%EC%96%B4_(%EC%BB%B4%ED%93%A8%ED%8C%85)>), [스크립트](https://ko.wikipedia.org/wiki/%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%96%B8%EC%96%B4), [프로그램](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8) 또는 특정한 [데이터](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%A3%8C) 조각[[1]](https://ko.wikipedia.org/wiki/%EC%B7%A8%EC%95%BD%EC%A0%90_%EA%B3%B5%EA%B2%A9#cite_note-1)[[2]](https://ko.wikipedia.org/wiki/%EC%B7%A8%EC%95%BD%EC%A0%90_%EA%B3%B5%EA%B2%A9#cite_note-2)을 말하며, 이러한 것들을 사용한 공격 행위를 이르기도 한다.
  출처 : 위키

  기본적으로 길이를 검사하지 않는 함수들의 경우 memory corruption의 한 종류인 buffer overflow(bof)가 발생하기 쉽다. 길이를 검사하지 않는다는건 이와 같다.

  ```c
  #include <string.h>
  #include <stdio.h>

  int main()
  {
  	char dst[6] = "HELLO";
  	char src[10] = "aaaaaaaaa";

  	strcpy(dst, src);
  // 버퍼의 크기가 src보다 작기 때문에 BOF가 발생한다.
  // 컴파일상에서는 에러가 발생하지 않기 때문에 프로그래머가 실수하기 쉬움
  // 이로인해 exploit공격 대상이 되기 쉽다.
  	printf("%s", dst);
  }
  ```

  BOF에 취약한 대표적인 함수 : strcpy , strcmp, gets, scanf, strcat, getwd, sprintf 등

  따라서 길이를 check 하는 strncpy함수 사용을 권장하지만 이 함수 또한 BOF에서 자유롭지 못하다. strncpy는 복사할 크기를 3번째 인자로 받아 해당 크기만큼 버퍼에 복사한다. 이때 이 길이가 버퍼 크기보다 크면 컴파일 에러를 발생시킨다.(오버플로우가 나지 않도록 안전장치가 있는 셈) 만약 src가 len보다 짧거나 같으면 남은 버퍼는 모두 '\0'로 채운다(성능상 비효율적). 하지만 src > len 인 경우 null 문자를 붙여주지 않는다는 문제가 있다. 이 경우 문자열을 불러올 때 버퍼 크기 이상의 메모리 값에 접근할 수 도 있다.

  strcpy, strncpy의 이러한 결함을 보완하기 위한 함수가 strlcpy이다. strlcpy도 strncpy 처럼 len이 버퍼 크기보다 클 경우 컴파일 에러가 발생한다. 일단, len이 버퍼 크기보다 큰 경우는 없다는 것 → strcpy단점 보완, 또한 strncpy와는 달리 무조건 len에서 마지막에 넣을 null 값 자리를 확보하고 데이터를 복사한다. 이로 인해 문자열을 불러올 때 버퍼 크기 이상의 메모리값에 접근할 가능성을 차단한다. → strncpy단점 보완

- strlcpy가 문자열 포인터가 아닌 길이를 반환하는 이유

  복사한 문자열이 잘렸는지를 확인하기 위해.

  3번째 매개변수로 주어진 버퍼사이즈의 크기가 리턴값보다 작으면 복사된 문자열이 잘렸거나 제대로 복사되지 않았음을 확인할 수 있다.

  참조

  - [strlcpy()](http://egloos.zum.com/Zer5/v/509427)

## strlcat

- strlcat의 return value는 왜이럴까?

  ```c
  if (dst_len >= size)
  	return (src_len + size);
  else
  	return (dst_len + src_len);
  ```

  ```c
  //example

  Before :                || After :
  dst   | src   | size    || dst      | return
  ------------------------||--------------------
  dst\0 | src\0 | 0       || dst\0    | 3
  dst\0 | src\0 | 1       || dst\0    | 4
  dst\0 | src\0 | 2       || dst\0    | 5
  dst\0 | src\0 | 3       || dst\0    | 6
  dst\0 | src\0 | 4       || dst\0    | 6
  dst\0 | src\0 | 5       || dsts\0   | 6
  dst\0 | src\0 | 6       || dstsr\0  | 6
  dst\0 | src\0 | 7       || dstsrc\0 | 6
  dst\0 | src\0 | 8       || dstsrc\0 | 6
  ```

  return value가 size보다 작으면 잘린 값 없이 모두 이어붙여졌다는 뜻.

  💡 왜 dst_len ≥ size 조건이 필요한 것일까?

  `If dest is not nul-terminated, then strlcat will consider dest to be size in length and return size plus the length of src.`

  dest가 null종료 문자열이 아니면 dest 길이를 size의 크기로 간주한다. size가 dest길이 보다 크지 않으면 dest는 size 범위 내에서는 null 종료 문자열이 아니기 때문에 dest의 길이는 size의 크기로 치환된다. 결국 dest 길이 + src 길이를 리턴한다는 개념은 변하지 않은 것.

  결국 리턴 값을 달리해 dest가 null terminate 조건을 만족하는 문자열인지를 한번 확인하고 그 이후에 복사할 버퍼가 충분이 있는지를 체크할 수 있도록 설계된 것이다. null 종료 문자열이 맞는지 확인이 필요한 이유는 dest 버퍼를 넘어선 메모리에 접근해서 overrunning 하는 것을 방지하기 위해서이다.

  [libc.a reference](https://www.delorie.com/djgpp/doc/libc/libc_762.html)

- strlcat과 strlcpy의 의도 및 발전 과정

  이 두함수는 기존 함수들이 가진 버퍼 사이즈 미체크로 인한 1) BOF 문제(strcpy, strcat), 2) src가 버퍼 사이즈보다 클경우 발생하는 null 미종료 문자열 문제 (strncpy, strncat), 또는 3) 나머지 버퍼를 모두 null로 채우면서 발생하는 성능 비효율 문제 (strncpy, strncat)를 해결하기 위해 고안되었다.

  strlcat과 strlcpy는 사용자가 원하는 사이즈만큼 이어 붙이기 또는 복사 붙이기를 하기 위해 의도된 함수가 아니다. 이 함수들은 dst에 src를 부분적으로가 아닌 온전히 다 복사할 수 있는 충분한 버퍼 사이즈가 있는지 검증하고 작동 하기 위한 함수이다. 따라서 3번째 인자로는 sizeof를 사용해 문자열의 길이가 아닌 버퍼의 총 길이를 넣어 사용하는 것이 올바른 방법이다.

  이 두 함수가 생성하고자 하는 문자열의 총길이를 반환하는 것도 이 의도와 관련이 있다. 두 함수는 모두 생성하려는 문자열의 총 길이를 반환한다. (strlcpy : 복사할 문자열의 길이, strlcat : 문자열 두개를 붙여 생성될 총 길이). 이에 따라 3번째 매개변수로 주어진 버퍼사이즈의 크기가 리턴값보다 작으면 복사된 문자열이 잘렸거나 제대로 복사되지 않았음을 확인할 수 있다. 따라서 잘림이 발생하면 전체 문자열을 저장하는 데 필요한 바이트 수를 알 수 있으며 프로그래머는 원하는 경우 더 많은 공간을 할당하고 문자열을 다시 복사할 수 있다.

  또한 개발을 할 때 strncpy() 및 strncat()을 사용하여 문자열을 작성한 다음 strlen()을 사용하여 결과의 길이를 찾는 것이 일반적이기 때문에 유용하다. strlcpy() 및 strlcat()을 사용하면 마지막 strlen()이 더 이상 필요하지 않다.

  [strlcpy and strlcat - consistent, safe, string copy and concatenation.](https://www.sudo.ws/todd/papers/strlcpy.html)

> 여기에 적힌 글은 정답이 아닙니다. 여러 문서들을 읽으며 내린 저의 결론입니다. 이와 생각이 다르거나 잘못된 정보는 정정 부탁드립니다.
