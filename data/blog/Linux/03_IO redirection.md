---
title: Linux_IO redirection
date: '2021-09-20'
tags: ['Linux']
draft: false
summary: IO redirection 이해하기
---

## IO redirection

입출력의 방향을 바꾸다는 뜻.

```
ls -l > result.txt
```

`ls -l`의 결과를 result.txt에 저장하겠다는 뜻이다. 기존의 `ls -l`의 결과가 모니터로 출력되었던 것과는 다르게 `>`기호로 result.txt에 결과값이 들어갔다.

![unix Programs](https://blog.kakaocdn.net/dn/MvS0h/btqUeVRgYk4/x0ayD6315BcJ3flKLkGSFk/img.png)

- **Unix process**

  우리가 사용하는 명령어들에 해당한다. 위 예시에서는 `ls`가 해당한다.
  이 프로세스는 입력과 출력을 가지는데 대표적인 입력이 `Command Line arguments`, 위 예시에서는 `-l`과 같은 옵션 또는 인자를 의미한다.

- **standard output**

  화면에 출력되는 프로세스의 실행결과를 `standard output`이라고 한다.
  이 출력 방향을 모니터가 아닌 다른 곳에 출력하는 것이 `IO redirection`이고 위 예시에서는 `>`기호가 그 역할을 수행한다. standard output을 redirect 하는 경우 `>`기호 앞에는 `1`이 생략되어 있다.

- **standard error**

  standard error는 명령어가 에러가 났을때 출력되는 에러 내용을 의미한다. standard error는 `>`나 `1>`로는 redurect가 되지 않고 `2>`로 리다이렉트 해야한다.

  ```
  rm 없는파일 2> error.log
  // error.log 파일에 에러 내용이 출력된다.
  ```

  error를 에측할 수 없는 경우 standard output을 리다이렉트 하는 코드, standard error를 리다이렉트 하는 코드를 동시에 사용할 수 있다.

  ```
  rm 아무파일 1> result.txt 2> error.log
  ```

- **standard Input**

  ```
  cat hello.txt
  cat < hello.txtx
  ```

  두개의 결과는 같다. 그러나 첫번째 cat은 hello.txt를 Command Line Arguments로 받은것이고 두번째 cat은 hello.txt를 standard Input 방식으로 받은 것이다.

  ```
  head -n1 hello.txt
  head -n1 < hello.txt
  ```

  `head`는 맨앞의 10줄만 출력하는 명령어고 `-n1`은 그중 첫번째 줄만 출력하라는 뜻이다. 위의 예도 결과값은 두 문장 모두 동일하다.

- **standard Input과 standard Output**
  ```
  head -n1 < hello.txt > result.txt
  ```
