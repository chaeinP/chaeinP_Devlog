---
title: Linux_08.positional parameters
date: '2021-09-21'
tags: ['Linux']
draft: false
summary:
---

## Positional Parameters(위치 매개 변수)란?

모든 명령어와 인자는 그 위치에 따른 변수가 부여된다. 마치 프로그래밍의 함수 처럼 쉘 파일안에 스크립트를 작성하고 실행할때마다 인자값을 다르게 넣어 각기 다른 값을 출력할 수 있다.

```s
cp /file1 /newFile
```

- 여기서 `cp`는 $0, `/file1`은 $1, `/newFile`은 $2 라는 위치 매개 변수에 담긴다.
- 10번째 아규먼트 부터는 `${10}`처럼 중괄호를 사용해야한다.
- 인자의 개수는 $#으로 들어간다 여기서 $#은 2가 된다. 인자가 2개라서!
- 인자의 이름은 $@ 또는 $\*안에 리스트로 부여된다.

## Special shell variables

- 로그인 shell의 PID : $$
- 현재 작업 디렉토리 : $PWD
- 부모 프로세스 ID : $PPID
