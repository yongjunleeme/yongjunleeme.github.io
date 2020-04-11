---
layout  : wiki
title   : dev-common-sense 
summary : 
date    : 2020-04-06 14:50:51 +0900
updated : 2020-04-09 15:59:28 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## RESTful API

### REST란?

REST는 Representational State Transfer의 약자로, 월드와이드웹과 같은 분산 하이퍼미디어 시스템에서 운영되는 소프트웨어 아키텍처스타일이다. 2000년에 [Roy T. Fielding](https://en.wikipedia.org/wiki/Roy_Fielding) 이 박사학위 [논문](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) 에서 처음 소개했다.

REST에 ~ful이라는 형용사형 어미를 붙여 ~한 API라는 표현으로 사용된다. REST의 기본 원칙을 지킨 서비스 디자인은 'RESTful'하다고 표현할 수 있다.

REST가 디자인 패턴이다, 아키텍처다 많은 이야기가 존재하는데 하나의 아키텍처로 볼 수 있다. 말하자면 REST는 리소스 중심 아키텍처다. API 설계의 중심에 자원이 있고 HTTP 메서드를 통해 자원을 처리하도록 설계하는 것이다. 

### 중심 규칙

- URI는 정보의 자원을 표현해야 한다.
- 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE 등)으로 표현한다.

|Resource|GET|PUT|POST|DELETE|
|------|-----|-----|------|-----|
|Collection URI, such as http://example.com/resources/ |컬렉션에 속한 자원들의 URI나 그 상세사항의 목록을 보여준다. |전체 컬렉션은 다른 컬렉션으로 교체한다.|해당 컬렉션에 속하는 새로운 자원을 생성한다. 자원의 URI는 시스템에 의해 할당된다. |전체 컬렉션을 삭제한다. |
|Element URI, such as http://example.com/resources/item17 |요청한 컬렉션 내 자원을 반환한다.|해당 자원을 수정한다.|해당 자원에 귀속되는 새로운 자원을 생성한다. |해당 컬렉션내 자원을 삭제한다.|

- 이외에 `PATCH`라는 메서드도 있다. PUT은 자원 전체를 교체하고 PATCH는 일부를 변경한다는 의미. update 이벤트에서 PUT보다 적합하다는 평가.

## REST의 6가지 특징

1) Uniform(유니폼 인터페이스)
Uniform Interface는 URI로 지정한 리소스 조작을 통일해 한정적인 인터페이스를 사용하는 아키텍처 스타일

2) Stateless(무상태성)
작업을 위한 상태정보를 따로 저장하고 관리하지 않는다. 예를 들어 세션 정보나 쿠키 정보를 별도로 저장하지 않으므로 API 서버는 요청만 처리하면 된다. 이에 서비스의 자유도가 좊아지고 불필요한 정보를 관리하지 않아도 되므로 구현이 단순해진다.

3) Cacheable(캐시가능)
REST의 특징 중 하나는 HTTP 웹 표준을 그대로 사용하는 것이다. 따라서 HTTP가 가진 캐싱 기능을 적용할 수 있다. Last-modified 태그나 E-Tag를 이용해 캐싱 구현이 가능한 것이다.

4) Self-descriptiveness(자체 표현 구조)
API 메시지만 보고 쉽게 이해할 수 있는 표현 구조

5) Client - Server 구조
REST 서버는 API 제공, 클라이언트는 사용자 인증, 컨텍스트(세션, 로그인 정보) 등을 직접 관리하는 구조로 각자 역할이 구분. 개발 내용이 명확해지고 의존성이 줄어든다.

6) 계층형 구조
다중 계층으로 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상 유연성을 둘 수 있다. Proxy, 게이트웨이 같은 네트워크 기반 중간매체를 사용할 수 있게 한다.

### REST API 디자인 가이드

- URI는 정보의 자원을 표현(리스소명은 동사보다 명사를 사용)

```
    GET /members/delete/1
```

위에는 REST를 제대로 적용하지 않은 URI. URI는 자원을 표현하는 데 중점을 두어야 한다. delete와 같은 행위 표현이 들어가면 안 된다.

- POST : 생성
- GET : 조회
- PUT : 수정(전체)
- PATCH : 업데이트

### URI 설계 시 주의점

- 슬래시 구분자(/)는 계층 관계를 나타낸다.
- 리소스간 관계 표현

```
    /리소스명/리소스 ID/관계가 있는 다른 리소스명
    ex)    GET : /users/{userid}/devices (일반적으로 소유 ‘has’의 관계를 표현할 때)
```

만약 관계명이 복잡하다면 이를 서브 리소스에 명시적으로 표현 가능. 예를 들어 '좋아하는' 디바이스 목록을 표현할 경우 다음과 같은 형태

```
    GET : /users/{userid}/likes/devices (관계명이 애매하거나 구체적 표현이 필요할 때)
```

