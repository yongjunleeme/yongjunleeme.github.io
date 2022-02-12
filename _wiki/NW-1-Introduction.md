---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:10:25 +0900
updated : 2022-02-13 00:10:25 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 요약
## 1.1 인터넷이란 무엇인가
### 1.1.1 인터넷의 구성요소

1. End System(호스트)
애플리케이션을 실행하고 있으므로 호스트라고 부른다.
가장자리에 있으므로 End System이라고 부른다.

2. 스위치=패킷 교환기
최종 목적지 방향으로 패킷을 전달
입력 통신 링크의 하나로 도착하는 패킷을 받아서 출력 통신 링크의 하나로 패킷을 전달
1) 라우터
- 네트워크 코어에서 사용
2) 링크 계층 스위치
- 액세스 네트워크에서 사용

3. 링크(물리적인 회선)
라우터와 호스트 or 스위치와 라우터를 연결

종단 시스템: 통신 링크와 패킷 스위치의 네트워크로 연결
패킷: 데이터를 세그먼트로 나누고 각 세그먼트에 헤더를 붙인다. 
종단 시스템: ISP(Internet Service Provider)를 통해 인터넷 접속
ISP: 패킷 스위치와 통신 링크로 이뤄진 네트워크
TCP(Transmission Control Protocol)/IP(Internet Protocol): IP 프로토콜은 라우터와 종단 시스템 사이에 송수신되는 패킷 포맷을 기술
상호 호환되는 시스템을 만들기 위해 프로토콜을 정의
소켓 인터페이스: 송신 프로그램이 따라야 하는 규칙의 집합

### 1.1.2 표준화
- IETF(Internet Engineering Task Force)에서 RFC(Request for comments, 표준)을 정한다.
- chronological하게 연대기순으로 1번부터 번호가 다 붙어 있다.

### 1.1.3 프로토콜이란 무엇인가

프로토콜은 둘 이상의 통신 개체 간에 교환되는 메시지 포맷과 순서뿐만 아니라 메시지의 송수신과 다른 이벤트에 따른 행동들을 정의한다.
예) 하드웨어로 구현된 프로토콜: 종단 시스템에 구현된 혼잡제어 프로토콜은 송수신자 간에 전송되는 패킷률을 조절

## 1.2 네트워크의 가장자리
### 1.2.1 액세스 네트워크

- 역할: 엔드시스템과 라우터를 연결
- 통신사, 케이블회사, ISP 등이 제공
- 중요척도: 대역폭, Shared 여부

1. DSL(Digital subscriber line)
Dedicated line
가정 전화회선: 데이터와 전통적인 전화 신호 동시 전달, 이들은 각각 다른 주파수 대역에서 인코드된다. 고속 다운스트림 채널, 중간속도 업스트림 채널, 일반 양방향 전화 채널로 3개의 분리된 링크(각각 다른 대역)인 것처럼 보이게 하여 하나의 전화 회선과 인터넷 연결이 동시에 DSL 링크를 공유할 수 있게 한다.
스플리터: 가정에 도착하는 데이터와 전화 신호를 분리
DSL 모뎀: 디지털 데이터를 받아서 전화선을 통해 CO(중앙국)로 전송하기 위해 고주파 신호로 변환
DSLAM: 아날로그 신호를 디지털 신호로 다시 변환
DSL 표준: 다운스트림과 업스트림 전송속도가 다르기 떄문에 접속이 비대칭(asymmetric), 일반적으로 CO로부터 8~16km 내에 있지 않으면 다른 유형의 인터넷 접속을 고려해야 한다.

집전화 --------------- 스플리터 --> DSLAM(Digital Subscriber line access multiplexer)--->전화망
                                 ^                  |
PC --> DSL 모뎀 ------|                   |------------------->인터넷

2. 케이블 네트워크
케이블 TV 회사의 기존 케이블 TV 기반구조를 이용
Shared Link(예: 한 아파트에서 나눠쓰니), 여러 사용자가 다운스트림 채널에서 다른 비디오 파일을 동시에 수신하고 있다면 실세 수신율은 다운스트림 전송률보다 상당히 작아진다.
HFC(Hybrid Fiber Coax): 공유하는 부분은 Coax, Cable Headend에서는 Fiber

