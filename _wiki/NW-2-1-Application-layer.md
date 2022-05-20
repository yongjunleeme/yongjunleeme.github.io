---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:12:30 +0900
updated : 2022-05-20 17:18:47 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 요약

2.1.1 네트워크 애플리케이션 구조
서버와 클라의 프로세스가 통신

서버 
- Permanent IP address
- always ON

클라
- Dynamic IP address(연결될 때마다 변경 가능)
- 간헐적 ON
- 서버와 통신, 자기들끼리 통신 X

P2P
- 커뮤니케이션 두 프로세스 모두 User 호스트
- 임의 유저끼리 다이렉트 커뮤니케이션
- 관리 복잡

2.1.2 프로세스간 통신 식별자, 호스트 식별자 
호스트가 아닌 프로세스가 커뮤니케이션, 목적지도 호스트가 아닌 프로세스(우편물의 최종 도착지가 집이 아니라 집에 있는 사람인 것 같이)

프로세스는 Host IP로 식별할 수 없고 포트번호로
- HTTP는 포트번호 80(well known port number)
- mail server: 25(well known port number)

IP 주소(128. 119. 245. 12)로 호스트 식별
- 실제 IP 주소는 32비트, 각 3글자가 8비트(Maximum 255)
- 호스트는 IP 주소로 프로세스는 포트번호로 식별

2.1.3 애플리케이션 이용 가능한 트랜스포트 서비스

애플리케이션 프로세스는 개발자에 의해 제어
트랜스포트 계층은 OS에 의해 제어
트랜스포트와 네트워크 사이의 Door 같은 계층 --> Socket

app 종류별 트랜스포트 서비스 requirements
- 파일 전송, 웹 트랜잭션: data integrity(온전함)  
예) e-mail, 웹 문서, 텍스트 메시지
-  audio: tolerate(용인하다) some loss
-  인터랙티브 게임: low delay
-  스트리밍: minimum throughput(단위 시간 내 일정 프레임이 있어야만 재생할 수 있으므로)
-  elastic apps(예: FTP): time sensitive yes and no 파일 전송이 좀 느려져도 괜찮으므로
-  Stored audio/video: loss-tolerant 버퍼링되어 있다면 중간에 데이터가 늦게 도착하더라도 재생 지속 가능하므로

트랜스포트 프로토콜 서비스
1.  TCP  
1-1) reliable transport
1-2) flow control
	- TCP의 버퍼가 유한하므로 애플리케이션에서 읽어가는 속도보다 네트워크가 더 빨리 많이 들어오면 Overflow 조절
	- 받는 쪽을 조절하는 게 아니라 주는 쪽을 조절  
1-3) connection-oriented
-  상대방 TCP와 핸드쉐이킹을 해서 개념적인 커넥션 과정을 거친 후에 데이터를 주고 받는다.
-  프로토콜 컨트롤 오버헤드가 크다  
1-4) congestion control
-  네트워크 홉 바이 홉의 경로에도 혼잡이 생기면 이를 조절한다.
1-5)does not provide: timing, minimum throughput guarantee, security

2.  UDP  
- unreliable data transfer
	- 네트워크 계층 호스트까지 배달, UDP가 목적지 호스트 내 어느 포트인지 찾아서 올려주는 역할  
- does not provide: reliability, flow control, congestion control, timing, throughput guarantee, security, or connection setup

timing, minimum throughput guarantee, security 다루는 프로토콜 현재 사용 X  
연구와 개발이 되었지만 수업에서 배우지는 X

2.1.4 인터넷 전송 프로토콜이 제공하는 서비스

TCP  
- data-integrity가 중요하므로  
- e-mail, remote terminal access, web, file transfer

TCP or UDP  
- timing, minimum throughput guarantee가 중요하므로  
- streaming multimedia  
- Internet telephony

 2.1.5 애플리케이션 계층 프로토콜 
교환하는 메시지 type 정의  
메시지 Syntax 정의  
Semantic 정의(어떻게 해석되어야 하는가)  
rules(언제, 어떻게 등)

2가지 애플리케이션 프로토콜 
1.  open  
    RFCs에 모두 정의돼 있음  
    interoperability(정보 처리 상호 운영)에 좋다  
    예) HTTP, SMTP
    
2.  proprietary  
    오픈되지 않아서 정확히 알 수 없음  
    예) Skype

