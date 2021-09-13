---
title: Network_http 헤더
date: '2021-09-14'
tags: ['NETWORK']
draft: false
summary: http 헤더 종류 많아도 너무 많다.
---

HTTP 헤더는 HTTP 전송에 필요한 모든 부가정보를 담기 위해 사용한다. HTTP 헤더에는 사용된 **프로토콜**, 요청과 응답의 상태를 알려주는 **상태코드**, 그리고 **바디에 담긴 데이터(표현 데이터)를 해석할 수 있는 정보(표현 헤더)**를 제공한다.

HTTP 헤더는 다음과 같이 키:값의 형태로 구성된다.

```
<field-name>:<field-value>
field-name은 대소문자를 구분하지 않는다.

//ex
Host: www.google.com
Content-Type: text/html;charset=UTF-8
```

## 표현 헤더

> 표현 데이터(body에 담긴 데이터)의 형식, 압축 방식, 자연 언어, 길이 등을 설명하는 헤더이다. 표현 헤더는 요청, 응답 둘 다 사용한다.

- <b>Content-Type</b> : 표현 데이터의 형식

```
Content-Type:text/html; charset=utf-8
Content-Type:application/json
Content-Type:Image/png
```

- <b>Content-Encoding</b> : 표현 데이터의 압축 방식
  데이터를 전달하는 곳에서 압축 후 인코딩 헤더를 추가한다. 데이터를 읽는 쪽에선 인코딩 헤더의 정보로 압축을 해제한다.

```
Content-Encoding:gzip
Content-Encoding:deflate
Content-Encoding:identity
```

- <b>Content-Language</b>: 표현 데이터의 자연 언어

```
Content-Language: ko
Content-Language: ko-KR
같은 한국어라도 두개는 다름
```

- <b>Content-Length</b>: 표현 데이터의 길이(바이트 단위)
  Content-Encoding이 아닌 Transfer-Encoding은 chunked 방식을 사용한다. chunked 방식의 인코딩은 많은 양의 데이터를 분할하여 보내기 때문에 전체 데이터의 크기를 알 수 없다. 따라서 Transfer-Encoding시에는 Content-Length헤더를 사용할 수 없다.

```
'hello'
Content-length:5
```

## 콘텐츠 협상 헤더

> 콘텐츠 협상 헤더는 요청시에만 사용한다.

- Accept : 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset: 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
- Accept-Language: 클라이언트가 선호하는 자연 언어

만약 협상 헤더가 요청에 포함되지 않으면 서버는 해당 서버의 기존 설정 값에 따라 데이터를 보낸다. 예를 들어 한국어 브라우저 에서 Accept-Language:ko 설정이 없이 요청을 보내면 외국 서버는 해당 서버의 기본언어로 설정된 영어로 데이터를 보낸다. 반면 콘텐츠 협상헤더를 추가하면 해당 서버가 그 언어를 지원하고 있을 때 클라이언트가 요청한 언어로 데이터를 전송한다.

선호하는 언어를 서버에서 지원하지 않을 때를 대비해 여러 언어에 1부터 0까지 우선순위를 부여할 수 있다.

```
ex)
Accept-Language: ko-KR;(q=1), ko;q=0, en-US;q=0,en;q=0.7
q=1은 생략가능하다.
```
