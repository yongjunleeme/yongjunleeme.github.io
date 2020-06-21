---
layout  : wiki
title   : 
summary : 
date    : 2020-05-11 21:46:27 +0900
updated : 2020-06-20 23:17:47 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


## Database

### DB 스케일링 업을 무한정 할 수 있는데 안하는 이유

#### 스케일 아웃

- 서버를 여러 대 추가해 확장하는 방법
- 각 서버에 걸리는 부하를 균등하게 해주는 '로드밸런싱' 필요
    - 한 대가 다운되더라도 다른 서버로 제공 가능하지만
    - 모든 서버가 동일한 데이터를 가지고 있어야 하므로 데이터 변화가 적은 '웹 서비스'에 적합

#### 스케일 업

- 서버에 고성능 부품을 교환하는 방법
- CPU나 RAM을 추가하려면 여유 슬롯이 있어야 하며 그렇지 않은 경우 서버 자체를 교체해야 함
    - 서버 한 대에 부하가 집중되므로 장애 시 영향을 크게 받을 수 있음
    - 한 대 서버에서 모든 데이터를 처리하므로 데이터 갱신이 빈번하게 일어나는 '데이터베이스 서버'에 적합한 방식


### 정규화 기준

- 애트리뷰트(필드)를 나눠서 작은 릴레이션으로 분해하는 작업

### NoSQL을 사용하는 이유 및 사례, 개념

#### 개념

'Not Only SQL'
- 스키마 없이 동작하며 대부분 클러스터(서버들)에서 실행할 목적으로 만들어졌기에 관계형 모델을 사용하지 않는다.

#### 등장 배경

- 트래픽이 늘면서 스케일 업에 따른 비용부담이 커졌다. 이에 분산 저장을 목표로 스케일 아웃에 사용할 NoSQL 등장

#### 장점

- NOSQL 수평확장, 유연한 추가저장, 데이터 형식 - 예)페이스북, 트위터 게시물 저장, 실시간으로 쌓이는 로그 데이터

#### 종류