번외 FFTH(Fiber to the home)
CO부터 가정까지 적접 광섬유 경로 제공

번외2 위성링크
DSL, 케이블, FTTH 가용하지 않은 곳(예: 시골)에서 1Mbps 이상

3. 홈 네트워크
LAN(local area network)
하나의 라우터에 와이파이 액세스 포인트

4. 기업 액세스 네트워크(이더넷)
Dedicated Line: ISP와 라우터를 직접 연결
라우터에 여러 이더넷 스위치가 연결되고 스위치들에 여러 엔드 포인트들이 연결

5. Wireless
Shared
LTE(Long-Term Evolution)

패킷 하나 내보내는 데 걸리는 시간: L bit / R bit 초
- 패킷 크기: L bit
- 액세스네트워크 연결 속도 transmission rate: R bit

### 1.2.2 물리매체 Link 

물리매체상에서 전자파나 광 펄스를 전파하여 전송 

피지컬 미디어 분류
1) Guided media: 물리적 와이어 사용(copper, 이더넷, fiber, coax)
2) Unguided media: 물리적 와이어 없음(라디오, 와이파이)

Link 종류
1. Twisted pair(TP): copper의 다른 이름
꼬임쌍선: 이웃하는 쌍들 간에 전기 간섭을 줄이기 위해 선들이 꼬여 있다.
주로 이더넷 케이블링 용도

2. Coax, Fiber
Coaxial cable
- 동축케이블도 꼬임쌍선처럼 2개의 구리선으로 되어 있으나 두 구리선이 평행하지 않고 동심원 형태를 이루고 있다.
- 꼬임쌍선보다 더 높은 데이터 전송률, 보통 케이블 TV에서 사용
- two concentric(중심이 같은) copper(구리) conductors, bidirectional(양방향)
- 대역폭이 넓은 링크

Fiber optic cable
- 단일 광섬유 초당 10~100기가바이트의 놀라윤 비트율
- electronic이 아니라 light pulses
- 주변 노이즈 영향 별로 X, 시그널 약해도 먼 거리 이동 가능
- 세계 여러 나라의 광역 네트워크에 사용, 해저 링크에 이용
- Transmission rate가 매우 높다(수십 Gbps)

3. 지상Radio
전자기 스펙트럼으로 신호를 전달
- 전파환경(장애물, 라디오끼리)에 영향을 받는다.
- LAN(WiFi: 와이파이가 Wireless LAN)
	- 300미터 내외
- Wide-area(Cellular)
	- 수십 킬로미터
	- LTE 10Mbps 이상 
	
4. 위성  라디오 Satellite(위성)
- 정지 위성, 저궤도 위성(고정 X)
- DSL 접속 혹은 케이블 기반 접속을 할 수 없는 지역에서 주로 이용

## 1.3 네트워크 코어

### 1.3.1 Packet Switching
- Why: 독점 사용하는 회선은 비효율적이므로 메시지를 패킷으로 잘라서 사용
- How: 어디로 전송할지 불명확하므로 패킷에 목적지 주소 명시
- Store & Forward: 패킷이 모두 전송됐어도 링크가 다른 메시지 사용 중이면 Store 상태로 기다린 후 Forward(큐잉 딜레이의 원인)
- 단점: 꽉 찬 상태인데도 패킷이 계속 들어오면 Packet loss 발생
	- Congestion: 큐잉 딜레이에, 로스까지 발생

### 1.3.2 Circuit Switching
- 전화 네트워크
- 과정: 유저 call --> call set up(경로 설정) --> 자원 예약
- 파이프라인 연결
- 네트워크 분할해 사용처에 나눠줌
	- FDM(Frequency division)
		- 가용 횟수를 대역으로 나눠서 사용자별로 할당
		- 사용자는 할당받는 네트워크 지속 사용 가능
	- TDM: 시간별로 할당

- 리소스 쉐어(예)
	- circuit-switching: 맥시멈 10명
	- packet-switching
		- 35명이 쓰더라도 10명이 동시에 active가 될 확률은 0.0004%
		- 그러나 자원을 예약해 놓지 않으므로 Congestion이 발생할 수 있음
		- 패킷을 circuit과 같이 큐잉 딜레이 없이 전송할 방법은 없을까? Chapter 7에 나와 있음 그러나 진도에 없으므로 찾아봐야 함