2.2 웹과 HTTP
2.2.1 HTTP 개요
- Hypertext Transfer Protocol
- 클라 - 브라우저, 서버 - 웹서버
- 과정
	- 웹은 데이터 인테그리티가 중요하므로 UDP가 아닌 TCP에 요청
	- HTTP 애플리케이션 프로토콜로 트랜스포트 서비스를 TCP에 요청
	- 요청 메시지를 전달하기 전에 클라가 서버에게 TCP 커넥션 
	- 서버가 억셉하는 핸드 쉐이킹 이후 메시지 주고 받는다.
- Stateless
	- 서버는 유저가 보낸 요청을 기억하지 않는다.
	- 반대로 State 유지하려면? 오버헤드가 매우 커질 것
		- HTTP 히스토리를 모두 저장해야 하고 크래시날 경우 서버와 클라가 실제 주고 받은 메시지를 정의하고 구별하기 어려우므로

2.2.2 비지속 연결과 지속 연결
1) non-persistent HTTP 과정
클라: TCP 커넥션 요청
서버: 80번 포트로 억셉(커넥션=클라/서버에 소켓 만들어졌고 요청과 응답 소켓 통해 진행)
클라: 요청 메시지 TCP 커넥션 소켓으로 보냄
서버: 응답 메시지 & 요청 오브젝트(Base HTML 파일만) 소켓으로 전송. 커넥션 종료.
클라: HTML 파일 포함 응답메시지 디스플레이. HTML 파싱해 HTML 파일에 연결된 다른 여러 오브젝트들 찾아내 이를 요청하기 위해 다시 TCP 커넥션 요청

2) persistent HTTP 과정
커넥션 종료 없이 지속 연결

1) non-persistent 웹페이지 응답시간
2회 RTT(Round Trip Time) * 오브젝트 수
- 느려서 병렬(오브젝트 수만큼 소켓 열어서) Fetch할 수도 있음
- 트랜스포트 아래 계층은 OS가 관리하므로 소켓 개수 많아질수록 오버헤드, 커넥션수 만큼 버퍼가 필요하므로 비효율

2) persistent 웹페이지 응답시간
- 커넥션 맺고 지속적 소켓 오픈
- 3RTT

2.2.3 HTTP 메시지 포맷
아스키 코드(사람이 읽을 수 있는) 포맷
첫째줄: 요청라인
중간: 헤더라인
바디: 선택적
\r\n 캐리지 리턴으로 구분

HTTP/1.0  
- 서버에 요청하는 류의 메서드  
- GET, POST  
- HEAD 메서드: 응답 메시지는 보내되 실제로 오브젝트를 나한테 보내지는 마(테스트 목적)

HTTP/1.1  
- 클라이언트에서 서버에 데이터 업데이트  
- GET, POST, HEAD  
- PUT - 서버에 업로드  
- DELETE

2.2.4 사용자와 서버 간의 상용작용: 쿠키
HTTP stateless이지만 서버에서 클라의 트랜잭션 히스토리를 유지하기 위해서 쿠키를 사용한다.
1) 서버측 response 메시지에 쿠키 헤더라인을 추가
2) 클라도 요청 메시지에 쿠키 헤더라인 포함
3) 브라우저에서는 쿠키 파일을 유지
4) 웹사이트에서는 백엔드 데이터베이스를 유지
예) 최초 이커머시 방문
- 유니크 ID 할당
- 백엔드 데이터에 ID를 위한 엔트리 생성 
- 서버에서 응답보낼 때 ID를 알려주고
- 그다음부터는 요청할 때마다 ID를 달고 요청
- ID를 저장해놓기 위해 쿠키파일을 사용

쿠키 사용 예
유저 세션 상태 유지(메일)
- 세션은 1회로 끝나지만 쿠키를 사용하면 인증 없이 다음에도 접근

2.2.5 웹 캐싱
서버 없이 요청하는 것이 목적
- 브라우저가 HTTP 요청을 서버가 아닌 캐시(프록시 서버)로 보냄
	- 원하는 오브젝트가 있으면 바로 받아옴
- 캐시는 서버? 클라? 둘 다
- 누가 로컬 사이트를 설치? 대학, 회사, Residential ISP
- Why 캐싱?
	- 응답시간 감소
	- 트래픽 많아질수록 대역폭이 큰 액세스 링크를 구매해야 하므로
	- 많은 네트워크 인프라 없이 효율적인 딜리버리 가능
	
웹 캐시 예
- 오브젝트 사이즈: 1M bits
- 평균 rate: 15/sec
- 트래픽 유입속도: 초당 15 Mbps