### 자원을 표현하는 Collection과 Document

Collection과 Document에 관해 알면 URI 설계가 한 층 쉬워진다. Document는 단순 문서로 이해해도 되고 한 객체라고 이해해도 된다. 컬렉션은 문서들의 집합, 객체들의 집합이라고 볼 수 있다.

```
    http:// restapi.example.com/sports/soccer
```

위 URI를 보면 sports라는 컬렉션과 soccer라는 도큐먼트로 표현했다. 좀더 예를 들어보자면

```
    http:// restapi.example.com/sports/soccer/players/13
```

sports, players 컬렉션과 soccer, 13(13번 선수)를 의미하는 도큐먼트로 URI가 이뤄진다. 컬렉션은 복수로 사용한다. 

### HTTP 응답 상태 코드

| 상태코드 | 설명                                                                                                     |
| -------- | -------------------                                                                                      |
| 200      | 클라이언트의 요청을 정상적으로 수행함                                                                    |
| 201      | 클라이언트가 어떠한 리소스 생성을 요청, 해당 리소스가 성공적으로 생성됨(POST를 통한 리소스 생성 작업 시) |
| 400      | 클라이언트의 요청이 부적절 할 경우 사용하는 응답 코드                                                    |
| 401      | 클라이언트가 인증되지 않은 상태에서 보호된 리소스를 요청했을 때 사용하는 응답 코드                      |
|          | (로그인 하지 않은 유저가 로그인 했을 때, 요청 가능한 리소스를 요청했을 때)                              |
| 403      | 유저 인증상태와 관계 없이 응답하고 싶지 않은 리소스를 클라이언트가 요청했을 때 사용하는 응답 코드       |
|          | (403 보다는 400이나 404를 사용할 것을 권고. 403 자체가 리소스가 존재한다는 뜻이기 때문에)               |
| 405      | 클라이언트가 요청한 리소스에서는 사용 불가능한 Method를 이용했을 경우 사용하는 응답 코드                 |
| 301      | 클라이언트가 요청한 리소스에 대한 URI가 변경 되었을 때 사용하는 응답 코드                               |
|          | (응답 시 Location header에 변경된 URI를 적어 줘야 합니다.)                                               |

### RESTful하게 API를 디자인한다는 것은 무엇?

1. 리소스와 행위를 명시적이고 직관적으로 분리한다.
- 리소스는 URI로 표현되는데 리소스가 가리키는 것은 명사로 표현되어야 한다.
- 행위는 HTTP Method로 표현하고, GET(조회), POST(생성), PUT(기존 entity 전체 수정), PATCH(기존 entity 일부 수정), DELETE(삭제)을 분명한 목적으로 사용한다.

2. Message는 Header와 Body를 명확하게 분리해서 사용한다.
- Entity에 대한 내용은 body에 담는다.
- 애플리케이션 서버가 행동할 판단의 근거가 되는 컨트롤 정보인 API 버전 정보, 응답받고자 하는 MIME 타입 등은 header에 담는다.
- header와 body는 http header와 http body로 나눌 수도 있고, http body에 들어가는 json 구조로 분리할 수도 있다.

3. API 버전을 관리한다.
- 환경은 항상 변하기 때문에 API의 signature가 변경될 수도 있음에 유의하자.
- 특정 API 를 변경할 때는 반드시 하위호환성을 보장해야 한다.

4. 서버와 클라이언트가 같은 방식을 사용해서 요청하도록 한다.
- 브라우저는 form-data 형식의 submit으로 보내고 서버에서는 json 형태로 보내는 식의 분리보다는 json 으로 보내든, 둘 다 form-data 형식으로 보내든 하나로 통일한다.
- 다른 말로 표현하자면 URI가 플랫폼 중립적이어야 한다.

- 어떠한 장점이 존재하는가?
    - Open API 를 제공하기 쉽다
    - 멀티플랫폼 지원 및 연동이 용이하다.
    - 원하는 타입으로 데이터를 주고 받을 수 있다.
    - 기존 웹 인프라(HTTP)를 그대로 사용할 수 있다.
- 단점은 뭐가 있을까?
    - 사용할 수 있는 메소드가 4 가지밖에 없다.
    - 분산환경에는 부적합하다.
    - HTTP 통신 모델에 대해서만 지원한다.


### Referencw

- [프론트엔드와 백엔드가 소통하는 엔드포인트, RESTful API](https://evan-moon.github.io/2020/04/07/about-restful-api/?fbclid=IwAR3sbk8REMXfwP8UpNCiSfk91LZxL4dWwL3c72OW_umetAPM90A9aObmYfI)

## Link

- [REST API 제대로 알고 사용하기](https://meetup.toast.com/posts/92) 
- [Interview_Question](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Development_common_sense)