### 1.3.3 Network of networks
- Global ISP의 라우터들을 액세스 네트워크로 연결
- ISP끼리 Peering link로 연결
- IXP(Internet Exchange Point): Peering link 없는 ISP끼리 연결
- ISP 아래 Regional ISP, 그 아래 또 위계구조 
- Content Provider Network
	- 서비스 회사사 만든 Own network
	- 구글, MS 등이 전 세계 데이터 센터를 연결

## 1.4 지연, 손실, 처리율 in 패킷 교환 네트워크
1.delay, 2.loss, 3.throughput 

### 딜레이
1) nodal processing
기본적 네트워크 프로세싱에 걸리는 시간
- 에러체크
- 아웃고잉 링크 결정
	- 링크가 당장 이용 가능하지 않으므로 버퍼 큐에 넣는다.
	- 버퍼에서 기다리는 시간이 큐잉 딜레이

2) 큐잉 딜레이
Why: 아웃고잉 링크를 뽑아내는 속도보다 트래픽(데이터)이 빠르게 쌓이므로
노드의 congestion(혼잡) 정도에 따라 딜레이 시간이 달라짐
R: link bandwidth (bps)
L: packet length (bits)
A: Average packet arrival rate
    
단위시간당 트래픽 유입량: L * A
Traffic Intensity 트래픽의 정도: L * A / R
   
L*A와 R이 같다? 
- 유입되는 속도와 뽑아내는 속도가 같다.

Traffic Intensity > 1?
- 유입되는 양이 뽑아내는 속도보다 더 많다.
- 큐의 길이가 무한정 늘어난다.

Traffic Intensity이 0에 가깝다?
- 트래픽이 유입되는 속도보다 나가는 속도가 더 빠르다.
    
0.7만 되어도 딜레이가 급증 1이면 무한정 높아지는데 이유는?  
- Average packet arrival rate로 평균이 1이지만 더 빠른 패킷이 오면 큐에 계속 쌓이므로

3) 트랜스미션 딜레이
큐에서 기다려서 드디어 내 차례가 됐지만 여기서 패킷을 밀어 넣는 데 걸리는 시간
패킷의 크기와 링크의 대역폭에 의해 결정
L: packet length(bits)
R: link bandwidth(bps)
Transmission delay: L / R
패킷이 길수록 길어지고 대역폭이 클수록 짧아진다.

4) 전파 딜레이
비트가 노드에 실린 다음 전파까지 되어야 전달완료
d: 링크의 길이
s: 전기신호가 전파되는 속도
Propagation delay: d / s
일반적으로 2 * 10^8 m/sec

4개의 과정 중 큐잉 딜레이만 가변적이다.

### Packet loss

아웃고잉 링크가 바로 이용가능하지 않을 떄마다 큐가 생기는데 큐의 전체 길이는 한정돼 있다.
이 큐가 꽉 차면 패킷이 드랍돼 낭비 발생하는 것이 패킷 로스

### Throughput

단위 시간당 처리량(Source부터 Destination까지 배달한 트래픽 양)
제일 대역폭이 작은 링크에 의해 Throughput 결정 --> Bottleneck link 

## 1.5 프로토콜 계층과 서비스 모델
프로토콜은 레이어링 구조, 매우 방대하고 복잡한 시스템
레이어링으로 모듈화하면 나타나는 현상
- 관계 정의, 유지나 업데이트 용이
- 각 레이어 고유 목적이 독립적이므로 충돌이 날 수도 있다.
- 레이어끼리 커뮤니케이션할 때 오버헤드가 발생할 수 도 있다. 

### 1.5.1 계층구조
1) 애플리케이션 프로토콜
유저의 메시지 캡슐화
예) SMTP 이메일, HTTP 

2) 트랜스포트
애플리케이션에서 만들어낸 메시지를 프로세스 투 프로세스로 딜리버리하는 계층
호스트 투 호스트 딜리버리를 네트워크 계층에 부탁
예) TCP, UDP

3) 네트워크
라우팅을 통해 길찾기, 길찾을 때 단위(홉 바이 홉?)마다 이를 링크 계층에 부탁
예) IP, routing protocols

4) 링크
한 홉을 건너 가기 위해 피지컬 미디엄에 비트를 밀어넣어야 한다.
데이터를 실어 나르는 일을 피키컬 미디엄에 부탁
예) 이더넷, 와이파이