- 트래픽 인텐서티 : 트래픽 유입속도 / 대역폭(15.4Mbps)
- 거의 1인데 0.7만 넘어가도 급격히 딜레이가 증가한다.

--> 더 비싼 넓은 대역폭 액세스링크를 구매?
--> 웹 캐시 하나 설치가 더 경제적

웹 캐시의 문제 오리지널 서버의 업데이트 미반영
Conditional GET 메서드로 해결
- 최신 캐시 버전을 반영
- HTTP 요청 헤더라인에 `If-modified-since: <date>`
	- 업데이트 안 되었으면 응답은 보내지만 오브젝트는 안 보냄
		- `304 Not Modified`
	- 업데이트 되었으면
		- 200과 함께 오브젝트를 보냄
		
## 목차

애플리케이션 계층

- 2.1 네트워크 애플리케이션의 원리
- 2.1.1 네트워크 애플리케이션 구조
- 2.1.2 프로세스 간 통신
- 2.1.3 애플리케이션이 이용 가능한 트랜스포트 서비스
- 2.1.4 인터넷 전송 프로토콜이 제공하는 서비스
- 2.1.5 애플리케이션 계층 프로토콜
- 2.1.6 이 책에서 다루는 네트워크 애플리케이션

- 2.2웹과 HTTP
- 2.2.1 HTTP 개요
- 2.2.2 비지속 연결과 지속 연결
- 2.2.3 HTTP 메시지 포맷
- 2.2.4 사용자와 서버 간의 상호작용: 쿠키
- 2.2.5 웹 캐싱

## 애플리케이션 계층

3가지 주제
1) 애플리케이션의 구조, 개념
2) 실제 프로토콜
3) Socket API 

## 2.1 네트워크 애플리케이션의 원리

### 2.1.1 네트워크 애플리케이션 구조

2가지 구조(네트워크 프로토콜)
- 1) 클라, 서버
- 2) P2P

1-1) 서버
항상 ON인 호스트
Permanent IP address

1-2) 클라
자기들끼리는 통신 X
서버와 통신
인터넷에 간헐적으로 연결
Dynamic IP 주소(연결될 때마다 변경 가능)

서버와 클라의 프로세스가 통신하는 것

2) P2P
커뮤니케이션하는 두 프로세스 모두 User 호스트에
임의로 유저끼리 다이렉트로 커뮤니케이션
피어끼리 소비와 공급
Self scalability - 별도 서버 없이 피어끼리 
관리 복잡

통신하는 프로세스 2종류
1) 클라이언트 프로세스
init
2) 서버 프로세스
response

P2P -> 클라 서버 프로세스 모두 수행

### 2.1.2 프로세스 간 통신

커뮤니케이션하는 실체? 호스트가 아닌 프로그램(프로세스)
목적지도 호스트가 아니라 프로세스(우편물의 최종 도착지가 집이 아니라 인물인 것 같이)

Host IP로 식별할 수 없고 포트번호(프로세스를 구별하는 값)
HTTP(웹)은 포트번호 80으로 고정(well known port number)
mail server: 25(well known port number)

IP 주소: 128. 119. 245. 12(호스트 식별)
실제 IP 주소는 32비트, 각 3글자가 8비트(Maximum 255)

호스트는 IP 주소로 프로세스는 포트번호로 식별

### 2.1.3 애플리케이션이 이용 가능한 트랜스포트 서비스

애플리케이션 프로세스는 개발자에 의해
트랜스포트 이하는 OS에 의해 컨트롤
트랜스포트와 네트워크 사이의 Door같은 계층 --> Socket 

애플리케이션에서 트랜스포트 계층에 원하는 대목?
- 파일 전송나 웹 트랜잭션: data integrity(온전함)
예) e-mail, 웹 문서, 텍스트 메시지
- audio: tolerate(용인하다) some loss
- 인터랙티브 게임: low delay
- 스트리밍: minimum throughput(단위 시간 내 일정 프레임이 있어야만 재생할 수 있으므로)
- elastic apps(예: FTP): 파일 전송이 좀 느려져도 괜찮.. 
- Stored audio/video: loss-tolerant 버퍼링되어 있다면 중간에 데이터가 늦게 도착하더라도 재생 지속 가능하므로
security

