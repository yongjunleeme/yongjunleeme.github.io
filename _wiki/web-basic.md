---
layout  : wiki
title   : web-basic
summary : 
date    : 2020-03-02 14:15:19 +0900
updated : 2020-05-28 16:48:51 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 웹 통신의 큰 흐름

크롬을 실행해 주소창에 URL을 입력하면 어떤 일이 일어나는가?

### in 브라우저

1. url에 입력된 값을 브라우저 내부에서 결정된 규칙에 따라 그 의미를 조사한다.
2. 조사된 의미에 따라 HTTP Request 메시지를 만든다.
3. 만들어진 메시지를 웹 서버로 전송한다.

이 때 만들어진 메시지 전송은 브라우저가 직접하는 것이 아니다. 브라우저는 메시지를 네트워크층에 송출하는 기능이 없으므로 OS에 의뢰해 메시지를 전달한다. OS에 송신을 의뢰할 떄는 도메인명이 아니라 IP주소로 메시지를 받을 상대를 지정해야 하는데, 이 과정에서 DNS서버를 조회해야 한다.

### in 프로토콜 스택, LAN 어댑터

1. 프로토콜 스택(운영체제에 내장된 네트웤 제어용 소프트웨어)이 브라우저로부터 메시지를 받는다.
2. 브라우저로부터 받은 메시지를 패킷 속에 저장한다.
3. 수신처 주소 등의 제어정보를 덧붙인다.
4. 패킷을 LAN 어댑터에 넘긴다.
5. LAN 어댑터는 패킷을 전기신호로 변환시킨다.
6. 신호를 LAN 케이블에 송출시킨다.

프로토콜 스택은 통신 중 오류가 발생했을 때, 이 제어 정보를 사용해 고쳐 보내거나, 각종 상황을 조절하는 등 다양한 역할을 한다.

### in 허브, 스위치, 라우터

1. LAN 어댑터가 송신한 패킷은 스위칭 허브를 경유하여 인터넷 접속용 라우터에 도착한다.
2. 라우터는 패킷을 프로바이더(통신사)에 전달한다.
3. 인터넷으로 들어간다.

### in 액세스 회선, 프로바이더

1. 패킷은 인터넷의 입구에 있는 액세스 회선(통신 회선)에 의해 POP(Point Of Presence, 통신사용 라우터)까지 운반된다.
2. POP을 거쳐 인터넷의 핵심부로 들어간다.
3. 수많은 고속 라우터들 사이로 패킷이 목적지를 향해 흘러간다.

### in 방화벽, 캐시서버

1. 패킷은 인터넷 핵심부를 통과해 웹 서버측의 LAN에 도착한다.
2. 기다리고 있던 방화벽이 도착한 패킷을 검사한다.
3. 패킷이 웹 서버까지 가야하는지 가지 않아도 되는지를 판단하는 캐시서버가 존재한다.

굳이 서버까지 가지 않아도 되는 경우를 골라낸다. 액세스한 페이지의 데이터가 캐시서버에 있으면 웹 서버에 의뢰하지 않고 바로 그 값을 읽을 수 있다. 페이지의 데이터 중에 다시 이용할 수 있는 것이 있으면 캐시 서버에 저장된다.

### in 웹 서버

1. 패킷이 물리적인 웹 서버에 도착하면 웹 서버의 프로토콜 스택은 패킷을 추출해 메시지를 복원하고 웹 서버 애플리케이션에 넘긴다.
2. 메시지를 받은 웹 서버 애플리케이션은 요청 메시지에 따른 응답 메시지를 넣어 클라이언트로 보낸다.
3. 왔던 방식대로 응답 메시지가 클라이언트에게 전달된다.


## 쿠키 & 세션

- Cookie | Session
- 저장위치 : Client | Server
- 저장형식 : Text | Object
- 만료시점 : 쿠키 저장시 설정(설정 없으면 브라우저 종료 시) | 정확한 시점 모름
- 리소스 :	클라이언트의 리소스 | 서버의 리소스
- 용량제한	: 한 도메인 당 20개, 한 쿠키당 4KB | 제한 없음

### 저장 위치

- 쿠키 - 클라이언트의 웹 브라우저가 지정하는 메모리 or 하드디스크
- 세션 - 서버의 메모리에 저장

### 만료 시점

- 쿠키 - 저장할 때 Expires 속성을 정의해 무효화시키면 삭제될 날짜를 정할 수 있다
- 세션 - 클라이언트가 로그아웃하거나, 설정 시간 동안 반응이 없으면 무효화되므로 정확한 시점 알 수 없음

## HTTP Status Code

- 10x : 정보 확인
- 20x : 통신 성공
- 30x : 리다이렉트
- 40x : 클라이언트 오류
- 50x : 서버 오류

### 200번대

- 201 : Create | 생성 성공(POST)
- 202 : Accepted | 요청 접수 O, 리소스 처리 X
- 204 : No Contents | 요청 성공 O, 내용 없음

### 300번대 : 리다이렉트

- 300 : Mutilple Choice | 요청 URI에 여러 리소스가 존재
- 301 : Move Permanently | 요청 URI가 새 위치로 옮겨감
- 304 : Not Modified | 요청 URI의 내용이 변경 X

### 400번대 : 클라이언트 오류

- 400 : Bad Request | API에서 정의되지 않은 요청 들어옴
- 401 : Unauthorized | 인증 오류
- 403 : Forbidden | 권한 밖의 접근 시도
- 404 : Not Found | 요청 URI에 대한 리소스 존재 X
- 405 : Method Not Allowed | API에서 정의되지 않은 메소드 호출
- 406 : Not Acceptable | 처리 불가
- 408 : Request Timeout | 요청 대기 시간 초과
- 409 : Confilct | 모순
- 429 : Too Many Request | 요청 횟수 상한 초과