5) 피지컷
비트 on the wire

### 1.5.2 캡슐화
라우터는 3계층, 스위치는 2계층인 이유
- 애플리케이션은 엔드시스템에서만 실행되므로
- 트랜스포트는 엔드 투 엔드 프로세스 딜리버리 역할이므로 중간에 필요없다.
- 네트워크는 다음 홉 길찾기 역할이므로 라우터에도 필요.
	- 스위치는 제너럴한 길찾기 기능이 없다.

PDU
애플리케이션:메시지
트랜스포트:세그먼트
네트워크:데이터그램
링크:프레임 

## 네트워크 보안

1) 악성코드: malware
virus: 실행해야만 activate, 전파
worm: 받기만 하면 저절로 구동, 전파  
Spyware malware: 사이트 방문, 키스트록 정보를 컬렉션 사이트에 올린다.  
botnet: 디도스 어택을 위한 오염된 컴퓨터들의 집합 

2) Dos(Denial of Service) 공격: 서버에 쓰레기 트래픽을 계속 보낸다.  
1 타겟을 결정  
2 타겟 주변의 컴퓨터들을 botnet으로 만들다  
3 botnet이 쓸모 없는 트래픽을 타겟으로 보낸다.

3) packet Sniffing
쉐어드 이더넷, 와이파이에서 발생
각 NIC 카드(피지컬)에서 프레임을 받으면 프레임에 적힌 주소를 보고 NIC에 적힌 주소와 일치하면 윗 계층으로 올려보내고 아니면 드롭시킨다.
간혹 NIC에 promiscuous로 세팅해놓으면 그냥 위로 올려 보내?므로 Malware 침투

4) IP spoofing
Host가 다른 Source인 척

# 목차
컴퓨터 네트워크와 인터넷

- 1.1 인터넷이란 무엇인가?
	- 1.1.1 구성요소로 본 인터넷
	- 1.1.2 서비스 측면에서 본 인터넷
	- 1.1.3 프로토콜이란 무엇인가?
- 1.2 네트워크의 가장자리
	- 1.2.1 접속 네트워크
	- 1.2.2 물리 매체
- 1.3 네트워크 코어
	- 1.3.1 패킷 교환
	- 1.3.2 회선 교환
	- 1.3.3 네트워크의 네트워크
- 1.4 패킷 교환 네트워크에서의 지연, 손실과 처리율
	- 1.4.1 패킷 교환 네트워크에서의 지연 개요
	- 1.4.2 큐잉 지연과 패킷 손실
	- 1.4.3 종단간 지연
	- 1.4.4 컴퓨터 네트워크에서의 처리율
- 1.5 프로토콜 계층과 서비스 모델
	- 1.5.1 계층구조
	- 1.5.2 캡슐화
- 1.6 공격받는 네트워크
- 1.7 컴퓨터 네트워킹과 인터넷의 역사
	- 1.7.1 패킷 교환 개발: 1961~1972
	- 1.7.2 독점 네트워크와 인터네트워킹: 1972~1980
	- 1.7.3 네트워크 확산: 1980~1990
	- 1.7.4 인터넷 급증: 1990년대
	- 17.5 새 천 년

## 1.1 인터넷이란 무엇인가?

### 1.1.1 인터넷의 구성요소

1. End System(호스트)
애플리케이션을 실행하고 있으므로 호스트라고 부른다.
가장자리에 있으므로 End System이라고 부른다.

2. 라우터(스위치)
사용자들의 메시지를 목적지로 보내는 역할

3. 링크(물리적인 회선)
라우터와 호스트, 스위치와 라우터끼리 연결

### 1.1.2 서비스 측면에서 본 인터넷

- 인터넷은 네트워크들이 모인 네트워크. 라우터들이 플랫하게 놓여 있는 게 아니라 뭉텅이로 모여 있다. 
- 그래서 표준화가 중요
	- IETF(Internet Engineering Task Force)에서 RFC(Request for comments, 표준)을 정한다.
	- 표준 이름이 Request for comments? RFC를 대학원생들이 만들었기 때문에 Request for comments로 이름붙여
	- chronological하게 연대기순으로 1번부터 번호가 다 붙어 있다. 

