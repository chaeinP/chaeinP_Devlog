---
title: AWS_CLI 실습
date: '2022-03-01'
tags: ['AWS']
draft: false
summary:
---

aws cli를 사용하기 위해서는 먼저 aws cli를 설치해야한다.
[aws cli 설치](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-install.html)

## AWS access key

aws cli 또는 sdk를 사용하기 위해선 access key를 발급해야 한다. access key를 발급받을 때는 root 계정이 아닌 사용자 계정을 사용할 것을 권고한다.

- IAM 서비스에서 USER 탭을 클릭
- access key를 발급받을 유저 선택
- 보안 자격 증명 탭에서 access key 발급
- 해당 정보는 발급 당시에만 확인 가능, .csv 파일을 다운로드 해 해당 정보를 보유하고 있을 것
- 이 정보는 절대 외부로 유출되어서는 안된다.

### aws cli 설정

```
$aws configure
AWS Access Key ID [None]: 본인의 access key id
AWS Secret Access Key [None]: 본인의 access key 비밀번호
Default region name [None]: ap-northeast-2 (사용할 리전)
Default output format [None]:
```

위 설정을 완료하면 cli로 aws 서비스를 사용할 수 있다.

- 사용자 확인
  ```
  aws iam list-users
  ```

## cloud shell

aws 클라우드는 클라우드 쉘을 제공한다. 클라우드 쉘은 브라우저에서 바로 클라우드 리소스에 접근할 수 있는 서비스이다. 모든 리젼에서 사용할 수 있는 것은 아니기 때문에 클라우드 쉘 기능을 사용하기 위해선 이 서비스를 제공하는 리젼을 선택해야 한다.

[클라우드 쉘 사용 가능 리전](https://docs.aws.amazon.com/general/latest/gr/cloudshell.html)
