---
title: C_file descriptor(fd)
date: '2021-11-24'
tags: ['C', '42Seoul']
draft: false
summary: fd, open(), read()
---

> 이 포스트는 42Seoul 본과정 get_next_line 과제를 위해 공부한 내용을 기록한 스터디로그입니다.

## fd (file descriptor)

fd란 시스템으로부터 할당 받은 파일을 대표하는 음이 아닌 정수 값을 의미한다. (유닉스 시스템에서 파일은 일반적인 정규파일부터 디렉토리, 소켓, 파이프, 블록 디바이스 등등 모든 것을 의미한다.) fd가 음이 아닌 정수값으로 표현되는 이유는 이 값이 프로세스에서 열린 파일의 목록을 관리하는 fd 테이블의 인덱스 값이기 때문이다.

프로세스가 실행 중에 파일을 open하면 커널은 해당 프로세스의 파일 디스크립터 숫자 중에 사용하지 않는 가장 작은 값을 할당한다. 여기서 0, 1, 2값은 이미 표준 입력(Standard Input), 표준 출력(Standard Output), 표준 에러(Standard Error)로 할당되어있기 때문에 그 다음값부터 부여된다. 파일디스크립터는 0 ~ OPEN_MAX까지 값을 가질 수 있으며, OPEN_MAX는 플랫폼에 따라 다르다.

| 파일 디스크립터 | 목적     | POSIX         | stdio 스트림 |
| --------------- | -------- | ------------- | ------------ |
| 0               | 표준입력 | STDIN_FILENO  | stdin        |
| 1               | 표준출력 | STDOUT_FILENO | stdout       |
| 2               | 표준에러 | STDERR_FILENO | stderr       |

위 세 FD는 프로그램이 시작할 떄 셸의 디스크립터의 복사본을 상속받고 셸은 보통 위 FD가 언제나 열린채로 동작한다. 위 번호를 사용할때는 가능한 "UNISTD.H"에 정의된 POSIX이름을 쓰는편이 좋다.

fd테이블의 각 항목은 파일 테이블로의 포인터를 담고 있다. 따라서 FD 번호와 이 번호가 가지고있는 테이블 주소를 이용해 시스템의 파일을 참조할 수 있다.

참조

[리눅스-파일디스크립터](https://dev-ahn.tistory.com/96)

## open()

```c
//헤더
#include <fcntl.h>

int open(const char *path, int oflag);
int open(const char *filepath, int flag, mode_t mode);
```

- 파라미터

  - path는 open할 파일명에 대한 절대경로 또는 상대경로를 의미한다.
  - oflag는 파일을 어떤 목적으로 open할 것이며, 파일을 초기화할 지 여부등을 지정한다.
    - O_RDONLY : 파일을 읽기 전용으로 open합니다.
    - O_WRONLY : 파일을 쓰기 전용으로 open합니다.
    - O_RDWR : 파일을 읽고 쓰기를 모두 할 수 있도록 open합니다.
    - 이 외에도 여러 상수들이 존재한다.
  - mode는 파일의 접근권한을 명시한다.

- 리턴
  - 0 이상의 정수 : 파일이 정상적으로 열렸음을 의미하며 해당 fd를 반환
  - -1 : 오류가 발생한 것이며 오류 내용은 errno에 설정

참조

[파일을 읽거나 쓰기 위해 열기](https://www.it-note.kr/19)
[C/C++ open 함수 - 파일 생성/읽기/쓰기](https://bubble-dev.tistory.com/entry/CC-open-%ED%95%A8%EC%88%98-%ED%8C%8C%EC%9D%BC-%EC%83%9D%EC%84%B1-%EC%9D%BD%EA%B8%B0-%EC%93%B0%EA%B8%B0)

## close()

```c
#include <unistd.h>

int close(int fd);
```

open()으로 연 파일은 close로 닫아주어야 한다. 그렇지 않으면 프로그램이 종료될 떄까지 계속 남아 컴퓨터 자원을 낭비하게 된다.

## read()

```c
//헤더
#include <unistd.h>
//프로토타입
ssize_t read(int fildes, void *buf, size_t nbyte);
```

- ssize_t 자료형은?

  size_t가 음이 아닌 정수형을 표현한다면 ssize_t는 모든 정수형을 의미한다. size_t가 unsignd int랑 비슷하다면 ssize_t는 int랑 비슷한 셈

- 파라미터

  - fildes는 읽을 파일의 디스크립터를 의미
  - buf는 읽어들인 데이터를 저장할 버퍼를 의미
  - nbyte는 읽어들일 데이터의 최대 길이를 의미한다.(buf사이즈보다 작아야함)

- 리턴

  - 읽어들인 데이터의 길이
  - 무조건 nbyte가 리턴되지는 않는다. 더이상 읽을 데이터가 없으면 거기까지만 읽고 반환하기 떄문
  - 더이상 읽을 데이터가 없으면 0
  - 에러가 발생하면 -1을 반환

  💡EOF란
  C언어는 운영체제와 상관없이 파일의 끝에 도달하면 언제나 특별한 값을 반환한다. 그 값을 EOF라고 하며 실제로 이값은 -1을 나타낸다.