### 1.1.3 프로토콜 무엇?

보내고 받는 메시지의 포맷, 순서, 액션을 정한다.
 
## 1.2 네트워크의 가장자리 Access networks and physical media

- 엔드 시스템과 라우터를 어떻게 연결?
	- 액세스 네트워크를 통해 엔드시스템에 접근할 수 있다.
	- 액세스 네트워크는 통신사, 케이블회사, ISP 등이 제공한다.
- 액세스 네트워크에서 중요한 척도
	- 대역폭(bits per second)
	- Shared 여부(혼자 or 나눠쓰는가) 중요

### 1.2.1 Access net
 
- 1.digital subscriber line(DSL)
	- 전화회사가 전화선을 통해 액세스 네트워크를 제공
	- 특징
		- Dedicated line(단독)
	- 과정
		- DSL 모뎀 ---> Splitter --> 전화회사의 센트럴 오피스 --> DSLAM(DSL Access multiplexer): 보이스면 전화로 데이터면, 인터넷으로 나눠줌 
	- Upstream이 Downstream보다 적은 이유: 인터넷 사용할 때 다운로드를 하는 비중이 더 크므로

- 2. 케이블 네트워크
	- 케이블회사가 모뎀을 통해 액세스 네트워크를 제공
	- 특징
		- Shared Link(나눠쓴다, 예: 한 아파트에서 공유하니까)
		- HFC: hybrid fiber coax: 아파트 공유하는 곳에서는 Coax cable을 쓰고 Cable headend에서는 fiber를 사용해서 하이브리드
		- 30Mbps Downstream이라 전화선보다 빠르다? No No 쉐어하므로 더 느릴 수 있다.

- 3. 홈 네트워크
	- 하나의 라우터와 와이파이 액세스 포인트

- 4. 기업 액세스 네트워크(이더넷) Enterprise access networks(Ethernet)
	- 라우터에 여러 이더넷 스위치가 연결되고 거기에 또 여러 엔드 포인트들이 물려 있다.
	- 라우터와 ISP(Internet)를 직접 연결(Dedicated line)
		- 케이블회사나 전화회사가 아니라 ISP가 직접 깔아줌

- 5. wireless
	- 와이파이나 셀룰러(LTE) 모두 Shared

- 호스트
	- 역할: 애플리케이션 메시지를 패킷으로 잘라서 액세스 네트워크로 내보낸다
	- 패킷 크기: L bit
	- 액세스네트워크 연결 속도 transmission rate: R bit
	- 패킷 하나를 호스트에서 내보내는 데 걸리는 시간: L bit / R bit 초

### 1.2.2 물리매체 Link

- Physical media
	- 1. guided media: 물리적 와이어 사용(예: copper: 이더넷, fiber, coax: HFC)
	- 2. unguided media: 물리적 와이어 사용 X(예: 라디오(예:와이파이, 셀룰러))
	
- 1) Twisted pair(TP): copper의 다른 이름
	- 이더넷을 케이블링하는 데 주로 사용되는 링크
	- 1. Category 5: 100Mbps, 1Gpbs Ethernet
	- 2. Category 6: 10Gbps
	
- 2) Coax, fiber
	- Coaxial cable
		- two concentric copper conductors
		- bidirectional
		- Broadband 링크: 대역폭이 넓은 링크
			- HFC 케이블링에 사용
			- Multiple channels on cable
	- fiber optic cable
		- glass fiber carrying light pulses, each pulse a bit
			- electronic이 아니라 light pulses
				- 주변 노이즈에 별로 영향 X
				- 시그널이 약해지더라도 먼 거리를 이동할수 있다
		- transmission rate가 매우 높다.
			- 수십 Gbps
		- 에러율이 낮다
		
- 3) Radio
	- propagation environment effects
		- 장애물에 의해 reflection
		- 라디오끼리 interference
	- terrestrial microwave
		- 45Mbps
	- LAN(WiFi--> 와이파이가 Wireless LAN이라는 뜻)
		- 802. 11b --> 1Mbps
		- 802. 11g --> 54Mbps
		- 300미터 내외
	- wide-area(Cellular)
		- LTE 10Mbps 이상
		- 수십 킬로미터 내외
	- satellite(위성)
		- 270 m/sec end-end delay
		- 위성까지 왔다갔다 머니깐 딜레이 
		- geostationary satellite 땅에서 근접하니 속도가 더 빠름
		- low-earth orbiting(LEO) satellite