트랜스포트 프로토콜 서비스
1) TCP
1-1) reliable transport
1-2) flow control
- TCP들은 버퍼를 가지고 있는데 버퍼가 유한하므로 애플리케이션에서 읽어가는 속도보다 네트워크가 더 빨리 많이 들어오면 Overflow 조절
- 받는 쪽을 조절하는 게 아니라 주는 쪽을 조절
1-3) connection-oriented
- 상대방 TCP와 핸드쉐이킹을 해서 개념적인 커넥션 과정을 거친 후에 데이터를 주고 받는다.
- 프로토콜 컨트롤 오버헤드가 크다
1-4) congestion control
- 네트워크 홉 바이 홉의 경로에도 혼잡이 생기면 이를 조절한다.
- 네트워크의 혼잡을 피하기 위해
1-5)does not provide: timing, minimum throughput guarantee, security

2) UDP
unreliable data transfer
- 네트워크 계층 호스트까지 배달, UDP가 목적지 호스트 내 어느 포트인지 찾아서 올려주는 역할
does not provide: reliability, flow control, congestion control, timing, throughput guarantee, security, or connection setup

timing, minimum throughput guarantee, security 다루는 프로토콜 현재 사용 X
연구와 개발이 되었지만 수업에서 배우지는 X

### 2.1.4 인터넷 전송 프로토콜이 제공하는 서비스

TCP
data-integrity가 중요하므로
e-mail, remote terminal access, web, file transfer 
 
TCP or UDP
timing, minimum throughput guarantee가 중요하므로
streaming multimedia
Internet telephony

### 2.1.5 애플리케이션 계층 프로토콜

교환하는 메시지 type 정의
메시지 Syntax 정의
Semantic 정의(어떻게 해석되어야 하는가)
rules(언제, 어떻게 등)

2가지 애플리케이션 프로토콜
1) open
RFCs에 모두 정의돼 있음
interoperability(정보 처리 상호 운영)에 좋다
예) HTTP, SMTP

2) proprietary 
오픈되지 않아서 정확히 알 수 없음
예) Skype

## 2.2웹과 HTTP

웹을 구성하고 있는 HTTP 프로토콜
웹 페이지는 여러 오브젝트로 구성
기반은 HTML 파일 이를 참조하는 여러 파일들
오브젝트는 HTML 파일, JPEG 이미지, 오디오 파일 등이다. 

### 2.2.1 HTTP 개요
Hypertext Transfer Protocol
웹 이외에도 멀티미디어 스트리밍이나 이메일에서도 일부 사용
클라이언트 - 브라우저
서버구조 - 웹서버
 
다양한 애플리케이션 프로세스가 하나의 애플리케이션 프로토콜를 따라서 통신한다.

HTTP 애플리케이션 프로토콜은 트랜스포트 서비스를 TCP에 요청
웹은 데이터 인테그리티가 중요하므로 UDP가 아닌 TCP에 요청
리퀘스트 메시지를 전달하기 전에 TCP 커넥션을 맺어야 한다.
클라이언트가 TCP 커넥션을 서버에게 시작한다.
서버가 억셉하는 핸드 쉐이킹이 이뤄지면 메시지를 주고 받는다.
Stateless: 서버는 유저가 보낸 리퀘스트를 기억하지 않는다.
반대로 State를 유지하려면 오버헤드가 커지면서 매우 복잡해진다. 
- HTTP 히스토리 저장하는 것은 물론 크래시가 날 경우 서버와 클라가 실제 받은 메시지를 구별하기도 어려우므로

### 2.2.2 비지속 연결과 지속 연결

1) 최초 HTTP는 non-persistent HTTP
서버측에서 TCP 커넥션을 하나 보내고나면 자동으로 클로즈
오브젝트가 여러 개인데 하나마다 커넥션을 맺으면 비효율적이므로 이후에는
2) persistent HTTP 
1회 TCP 커넥션을 통해서 여러 오브젝트를 딜리버리
 
1) non-persistent HTTP 과정
클라: TCP 커넥션 요청 
서버: 80번 포트로 억셉
클라: 리퀘스트 메시지(URL 포함)를 TCP 커넥션 소켓으로 보냄
커넥션을 맺으면 TCP의 소켓이 별도로 열린다.
서버: 소켓으로 전달된다.리퀘스트 메시지를 보고 응답 메시지를 작성한다. 여기에 요청된 오브젝트를 포함한다. 이 오브젝트는 Base HTML 파일만 들어간다. 소켓으로 내려보낸다. 커넥션 종료
클라: HTML 파일이 포함된 응답메시지를 받아서 디스플레이. HTML 파일을 파싱해서 다른 여러 오브젝트들을 찾아낸다. 각 오브젝트 별로 다시 TCP 커넥션을 요청한다.

TCP 커넥션 맺어졌다
- 클라/서버에 소켓이 만들어진다는 이야기
- 요청/응답을 소켓을 통해서