### 500번대 : 서버 오류

- 500 : Internal Server Error | 서버 내부 오류
- 502 : Bad Gateway | 게이트웨이 오류
- 503 : Service Unavailable | 서비스 이용 불가
- 504 : Gateway Timeout | 게이트웨이 시간 초과

## REST(REpresentational State Transfer)

### 특징

- Uniform Interface
    - HTTP 표준만 맞는다면, 어떤 기술도 가능한 Interface 스타일
    - 예) REST API 정의를 HTTP + JSON로 하였다면, C, Java, Python, IOS 플랫폼 등 특정 언어나 기술에 종속 받지 않고, 모든 플랫폼에 사용이 가능한 Loosely Coupling 구조

- Self-Descriptive Messages
    - API 메시지만 보고, API를 이해할 수 있는 구조 (Resource, Method를 이용해 무슨 행위를 하는지 직관적으로 이해할 수 있음)

- HATEOAS(Hypermedia As The Engine Of Application State)
    - Application의 상태(State)는 Hyperlink를 통해 전이되어야 함.
    - 서버는 현재 이용 가능한 다른 작업에 대한 하이퍼링크를 포함하여 응답해야 함.

- Resource Identification In Requests
- Resource Manipulation Through Representations
- Statelessness
    - HTTP Session과 같은 컨텍스트 저장소에 상태 정보 저장 안함
    - Request만 Message로 처리하면 되고, 컨텍스트 정보를 신경쓰지 않아도 되므로, 구현이 단순해짐.
    - 따라서, REST API 실행중 실패가 발생한 경우, Transaction 복구를 위해 기존의 상태를 저장할 필요가 있다. (POST Method 제외)
- Resource 지향 아키텍쳐 (ROA : Resource Oriented Architecture)
    - Resource 기반의 복수형 명사 형태의 정의를 권장.
- Client-Server Architecture
- Cache Ability
- Layered System
- Code On Demand(Optional)

## OAuth 인증 과정

- 용어
    - 사용자 : 계정을 가지고 있는 개인
    - 소비자 : OAuth를 사용해 서비스 제공자에게 접근하는 웹사이트 or 애플리케이션
    - 서비스 제공자 : OAuth를 통해 접근을 지원하는 웹 애플리케이션
    - 소비자 비밀번호 : 서비스 제공자에서 소비자가 자신임을 인증하기 위한 키
    - 요청 토큰 : 소비자가 사용자에게 접근권한을 인증받기 위해 필요한 정보가 담겨있음
    - 접근 토큰 : 인증 후에 사용자가 서비스 제공자가 아닌 소비자를 통해 보호 자원에 접근하기 위한 키 값

- 소비자가 서비스 제공자에게 요청토큰을 요청한다.
- 서비스 제공자가 소비자에게 요청토큰을 발급해준다.
- 소비자가 사용자를 서비스제공자로 이동시킨다. 여기서 사용자 인증이 수행된다.
- 서비스 제공자가 사용자를 소비자로 이동시킨다.
- 소비자가 접근토큰을 요청한다.
- 서비스제공자가 접근토큰을 발급한다.
- 발급된 접근토큰을 이용해서 소비자에서 사용자 정보에 접근한다.

## JWT(JSON Web Token)

JWT는 웹표준(RFC 7519)으로서 두 개체에서 JSON 객체를 사용하여 가볍고 자가수용적인 방식으로 정보를 안전성 있게 전달해줍니다.

- 헤더
- 정보(Payload)
    - Registred 클레임
    - Public 클레임
    - Private 클레임
- 서명

## CSR & SSR

- CSR : Client Side Rendering
- SSR : Server Side Rendering

### 장단점

- CSR 장단점
    - 장점
        - 트래픽 감소
            - 필요한 데이터만 받는다
        - 사용자 경험
            - 새로고침이 발생하지 않음. 사용자가 네이티브 앱과 같은 경험을 할 수 있음
    - 단점
        - 검색 엔진
            - 크롬에서 리액트로 만든 웹앱 소스를 확인하면 내용이 비어있음. 이처럼 검색엔진 크롤러가 데이터 수집에 어려움이 있을 가능성 존재
            - 구글 검색엔진은 자바스크립트 엔진이 내장되어있지만, 네이버나 다음 등 검색엔진은 크롤링에 어려움이 있어 SSR을 따로 구현해야하는 번거로움 존재

- SSR 장단점
    - 장점
        - 검색엔진 최적화
        - 초기로딩 성능개선
            - 첫 렌더링된 HTML을 클라이언트에서 전달해주기 때문에 초기로딩속도를 많이 줄여줌
    - 단점
        - 프로젝트 복잡도
            - 라우터 사용하다보면 복잡도가 높아질 수 있음
        - 성능 악화 가능성


### [HTTP3](https://evan-moon.github.io/2019/10/08/what-is-http3/)


- 나머지 정리
https://gyoogle.dev/blog/web-knowledge/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%EB%8F%99%EC%9E%91%20%EB%B0%A9%EB%B2%95.html


- cors
https://stackoverflow.com/c/wecode/questions/151


## Links

- [Interview_Question_for_Begiiner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Network#%EC%9B%B9-%ED%86%B5%EC%8B%A0%EC%9D%98-%ED%81%B0-%ED%9D%90%EB%A6%84)