## 1.3 네트워크 코어 Network core
라우터와 스위치들이 연결된 코어
메시지 전달이 목적 

### 1.3.1 Packet Switching

- Circuit은 전화 네트워크에서는 유용했지만 데이터 네트워크 등장
- 전화와 인터넷의 차이
	- 전화는 지속적으로 왔다갔다
	- 인터넷은 요청과 응답이므로 간헐적으로 왔다갔다
- Circuit은 전화할 때 다른 전화를 못 하는 것처럼 할당된 분량을 다른 사용자들이 사용할 수 없다는 단점
- 1. 예약이 없다. no call set up, no 리소스 예약
- 2. each packet transmitted at **full link capacity**
	- 예약이 없고 하나밖에 없다면 전체를 다 쓸 수 있다. 
- 3. store & forward
	- 하나의 메시지가 독점으로 오래 사용하면 비효율 발생. 그래서 메시지를 패킷이라는 청크로 잘라서 사용
	- 패킷으로 나눠쓰니 고정적으로 사용 가능
	- 파이프라인으로 즉각 연결되는 전화와 달리 어디로 전송되어야 할 지 불명확하므로 패킷에 목적지 주소가 명시돼 있다	
	- 라우터 입장에서는 잘린 패킷이 모두 올 때까지는 기다려야 함(store) --> 모두 받은 이후 forward
	- 패킷을 모두 받았어도 링크를 다른 메시지가 사용한다면 Store 상태로 기다린 후에 forward해야 할 수도 있음(queueing delay)
		- 어느정도 네트워크가 필요할 줄 모르므로 자원을 예약해놓지 않는다. 그래서 어떤 경우에는 패킷 쌓이므로 queueing delay
		- 꽉 찬 상태인데도 패킷이 계속 들어오면? Packet loss 발생!
		- queuing delay, loss까지 발생 --> Congestion	

### 1.3.2 Circuit Switching
- 전화 네트워크에서 사용
- 유저가 call --> call set up (경로 설정) --> 자원 예약
- 둘 사이를 파이프로 연결한 것처럼 메시지를 실어나를 수 있다.
- 네트워크를 분할해서 사용처에 나눠주는 형태

#### 네트워크 분할방법 Circuit Switching: FDM versus TDM

- FDM: Frequency division 
	- 가용한 횟수를 몇 개의 대역으로 나눠서 사용자별로 할당
	- 사용자는 할당받는 네트워크 계속 사용 가능
- TDM: Time
	- 시간을 할당

#### 패킷 스위칭과 써킷 스위칭의 리소스 쉐어

- 예: 1Mb/s link
- each user: 
	- 100 kb/s "active"
	- active 10% of time
	- 유저 10명이 동시에 사용하면 100k * 10 -> 1Mbps

- circuit-switching --> 맥시멈 10명
- packet-switching
	- **35명이 쓰더라도 10명이 동시에 active가 될 확률은 0.0004%**
	- 그러나 자원을 예약해 놓지 않으므로 Congestion이 발생할 수 있음
	- 패킷을 circuit과 같이 큐잉 딜레이 없이 전송할 방법은 없을까? Chapter 7에 나와 있음 그러나 진도에 없으므로 찾아봐야 함

### 1.3.3 Network of networks

- 너도 나도 다 직접 연결 O(N^2) --> 비효율
- global ISP사업자의 라우터들이 액세스 네트워크를 연결
- 모든 액세스 네트워크들이 Global ISP 라우터에 연결된다면 효율 증가? 그러나 경쟁 등의 이유에 의해 ISP 수가 여러 개
- ISP들끼리 peering link로 연결해서 하나만 연결되어도 타고타고 간다.
- peering link가 없더라도 ISP끼리 연결해주는 사업자도 등장 --> IXP(Internet exchange point)
- 그러나 전 세계 작은 지역까지 ISP가 서비스할 수는 없으므로 Regional ISP 등장(이 Regional ISP도 위계구조를 가지고 또 낳고 낳고..) Multi-tire hierarchy!
- ISP의 연결점--> POP(point-of-presence)
	- to/from backbone --> 프로바이더와 연결
	- to/from customers 
	- peering --> 같은 레벨의 다른 라우터들을 연결
	- 내부끼리 연결