- [나무위키](https://namu.wiki/w/NoSQL)

### Database가 물리적으로 저장되는 과정

- [데이터베이스의 기본 구조](http://egloos.zum.com/sweeper/v/3003333)


### DB Indexing? 왜 사용하는가?

- 인덱스는 칼럼 값과 레코드 주소를 키와 값 쌍의 인덱스로 만드는 것.
- 정렬 상태를 유지하므로 값 탐색은 빠르지만 값 추가, 삭제 등의 수정 속도는 느리다.
    - 저장 성능을 희생하고 읽기 속도를 높이는 기능
- DBMS의 인덱스도 인덱스가 많은 테이블은 INSERT, UPDATE, DELETE 처리가 느리다. 하지만 SELECT는 빠르게 처리


#### DB replication? 사용하는 이유?

- Replication이란 두 개 이상의 DBMS 시스템을 Master와 Slave로 나눠 동일한 데이터를 저장하는 방식
- 그냥 복붙 아닌가? SPOF를 방지하기 위함이 아닐까?

#### sharding

- 같은 테이블 스키마를 가진 데이터를 다수의 데이터베이스에 분산하여 저장하는 방법을 의미합니다.
- Horizontal Partitioning이라고 볼 수 있습니다.
- 샤딩(Sharding)을 적용한다는 것은?
    - 프로그래밍, 운영적인 복잡도는 더 높아지는 단점이 있습니다.
- 가능하면 Sharding을 피하거나 지연시킬 수 있는 방법을 찾는 것이 우선되어야 합니다.
    - Read 부하가 크다면?
        - Cache나 Database의 Replication을 적용하는 것도 하나의 방법입니다.
    - Table의 일부 컬럼만 자주 사용한다면?
        - Vertically Partition도 하나의 방법입니다.

#### 모델링 절차

- 업무파악 -> 개념적 모델링(ERD) -> 논리적 모델링(ERD) -> 물리적 모델링(코딩)

## Deploy

### 제가 만든 서버가 동시접속자를 몇명이나 견딜 수 있는지 어떻게 아나요?

- 현업에선 품질팀에서 성능검증을 통해 품질 검증 후 상용망에 적용합니다.
- Jmeter같은 simulator 활용하여 부하테스트
- [AWS 기반 부하 테스트](https://aws.amazon.com/ko/blogs/korea/how-to-loading-test-based-on-aws/)

### 웹서비스를 실제 운영하다 보면 크로스스크립트, 클릭 인젝션 같은 공격이 들어올 때가 있다던데 AWS에서 막을 방법이 있을까요?

- 어플리케이션 단의 공격 이전 인프라 단에서 막는 장비들이 있습니다.
- AWS shield(DDoS), WAF(Web Application Firewall), CloudFront(CDN)
- Market place에서 UTM솔루션 구매(Sophos, Fortinet 등)
- DNS 제공해주는 Gabia, Cloudflare 등을 이용하면 WAF 제공


- 대규모 트래픽 처리한 경험?
- Docker, 사용이유? Container, Image
- 클라우드 컴퓨팅
- AWS Lambda?
- 람다 함수와 파라미터 관련 event, context
- Kubernetes
- Serverless
- 쓰로틀
- Firebase
- Reverse proxy
- load balancer
- MSA
- 배포 절차

## DevOps

### 소프트웨어 생명 주기 관련 데브옵스의 역할, 하는 일

- 개발과 운영(서버, DB, 네트워크)이 하나로
    - 소규모 업데이트 자주, 마이크로 서비스
- 개발 라이프사이클을 이해하고 개발과 운영 양쪽에 충분한 지식과 경험을 보유한 사람?


#### CI/CD

- 지속적 통합 지속적 전달
    - 지속적 통합은 자동화된 빌드 및 테스트가 수행된 후, 개발자가 코드 변경 사항을 중앙 리포지토리에 정기적으로 병합하는 데브옵스 소프트웨어 개발 방식
    - 지속적 전달(Continuous Delivery)은 프로덕션에 릴리스하기 위한 코드 변경이 자동으로 준비되는 소프트웨어 개발 방식


## 암호화

### JWT 구성요소 세가지

- 헤더.페이로드.서명
    - 헤더- 토큰타입, 암호화 알고리즘
    `{
        "typ": "JWT",
        "alg": "HS256"
    }`
        - base64로 인코딩
    - 페이로드 - 클레임 정보
        - 1. 등록된 클레임
            - `iss`: 토큰 발급자(issuer)
            - `sub`: 토큰 제목(subject)
            - `aud`: 토큰 대상자(audience)
            - `exp`: 토큰 만료기간(expiration), 시간은 NumericDate형식(예:1480849147370)
            - `nbf`: Not Before를 의미, 토큰 활성날짜
            - `idt`: 토큰이 발급된 시간(issued at), 이 값을 통해 토큰의 age가 얼마나 되었는지 판단 가능
            - `jti`: JWT 고유 식별자로 중복처리 방지를 위해 사용, 1회용 토큰에 유용
        - 2. 공개(Public) 클레임
            - 충돌 방지를 위해 URI 형식
            `{
            "http://" : true
            }
            `
        - 3. 비공개(private) 클레임
            - 클라이언트와 서버 협의하에 사용되는 클레임 네임, 공개 클레임과 달리 중복 충돌 가능성이 있으므로 유의
            `{
            "username": "yongjun"
            }
            `
                - base64로 인코딩
    - 서명 - 헤더의 인코딩값과 정보의 인코딩값을 합친 후 시크릿키로 해시 적용
        - 문자열 인코딩이 아닌 hex -> base64 인코딩

- base64 인코딩 시 = 문자(패딩)가 붙으면 url로 쓸 수 없으므로 지워야 한다.
- [JWT 디버거](https://jwt.io/) 에서 토큰을 디코딩 테스

### Access Token, Refresh Token

액세스 토큰의 짧은 유효기간은 잦은 로그인을 야기하고 이는 곧 보안 취약점이다. 이에 액세스 토큰 만료 시 리프레시 토큰을 발행해 액세스토큰을 보완한다.
라고 하지만 아니라고 하는 사람도 있다.

- 액세스 토큰은 보통 세션에 저장
    - 액세스 토큰과 달리 리프레시토큰은 일반적으로 회원 DB에 저장
- 보통 SPA에서는 리프레시 토큰을 제공하지 않는다. 리프레시 토큰으로 액세스 토큰을 영원히 갱신할 수 있으므로

### 페북/인스타는 로그인 만료기간이 엄청 긴데, 어떻게 보안 관리를 하는지 아는가?

- 리프레시 토큰의 만료기간이 일정 기간 남았을 때(카카오- 리프레시토큰 유지기간 60일, 갱신기간 1개월) 갱신

### [JWT 변조 공격 어떻게 대처 하겠는지?](https://code-machina.github.io/2019/09/01/Security-On-JSON-Web-Token.html)

- JWT는 비밀키를 사용하여 서명되는데 이 때 HMAC 알고리즘 사용
    - 또한 RSA를 이용한 공개키/비밀키 쌍을 이용할 수 있다.

#### None 해싱 알고리즘

- 공격자가 해싱 알고리즘을 none으로 변경
- 몇몇 알고리즘은 none 알고리즘으로 서명한 토큰을 유효한 토큰으로 인식
- 공격자가 RSA 서명을 HMAC 알고리즘으로 변환해 퍼블릭키를 통해 복호화, 암호화하면 재서명이 가능
- 예방책
    - 아래 라이브러리 주의?
        - node-jsonwebtoken
        - pyjwt
        - namshi/jose
        - php-jwt
        - jsjwt

#### 토큰 하이재킹

- 토큰 가로채기
- 예방책
    - 사용자 컨텍스트를 토큰에 생성
        - 무작위 문자열의 SHA256해시 토큰 내 포함
        - HttpOnly, Secure, SameSite, Cooke prefixes와 같은 보안 설정
            - `HttpOnly` 쿠키는 자바스크립트의 `Document.cookie` API를 통해 접근이 불가능하다. 이들은 오로지 서버로만 전송된다. 예를 들어 서버사이드 세션은 자바스크립트가 접근할 필요가 없다. 이에 `HttpOnly` 플래그가 반드시 설정되어야 한다.
            - `Secure` 쿠키는 HTTPS 프로토콜을 통한 암호 요청만을 통해 전송할 수 있따. 이를테면 Http 통신을 하는 안전하지 않은 사이트는 Secure 디렉티브로 설정된 쿠키를 구울 수 없다.
            - `SameSite` 쿠키는 서버로 하여금 쿠키가 교차 도메인 전송을 차단하게 만든다. CSRF 보안을 제공한다.
            - 쿠키 접두사는 브라우저에 특정 속성이 필요하다고 전달한다. `__Secure-`가 대표적인 예이며 `__Host-`가 prefix 설정된 경우 `Path=/`와 `Secure` 속ㅈ성이 모두 필요하다고 브라우저에게 알려준다.

#### 사용자에 의한 명시적 토큰 철회/취소

#### 토큰 정보 노출

## Bcrypt 작동원리? 왜 사용? 단점?

- SHA2, SHA3는 너무 빨라서 레인보우 테이블(해시값 검색에 최적화된 테이블)도 빠르게 만들 수 있지만 Bcrpyt는 속도 조절 가능

### JWT 인증 방식 vs 세션 인증 방식

- 애플리케이션을 완전히 Stateless한 상태로 유지할 필요가 없다면 세션 시스템을 사용

### 암호화 Tool(Bcrypt, Argon2) 를 사용하는 이유?

### JWT를 활용하여 이상적인 Scalable한 인증서버를 만들려고 하고자 할때 어떻게 구성을 할 것인가?

## Django

### ORM과 쿼리 중 무엇을 선호? 차이점?

### Why Django?

- 배터리팩, Build Fast
- Simple syntax, MVC Core 아키텍처, ORM, 미들웨어 서포트, 유닛 테스트
- 확장성 있는 프로젝트 적합, 큰 트래픽과 데이터 핸들링 가능
- 보안 - 클릭 인젝션, SQL 인젝션 등 방지 용이

- MVC 패턴에 대하여(Django MVT 방식대로 설명)
- Django vs Flask
- Django vs Java Spring
- 같은 API에 Get요청을 여러 번 보내서 똑같은 요청을 할 경우, 효율성을 증대시키는 방법이 있는데 장고는 이를 자동으로 해준다. 이게 뭔지 알고있는지?(정답은 캐싱)
- DRF
- APP 어떤 기준으로 분리했는지?
- 클래스 인스턴스 활용
- 일대다, 다대다 관계
- request 요청시 동작 순서
- Django 장단점
- test.py, 유닛테스트, 사용한 모듈
- 로컬 DB와 서버 DB의 구조 차이로 인한 migrate가 conflict 날 경우 해결법?

## Python

- 데코레이터, 이터레이터, 제너레이터

### Decorator 사용시 주의점

- 데코레이터는 함수를 빠르게 변경할 때 사용 가능

### 파이썬 메모리 관리

- 개별적인 힙을 사용해서 메모리를 유지, 내부에 힙 매니저 구현
- 인터프리터가 접근, 프로그래머는 사용할 수 없음
- 빌트인 가비지 컬렉터를 통해 사용되지 않는 메모리의 힙공간을 관리

### Call by value, Call by reference

- Python은 passed by assignment
    - 불변 타입 -> Call by value
    - 가변 타입 -> Call by reference

- Call by value - 인자로 받은 값을 복사해서 처리, 복사하므로 메모리 증가
- Call by Reference - 인자로 받은 값의 주소를 참조해 직접 값에 영향을 준다. 값에 영향을 주는 대신 빠르다.

### Iterators는 무엇?

- 배열과 같은 객체로서 다음 요소로 이동하는 것이 가능하다. `next()` 메소드로 데이터를 순차적으로 호출한다.

### Iterators와 Iterable의 차이는?

- iterable은 하나씩 차례로 반환 가능한 객체. __iter__, __getitem__ 메소드로 정의된 클래스들이 iterable한 것.
- iterable을 iterator로 변환하고 싶다면 iter() 함수를 사용

### Closures?

- 함수 객체로서 다른 함수를 반환
- 일반 함수와 다르게 자신의 영역 밖에서 호출된 함수의 변수값과 레퍼런스를 복사, 저장, 접근 가능하게 한다

### 질문

- Why Python?
- 파이썬 가비지콜렉터 동작방식, 그리고 집적 구현한다면 어떤식으로 하겠는가?
- GCC(GNU Compiler Collection)이란 (파이썬 엔진과 연관지어서)
- 파이썬 인터프리터가 기존 컴파일러 언어에 비해서 퍼포먼스가 낮은데도 불구하고 파이썬이 주언어인 이유?

이 의문에 가장 중요한 대답은 "생산성이 높기 때문"이다. 이는 부인할 수 없는 현실이다. 또한 "먼저 개발하라! 그리고 나서 성능을 개선하라."는 취지에 가장 부합하는 언어이다. 개발을 먼저 할 시 장점은 협업 시(디자인, 기획 등) 비교하여 성능을 높이기 용이하다.

플랫폼 독립적 언어, 개발 기간 다축에 초점을 맞춘 언어 
예- 윈도우에서 개발한 코드로 맥과 리눅스에서 실행 가능 - OS에 인터르리터가 설치돼 있으므로, C는 컴파일 다시 진행해야

- 스크립트 언어 vs 컴파일 언어
- 디스크럽터
- 정규표현식
- 클래스 상속 시 메서드 실행 방식
- PyPy가 CPython보다 빠른 이유
- (JS와 비교하여) python의 장단점
- 비동기/동기 설명, 왜 쓰는지?
- 코루틴 설명, 왜 쓰는지?
- PEP8이 무엇인지?
- Python2와 Python3의 차이
- 프로젝트에서 이터레이터, 제너레이터 사용했는지
- 데코레이터 프로젝트 어디서 사용했는지, 왜 썼는지?
- 디스크립터가 무엇?
- Private Class, Public Class?

## API

### REST API / REST 탄생 배경 / RESTful

### 서로 다른 API로 요청이 동시에 들어오면서 해당 API들이 내 DB의 같은테이블 하나를조회한다고 가정했을때, 병목현상이 생길텐데 어떻게 처리할 것인가?

### 이미지 업로드 어떻게 하는지?

### 자동 문서 도구 툴

### Graphql과 RestfulAPI를 비교했을 때 Graphql의 장단점은?

## Network

### HTTP/3

- TCP
    - 번거로운 3 Way Handshake
        - HTTP/1 -> HTTP/2 핸드쉐이크 횟수 최소화
    -  HOLB(Head of line Blocking)
        - 주고받은 시퀀스 번호를 참고해 정확한 순서대로 패킷을 재조립해야 한다.
        - 손실은 그냥 넘어가지 않고 다시 보내야 다음 단계 진행 가능
        - 이러한 이유로 발생하는 병목현상
- HTTP/3 - QUIC(Quick UDP Internet Connection)
    - TCP 문제해결하고 레이턴시 한계를 뛰어넘고자 구글이 개발
    - 순서가 존재히자 않는 독립적 패킷 사용
    - 목적지만 정해져 있다면 중간에 어딜가도 상관 없음, 핸드쉐이크 과정 필요 없음
        - RTT(Round Trip Time) - 3RTT(TCP) -> 1RTT(QUIC)
            - 첫번쨰 핸드쉐이크에 필요한 정보와 데이터까지 전송
    - 커스터마이징 용이
        - 꽉 들어찬 TCP의 헤더와 달리 아무것도 없는 UDP의 헤더
    - 재전송 모호성을 해결하기 위해 패킷의 헤에 별도 패킷 번호 공간 부여
    - 클라이언트 IP가 바뀌어도 연결이 유지됨
        - TCP 연결 끊어지면 다시 3 Way Hanshake거쳐야
        - 예) wi-fi에서 셀룰러로 전환
        - QUIC은 Connection ID를 사용해 서버와 연. 이는 랜덤한 값을 뿐 클라이언트 IP와는 무관
        
- web server(Apache, Nginx)? 장점?
- 
- HTTP 1.0/1.1/2.0/3.0의 특징 (Keepalive -> TCP/IP -> Header Compression -> UDP)
- 웹브라우저 동작원리(프론트 ->브라우저 -> 백엔드 -> DB -> OS 순으로)
- HTTP vs HTTPS
- HTTP 요청/응답 헤더
- TCP/IP와 UDP의 특징과 차이에 대해. 각각의 header
- localStorage, sessionStorage, Cookies / 그 외에 브라우저에 저장하는 방법?
- url에 [google.com](http://google.com) 주소 치면 어떤 과정 생기나?
- 단순히 글을 조회하지만, 글을 조회할때 조회수가 올라간다 이럴때 http 메소드는 뭘써야 하겠는가?
- HTTP request 구성
- Response code
- 500대 코드 에러 처리
- 서버가 죽으면 어떻게 500 에러를 내는 것인가
- www
- protocol
- DNS
- 크로스 브라우징
- 웹 성능과 관련된 이슈
- 서버 사이드 렌더링, 클라이언트 사이드 렌더링
- HTTP 인증 방식
- 데이터 주고받을때 json형식밖에 없나요? 왜 제이슨을 쓰나요
- 이미지나 데이터 로딩이 많을때 로딩시간 최적화 방법은?
- Socket.io vs WebSocket
- 동기 vs 비동기
- 비동기 처리는 왜 하나?
- CORS 통신
- GET, POST 메서드
- Frame, Packet, Segment, Datagram

## Git

- git, github
- 협업 시 git 관리 어떻게 했는가?
- 코드 리뷰를 해본 적이 있는지?
- Git Flow / branch 관리
- git rebase / squash
- git branch 어떻게 따는가?
- 이미 서비스가 런칭된 상태인데 기획자가 바뀐 기획의도를 가지고 웹서비스를 다시 만들라고 한다면 git을 어떻게 사용할 것인가?
- Git Conflict 상황에서 해결 방법

## 자료구조

- FIFO
- FIFO 반대 개념?
- Set / Set을 쓰면 좋은 예시?
- 숫자가 정리되지 않은 리스트가 있고, 해당 리스트에 특정 값이 있는지 확인해야한다. 효율적인 방법을 제시해 보아라 (정답은 binary tree)
- List의 resizing이 언제 일어나는가? 크기가 어떻게 변하는가? 두배로 늘린다고 하면 문제점? 얼마만큼의 분량을 재할당 하는게 효율적인가?
- Tree vs Graph. 깊이 탐색과 너비 탐색
- Array vs Linked list
- HashTable
- Stack
- Queue
- Binary Heap
- Red-Black Tree
- B+ Tree

## 운영체제

### 프로세스와 스레드 차이

- 프로세스 : 자신만의 고유 공간과 자원을 할당받아 사용
- 스레드 : 다른 스레드와 공간과 자원을 공유하면서 사용

- 프로세스 메모리상에서 실행 중인 프로그램의 인스턴스를 말하며, 스레드는 이 프로세스 안세서 실행되는 흐름의 단위다.
    - 프로세스마다 최소 하나의 스레드를 가지며 주소공간을 할당받는다(code, data, heap, stack).
    - 스레드는 이중에 stack만 할당받고 나머지 영역은 스레드끼리 공유한다.


### 멀티 프로세스로 처리 가능한데 멀티 스레드로 처리하는 이유?

- 프로세스를 생성해 자원을 할당하는 시스템 콜이 감소하면서 자원의 효율적 관리가 가능하다.
- 프로세스간 통신(IPC)보다 스레드 사이 통신 비용이 적다.
- 하지만 멀티스레드는 공유자원으로 발생하는 문제 해결을 위해 동기화에 신경써야 한다.




## 기타

- CDN 써보았는지? 개념은?
- 정렬에 대해서 아는가?
- 총 4대의 서버가 존재하고 동시에 크론탭을 돌릴 때 어떻게 처리?
- Scrum 방식
- 레거시 코드
- Scale up / Scale out
- 인증/인가
- 시멘틱 마크업? 왜 준수해야 하나?
- 객체 지향 프로그래밍
- 함수형 프로그래밍
- 레이지 로딩
- 클린아키텍쳐
- 부하테스트
- TDD
- Agile
- 0.2 + 0.1의 결과값?
- Reference Error
- Garbage collector
- Serializer
- Linux, 명령어
- CRUD
- IDE
- Compiler VS Interpreter
- Thread process
- PIG란(Apache PIG인듯)
- Hash, Hash 충돌, 해결책
- 추상화란?
- Call by Reference Vs. Call by Value
- PWA
- GC 작동 방식
- 메모리 누수가 발생할 수 있는 경우
- Duck Typing
- 딥러닝
- ML
- AI
- IoT
- 분산락
- 64bit CPU와 32bit CPU 차이
- CVS, SVN, Git
- 웹 서버(Web Server)와 웹 어플리케이션 서버(WAS)의 차이
- Servlet과 JSP

### Memcached와 Redis의 차이

- Memcached
    - 명료함을 위해
    - 멀티스레드 지원, 스케일업을 통한 작업 처리 가능
    
- Redis
    - 다양한 용도
    - 싱글 스레드
    - 다양한 데이터 구조 활용 가능
    - 스냅샷 - 특정 시점 저장 보관 가능
- Maven과 Gradle의 차이 
  
  
## Link

- [gabia 라이브러리](http://library.gabia.com/contents/infrahosting/1222)
- [Database의 샤딩이란?](https://nesoy.github.io/articles/2018-05/Database-Shard)
- [JSON Web Token이 뭘까?](https://velopert.com/2389)
- [Cache Redis vs. Memcached](https://medium.com/@chrisjune_13837/redis-vs-memcached-10e796ddd717)
- [Python 은 call-by-value 일까 call-by-reference 일까](https://www.pymoon.com/entry/Python-%EC%9D%80-callbyvalue-%EC%9D%BC%EA%B9%8C-callbyreference-%EC%9D%BC%EA%B9%8C)
- [Python 기술 면접 대비](https://shelling203.tistory.com/31)
- [JSON 웹 토큰 보안](https://code-machina.github.io/2019/09/01/Security-On-JSON-Web-Token.html)
- [HTTP/3는 왜 UDP를 선택한 것일까?](https://evan-moon.github.io/2019/10/08/what-is-http3/)
