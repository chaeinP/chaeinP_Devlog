---
title: AWS_IAM이란?
date: '2022-03-01'
tags: ['AWS']
draft: false
summary:
---

## IAM이란?

AWS 리소스에 대한 엑세스를 안전하게 제어할 수 있는 웹서비스로 리전 기반의 서비스가 아닌 글로벌 서비스이다.

- AWS 가입으로 생성된 계정은 root 계정이다.
- root 계정으로 aws 서비스를 직접 사용하는 것을 지양해야한다.
- root 계정은 새로운 사용자를 생성하거나 사용자에게 접근 권한을 부여할 때에만 사용한다.
- 사용자는 그룹으로 묶일 수 있다.
- 사용자는 다수의 그룹에 속할 수 있다.

## IAM 주요 기능

1. 계정에 대한 공유 엑세스 : 암호나 액세스 키를 공유하지 않고도 AWS 계정의 리소스를 관리하고 사용할 수 있는 권한을 다른 사람에게 부여 가능
2. 세분화된 권한 : 리소스에 따라 여러 사람에게 다양한 권한 제공
3. EC2 애플리케이션이 다른 aws 리소스 접근을 위한 권한 부여 : ec2 애플리케이션이 s3버킷이나 dynamoDB와 같은 aws 리소스에 접근할 수 있도록 자격 증명 제공
4. 멀티 팩터 인증(MFA) : 보안을 위해 사용자에 2팩터 인증 추가 가능. 계정 작업을 위해 암호나 액세스 키뿐 아니라 특별히 구성된 디바이스 코드도 제공
5. 자격 증명 연동 : 기업 네트워크나 인터넷 자격 증명 공급자와 같은 다른 곳에 이미 암호가 있는 사용자에게 AWS 계정에 대한 임시 액세스 권한 부여 가능

## IAM Policy Structure

```json
//example
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListAllMyBuckets", "s3:GetBucketLocation"],
      "Resource": "arn:aws:s3:::*"
    },
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::BUCKET-NAME",
      "Condition": { "StringLike": { "s3:prefix": ["", "home/", "home/${aws:username}/"] } }
    },
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::BUCKET-NAME/home/${aws:username}",
        "arn:aws:s3:::BUCKET-NAME/home/${aws:username}/*"
      ]
    }
  ]
}
```

- Version: 정책의 처리에 사용할 언어 구문 규칙을 의미한다. 가장 최신 버전은 2012-10-17이다.
- Id(optional) : 정책 식별자. ID 요소를 설정할 수 있는 서비스의 경우에는 UUID(GUID)를 값으로 사용하거나, UUID를 ID 일부로 사용하여 고유성을 확보하는 것이 좋다.

```
"Id": "cd3ad3d9-2776-4ef1-a904-4c229d1642ee"
```

- Statement: 구체적인 정책 내용을 담는다. 각 정책은 {}에 담겨 배열의 요소로 작성된다.
  - Sid(optional): 정책 요소 식별자
  - Effect: 해당 정책에 대한 access 허가, 거부에 대한 정보 (Allow, Deny)
  - Principal: 해당 정책이 적용될 계정/사용자
  - Action : Effect에 따라 허가/거부되는 API 리스트
  - Resource: Action이 적용될 리소스의 목록
  - Condition(optional): 정책이 적용되거나 적용되지 않을 조건

## IAM password policy

AWS에서는 계정 보호를 위해 사용자에게 적용할 password policy를 생성할 수 있다.

- 비밀번호의 최소 길이
- 비밀번호에 포함되어야할 문자 종류
- 비밀번호 변경 허가/강제
- 이전 비밀번호 재사용 거부

## MFA(Multi Factor Authentication)

계정 비밀번호와 함께 사용자 소유 디바이스를 사용해 계정 보안을 강화하는 방법으로 계정 비밀번호가 탈취돼도 해커가 사용자 소유의 물리적 디바이스를 가지고 있지 않는 한 계정 접근이 불가능하다. aws는 루트 계정을 포함한 모든 사용자 계정에 MFA를 적용할 것을 강력히 권고한다.

### MFA devices options

- Virtual MFA machine : 여러 계정을 하나의 디바이스에서 통합 관리할 수 있다.

  - Google Authenticator: phone only
  - Authy: multi device

- Universal 2nd Factor Security Key : 물리적인 키 장치로 여러 계정을 하나의 security key로 관리가 가능하다.

  - Yubikey by Yubiko(3rd party of aws)

- Hardware Key Fob MFA Device

  - Provided by Gemalto(3rd party of aws)

- Hardware Key Fob MFA Device for
  AWS GovCloud (US)
  - Provided by SurePassId(3rd party of aws)