- settlement-free
	- regional이 다른 regional과 연결하기 위해 상위 ISP를 모두 거쳤다가 다시 내려와서 연결하는 비용을 제거하기 위해 peering으로 직접 연결하고 비용 프리
- Multi-home
	- 한ISP 사업자가 여러 ISP를 제공하여 장애 방지 가능?
- Content provider network
	- 서비스 회사들이 만든 Own network
	- 구글, MS 등이 전 세계 데이터센터를 직접 연결 혹은 액세스네트워크도 직접 연결?
	- 예: 구글
		- 30~50 데이터 센터
		- Private network로 연결하여 upper-tier ISP 비용 절감
 
## 1.4 패킷 교환 네트워크에서의 지연, 손실과 처리율

- 네트워크의 주요 메트릭
	- 1.delay, 2.loss, 3.throughput

### 1. 딜레이

1. nodal processing
- 기본적인 네트워크 프로세싱에 걸리는 시간
	- 에러체크
	- 아웃고잉 링크 결정
		- 링크가 당장 이용 가능하지 않으므로 버퍼 큐에 넣는다.
		- 버퍼에서 기다리는 시간이 큐잉 딜레이

2. 큐잉 딜레이
- 노드의 congestion(혼잡) 정도에 따라 딜레이 시간이 달라진다.

3. 트랜스미션 딜레이
- 노드에 도착 --> 프로세싱 --> 아웃고잉 링크 결정 --> 큐에서 기다려서 드디어 내 차례
- 여기서 패킷을 밀어 넣는 데 걸리는 시간
- 패킷의 크기와 링크의 대역폭에 의해 결정
- L: packet length(bits)
- R: link bandwidth(bps)
- Transmission delay: L / R
	- 패킷이 길수록 길어지고 대역폭이 클수록 짧아진다.

4. 전파 딜레이
- 비트가 노드에 실린 다음 Propagation까지 되어야 전달이 완료된다.
- d: 링크의 길이
- s: 전기신호가 전파되는 속도
- Propagation delay: d/s
- 일반적으로 2 * 10^8 m/sec

4개의 과정 중 큐잉 딜레이만 가변적이다.

큐잉딜레이 
- 왜 발생? 아웃고잉 링크를 뽑아내는 속도보다 트래픽(데이터와 같은 의미)이 빠르게 쌓이므로 
- R: link bandwidth (bps)
- L: packet length (bits) 
- A: Average packet arrival rate

- 단위시간당 트래픽 유입량: L * A
- Traffic Intensity 트래픽의 정도: L * A / R

- L*A와 R이 같다
	- 유입되는 속도와 뽑아내는 속도가 같다.
- Traffic Intensity > 1
	- 유입되는 양이 뽑아내는 속도보다 더 많다.
	- 큐의 길이가 무한정 늘어난다.
- Traffic Intensity이 0에 가깝다.
	- 트래픽이 유입되는 속도보다 나가는 속도가 더 빠르다. 

0.7만 되어도 딜레이가 급증 1이면 무한정 높아지는데 이유는?
Average packet arrival rate로 평균이 1이지만 더 빠른 패킷이 오면 큐에 계속 쌓이므로

### 2.Packet loss

- 아웃고잉 링크마다 바로 이용 가능하지 않을 때마다 큐가 생기는데 이 큐의 길이는 한정돼 있다.
- 큐가 꽉차면 패킷이 드랍되어 낭비 발생 --> loss

### 3.Throughput

- 단위 시간당 처리량(Source부터 Destination까지 배달한 트래픽의 양)
- Throughput은 Rs에 의해 결정
- Rs --> Rc로 패킷이 하나씩 이어지는 파이프라인으로 볼 수 있는데 Rc보다 더 작은 Rs
- 제일 대역폭이 작은 링크에 의해 Throughput이 결정 --> Bottleneck link

## 1.5 프로토콜 계층과 서비스 모델

프로토콜은 레이어링 구조, 매우 방대하고 복잡한 시스템
- 레이어링으로 모듈화하면
	- 관계를 정의하거나 유지, 업데이트에 용이
	- 각 레이어의 고유 목적이 독립적이기 때문에 충돌이 날 수도 있다.
	- 레이어끼리 커뮤니케이션할 때 오버헤드가 발생할 수 있다.

