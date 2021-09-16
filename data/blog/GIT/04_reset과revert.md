---
title: GIT_git reset 과 revert
date: '2021-09-16'
tags: ['GIT']
draft: false
summary: reset과 revert의 차이,  reset으로 squash 구현하기
---

> 이 포스트는 [gitbook](https://git-scm.com/book/ko/v2)을 보고 정리한 study log입니다.

git은 세개의 트리로 구성돼있다.

- HEAD : 마지막 커밋 스냅샷, 다음 커밋의 부모 커밋
- Index (staging area) : 다음에 커밋할 스냅샷
- 워킹 디렉토리 : 샌드박스(자유로운 공간)

![깃 워크플로](https://git-scm.com/book/en/v2/images/reset-workflow.png) 출처 : [깃북](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Reset-%EB%AA%85%ED%99%95%ED%9E%88-%EC%95%8C%EA%B3%A0-%EA%B0%80%EA%B8%B0)

## reset의 역할

### 1. HEAD 이동(--soft)

```
git reset --soft HEAD~
```

`HEAD~`는 현재 커밋의 부모 커밋을 의미한다. `--soft`옵션을 사용한 경우 인덱스나 워킹 디렉토리는 그대로 놔두고 브랜치가 가리키는 커밋만 이전으로 되돌린다. 만약 `커밋1 <- 커밋2 <- 커밋3(HEAD)`인 상황에서 위 명령어를 실행하면 만약 `커밋1 <- 커밋2(HEAD) <- 커밋3`로 HEAD가 이동한는 것이다. 워킹 디렉토리와 인덱스는 동일하기 때문에 만약 `git commit`을 실행하면 커밋3는 마치 `git commit --amend` 처럼 같은 커밋 3 내용이 덮어 씌워진다.

### 2. Index 업데이트(--mixed, default)

```
git reset HEAD~
git reset --mixed HEAD~
```

`--mixed`옵션은 워킹 디렉토리는 그대로 유지하고 인덱스를 이동한 HEAD의 커밋 데이터로 교체한다. reset 명령을 실행할 때 추가 옵션을 적지 않으면 `--mixed`가 기본으로 실행된다. 커밋과 add 명령어까지 되돌리는 것이다.

### 3. working 디렉토리 업데이트(--hard)

```
git reset --hard HEAD~
```

위 명령을 통해 `git add` 와 `git commit` 명령으로 생성한 마지막 커밋을 되돌린다. 그리고 워킹 디렉토리의 내용까지도 되돌린다.

이 명령은 위험하니 신중하게 사용해야 한다. git에서 얼마 없는 데이터 삭제 방법 중 하나이기 때문이다. reset 전 현재 작업 내용을 커밋해놓지 않았다면 `reset --hard`명령어 이후에는 작업하던 내용으로 절대 돌아올 수 없다.

![](https://blog.kakaocdn.net/dn/8Eb7o/btqAN396L0G/wlczLcAozoncW4JVepUTvk/img.png)

### 특정 경로에 따라 reset 하기

일부 파일만 reset 하는 방법도 있다.

```
git reset 리셋할 파일 ||
git reset --mixed HEAD 리셋할 파일 (HEAD가 가리키는 커밋 데이터로 데이터 되돌리기)
git reset 특정커밋 리셋할 파일 (특정 커밋 내 파일 데이터로 데이터 되돌리기)
```

만약 마지막 커밋 이후 몇가지 수정을 해 수정 파일들을 모두 staging area에 올렸다고 하자. 이 때 몇가지 파일들을 staging area에서 다시 워킹 디렉토리로 돌리고 싶다. 커밋 직전 수정 파일들을 각각 따로 커밋할 때 주로 이런 상황이 발생한다. 이 때 위의 명령어를 실행하면 해당 파일의 index 상태가 HEAD가 가리키는 커밋의 상태로 돌아간다. 한마디로 staged된 파일이 unstaged상태로 돌아가는 것이다. 그러나 워킹 디렉토리 데이터는 변함이 없다.

**분명히 나중에 이게 뭔소리야? 할게 뻔한 나를 위한 더 친절한 설명**

만약 마지막 커밋된 데이터가 1이었다고 치자. 마지막 커밋 이후 이 1을 2로 바꾸고 git add로 staged 상태를 만들었다. 이 2로 바꾼 파일을 다시 unstaged 상태로 바꾸고 싶다. 그러면 위의 명령어를 실행한다. 이때 index는 마지막 커밋이 저장하고 있는 상태 값으로 교체된다. 당연히 마지막 커밋에는 2로 수정된 파일이 index에 올라가 있지 않았으니 2는 unstaged 상태로 되돌아오는 것이다. 그러나 `--mixed 옵션`으로인해 해당 명령어가 워킹 디렉토리까지 영향을 미치진 않아서 2로 수정한 작업까지 1로 되돌아가지는 않는다.

## reset으로 커밋 합치기(squash 구현)

만약 `커밋1 <= 커밋2-1 <= 커밋2-2(HEAD)`인 상황에서 2-1과 2-2커밋을 합치고 싶다고 하자.

- 먼저 `git reset --soft HEAD~2`로 HEAD만 커밋 1로 이동한다.
- HEAD가 커밋 1로 이동되고 index와 워킹 디렉토리는 커밋 2-2상태일 때 새로운 커밋을 수행한다. `git commit -m '커밋 2'`
- `git log`를 확인해보면 커밋 2-1과 커밋 2-2는 사라지고 커밋 2-2의 스냅샷이 커밋 2로 교체되었다.

## reset과 revert의 차이

reset은 어떤 옵션을 취하든 HEAD가 된 커밋의 이후 커밋은 모조리 삭제한다. 따라서 과거로 돌아가는 건 큰 결심이 필요하다. 옵션을 통해 현재 워킹 디렉토리나 인덱스 데이터는 보존할 수 있지만 커밋했던 기록들까지 살릴 수 없다. 예를들어 `커밋 1 <= 커밋2 <= 커밋3(HEAD)` 상황에서 reset으로 HEAD의 위치를 커밋1로 돌렸다면 옵션에 따라 커밋 3의 워킹디렉토리나 인덱스 데이터는 보존되지만 다시는 커밋2나 커밋3 로그는 사라진다. (`git reflog`를 사용하면 reset으로 유실된 커밋들을 확인할 수 있다.)

그러나 revert의 경우 `커밋1 <= 커밋2 <= 커밋3 <= 커밋1(HEAD)`처럼 이전 커밋으로 이동하는 것 역시 새로운 커밋으로 추가돼 이전 커밋 이력을 훼손시키지 않는다. reset에 비해 안전한 방법이다.