1-1) non-persistent HTTP 응답시간

RTT(Round Trip Time): 매우 작은 패킷이 클라 --> 서버 --> 클라로 돌아오는 시간
응답시간: 2RTT + file transmission time

웹페이지 전체 응답시간
- Non-persistent(HTTP 1.0)
2회의 RTT * 오브젝트수
느려서 parallel(오브젝트 수만큼 소켓을 연다)로 fetch할 수도 있음
트랜스포트 아래 계층은 OS가 관리하므로 소켓 개수가 많아질수록 오버헤드, 커넥션수만큼의 버퍼가 필요하므로 비효율
- Persistent 
커넥션 맺고 소켓 오픈
3RTT

### 2.2.3 HTTP 메시지 포맷
1) 요청 메시지
아스키 코드로 표현
1줄 : 요청라인
중간 : 헤더라인
바디는 선택적
\r\n <- 캐리지 리턴으로 구분
소켓으로 주고 받으면서 왜 또 정보들을 적어서 보내는가? 나중에 알려줌
 
Header filed name : value
GET /index.html <-- 이 파일을 받겠다는 의미
User-Agent: 클라 브라우저 버전
Accept-Language: 여러 언어버전 중 선택
Connection: Keep-alive <-- 가능하면 Persistent로 요청

HTTP/1.0
서버에 요청하는 류의 메서드
GET, POST
HEAD 메서드: 응답 메시지는 보내되 실제로 오브젝트를 나한테 보내지는 마(테스트 목적)

HTTP/1.1
클라이언트에서 서버에 데이터 업데이트
GET, POST,  HEAD
PUT - 서버에 업로드
DELETE

2) 응답 메시지

### 2.2.4 사용자와 서버 간의 상호작용: 쿠키
HTTP stateless이지만 서버에서 클라의 트랜잭션 히스토리를 유지하기 위해서 쿠키를 사용한다.
1) 서버측 response 메시지에 쿠키 헤더라인을 추가
2) 클라도 요청 메시지에 쿠키 헤더라인 포함
3) 브라우저에서는 쿠키 파일을 유지
4) 웹사이트에서는 백엔드 데이터베이스를 유지
- 유니크 ID 할당
- 백엔드 데이터에 ID를 위한 엔트리 생성 
- 서버에서 응답보낼 때 ID를 알려주고
- 그다음부터는 요청할 때마다 ID를 달고 요청
- ID를 저장해놓기 위해 쿠키파일을 사용
 
```shell
{"set-cookie": 1678}
{"cookie": 1678}
```

쿠키 사용 예
유저 세션 상태 유지(메일)
- 세션은 1회로 끝나지만 쿠키를 사용하면 인증 없이 다음에도 접근

### 2.2.5 웹 캐싱
서버 없이 요청하는 것이 목적
 
- 브라우저가 HTTP 요청을 서버가 아닌 캐시(프록시 서버)로 보냄
	- 원하는 오브젝트가 있으면 바로 받아옴
- 캐시는 서버? 클라? 둘 다
- 누가 로컬 사이트를 설치? 대학, 회사, Residential ISP
- Why 캐싱?
	- 응답시간 감소
	- 트래픽 많아질수록 대역폭이 큰 액세스 링크를 구매해야 하므로
	- 많은 네트워크 인프라 없이 효율적인 딜리버리 가능

웹 캐시 예
- 오브젝트 사이즈: 1M bits
- 평균 rate: 15/sec
- 트래픽 유입속도: 초당 15 Mbps

- 트래픽 인텐서티 : 트래픽 유입속도 / 대역폭(15.4Mbps)
- 거의 1인데 0.7만 넘어가도 급격히 딜레이가 증가한다.

--> 더 비싼 넓은 대역폭 액세스링크를 구매?
--> 웹 캐시 하나 설치가 더 경제적

웹 캐시의 문제 오리지널 서버의 업데이트 미반영
Conditional GET 메서드로 해결
- 최신 캐시 버전을 반영
- HTTP 요청 헤더라인에 `If-modified-since: <date>`
	- 업데이트 안 되었으면 응답은 보내지만 오브젝트는 안 보냄
		- `304 Not Modified`
	- 업데이트 되었으면
		- 200과 함께 오브젝트를 보냄


연습문제를 꼭 풀어보라.
  

## Reference

- [컴퓨터 네트워크 - 이화여대 이미정 교수님](http://kocw.net/home/search/kemView.do?kemId=1046412)