### 1.5.1 계층구조

Source Host to Destination Host 딜리버리가 목적
인터넷 프로토콜 5계층

1. 애플리케이션 프로토콜 
- 유저의 메시지를 캡슐화
- 예: SMTP 이메일, HTTP
2. 트랜스포트
- 애플리케이션에서 만들어 낸 메시지를 프로세스 투 프로세스로 딜리버리하는 계층
- 호스트 투 호스트 딜리버리를 네트워크 계층에 부탁
- TCP, UDP
3. 네트워크
- 라우팅을 통해서 길찾기 
- 길찾을 떄 단위 마다(홉 바이 홉?) 찾는데 이를 링크 계층에 부탁
- IP, routing protocols
4. 링크
- 한 홉을 건너 가기 위해 피지컬 미디엄에 비트를 밀어넣어야 한다.
- 데이터를 실어 나르는 일을 피지컬 미디엄에 부탁
- 이더넷, 와이파이
5. 피지컬
- 비트 on the wire

### 1.5.2 캡슐화

라우터는 3계층, 스위치는 2계층
- 애플리케이션은 엔드시스템(호스트)에서만 실행되므로
- 트랜스포트는 엔드 투 엔드 프로세스 딜리버리가 역할이므로 중간에 들어갈 일이 없다.
- 네트워크의 다음 홉 길찾기 역할이므로 라우터에도 필요한 것이다.
	- 스위치는 제너럴한 길찾기 기능이 없다.

각 프로토콜 계층에서 자기 기능 수행을 위해 헤더를 붙인다.
나의 피어에게 전달하기 위해 헤더를 붙이는 것

PDU 
- 애플리케이션: 메시지
- 트랜스포트: 세그먼트
- 네트워크: 데이터그램 
- 링크: 프레임 

## 1.6 Network security

처음에는 과학자들끼리 네트워크
컴퓨터 네트워크 공격의 종류
어떻게 방어?

악성코드: malware
- 1.virus: 실행해야만 activate, 전파
- 2.worm: 받기만 하면 저절로 구동, 전파
Spyware malware: 사이트 방문, 키스트록 정보를 컬렉션 사이트에 올린다.
botnet: 디도스 어택을 위한 오염된 컴퓨터들의 집합

Dos(Denial of Service) 공격: 서버에 쓰레기 트래픽을 계속 보낸다.
1 타겟을 결정
2 타겟 주변의 컴퓨터들을 botnet으로 만들다
3 botnet이 쓸모 없는 트래픽을 타겟으로 보낸다.

packet Sniffing
- 쉐어드 이더넷, 와이파이에서 발생
- 각 NIC 카드(피지컬)에서 프레임을 받으면 프레임에 적힌 주소를 보고 NIC에 적힌 주소와 일치하면 윗 계층으로 올려보내고 아니면 드롭시킨다.
- 간혹 NIC에 promiscuous로 세팅해놓으면 그냥 위로 올려 보내?므로 Malware 침투

IP spoofing
- Host가 다른 Source인 척

## history

10년 단위로 큰 변화

### 1.7.1 패킷 교환 개발: 1961~1972

1961 큐잉 이론 -  UCLA 클레이락
1964 패킷 스위칭 - 바란
1969 최초 인터넷 ARPAnet
1972 최초 프로토콜 NCP, 최초 애플리케이션 e-mail. 등장 

### 1.7.2 독점 네트워크와 인터네트워킹: 1972~1980
다양한 네트워크 등장
이를 연결하는 인터네트워크 등장 --> 현재 인터넷 아키텍처 

### 1.7.3 네트워크 확산: 1980~1990

다양한 프로토콜 등장
- 1983 TCP/IP(패킷 스위칭 네트워크를 위한 프로토콜) 등장 

### 1.7.4 인터넷 급증: 1990년대

1990 HTTP 등장
1990년말 킬러 애플리케이션 등장
보안 문제 대두

### 17.5 새 천 년

용어와 개념에 익숙해져야 한다.


## Reference

- [컴퓨터 네트워크 - 이화여대 이미정 교수님](http://kocw.net/home/search/kemView.do?kemId=1046412)
