---
title: Linux_Shell Script 기능
date: '2021-09-21'
tags: ['Linux']
draft: false
summary: shell script가 뭘까?
---

## shell script란?

> 리눅스 command들을 모아좋은 ASCII Text 파일로 실행 퍼미션이 있어야 해당 파일을 실행할 수 있다.

- 실행 퍼미션 부여

  ```s
  chmod +x test.sh
  ```

- 스크립트를 실행할 subshell 이름 : shell script안에 가장 첫번째 줄에 선언한다. 아무것도 넣지 않으면 로그인 쉘과 동일한 쉘로 실행한다. 이 구문을 `셔뱅` 또는 `해시뱅`이라고 한다.

```sh
#!/bin/bash  // subshell을 bash로 실행하겠다는 의미
#!/bin/zsh   // subshell을 zsh로 실행하겠다는 의미
```

💡subshell이란?

```sh
$ echo $SHELL
/bin/zsh # login shell은 zsh
$ echo $0
zsh #현재 사용 중인 shell도 zsh

#subshell 실행
$ /bin/bash # subshell bash 실행, 이후 명령들은 #bash 로 실행된다.
$ echo $0
bash
exit #subshell 종료

#zsh쉘로 돌아옴
$ echo $0
zsh
```

- PATH 환경변수를 사용하면 sh 파일을 더 쉽게 활용할 수 있다.

```sh
$ echo $PATH
/Users/chaein/.nvm/versions/node/v14.17.3/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
#명령어를 위 경로로 순차적으로 찾아 실행하게 된다. :는 경로와 경로를 구분한다.
#만약 sh파일을 Desktop폴더에 bin폴더 안에 넣어둔다고 하면 해당 경로를 PATH에 추가했을때 경로 전체를 입력하지 않고 파일 이름만 입력해도 쉽게 실행시킬 수 있다.

$ PS1=$PS1:~/Desktop/bin
#위와 같이 변수 설정을 하면 서버 종료시 휘발되기 때문에 영구적으로 사용하고 싶을땐 .bashrc나 .zshrc 파일 안에 기록해야한다.
```
