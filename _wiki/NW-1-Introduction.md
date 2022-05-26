---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:10:25 +0900
updated : 2022-05-26 21:08:30 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 1.1 인터넷이란 무엇인가? 
### 1.1.1 인터넷의 구성요소

#### End System(호스트)
- 애플리케이션을 실행하고 있으므로 호스트라고 부른다.
- 가장자리에 있으므로 종단 시스템(End System)이라고 부른다.
	- 종단 시스템은 통신 링크(물리 매체)와 패킷 스위치의 네트워크로 연결된다. 
	- 패킷: 데이터를 세그먼트로 나누고 각 세그먼트에 헤더를 붙인 정보 패키지

#### 패킷 교환기(스위치)
- 입력 통신 링크의 하나로 도착하는 패킷을 받아서 출력 통신 링크의 하나로 그 패킷을 전달한다.
- 가장 널리 사용되는 두 가지: 라우터(네트워크 코어에서 사용), 링크 계층 스위치(액세스 네트워크에서 사용)
- 비유: 패킷은 트럭과 유사하고, 통신 링크는 고속도로와 유사하며, 패킷 교환기는 교차로와 유사하다.
- 사용자들의 메시지를 목적지로 보내는 역할

#### 프로토콜
- IP 프로토콜은 라우터와 종단 시스템 사이에서 송수신되는 패킷 포맷을 기술한다.

### 1.1.2 서비스 측면에서 본 인터넷

테스트
- 인터넷은 네트워크들이 모인 네트워크. 라우터들이 플랫하게 놓여 있는 게 아니라 뭉텅이로 모여 있다. 
- 그래서 표준화가 중요
	- IETF(Internet Engineering Task Force)에서 RFC(Request for comments, 표준)을 정한다.
	- 표준 이름이 Request for comments? RFC를 대학원생들이 만들었기 때문에 Request for comments로 이름붙여
	- chronological하게 연대기순으로 1번부터 번호가 다 붙어 있다. 

### 1.1.3 프로토콜 무엇?

보내고 받는 메시지의 포맷, 순서, 액션을 정한다.
 
## 1.2 네트워크의 가장자리 Access networks and physical media

![[NW_1overview_7.png]]

- 엔드 시스템과 라우터를 어떻게 연결?
	- 액세스 네트워크를 통해 엔드시스템에 접근할 수 있다.
	- 액세스 네트워크는 통신사, 케이블회사, ISP 등이 제공한다.
- 액세스 네트워크에서 중요한 척도
	- 대역폭(bits per second)
	- Shared 여부(혼자 or 나눠쓰는가) 중요

### 1.2.1 Access net

![[NW_1overview_8.png]]
 
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

![[NW_1overview_9.png]]

- 3. 홈 네트워크
	- 하나의 라우터와 와이파이 액세스 포인트

![[NW_1overview_10.png]]

- 4. 기업 액세스 네트워크(이더넷) Enterprise access networks(Ethernet)
	- 라우터에 여러 이더넷 스위치가 연결되고 거기에 또 여러 엔드 포인트들이 물려 있다.
	- 라우터와 ISP(Internet)를 직접 연결(Dedicated line)
		- 케이블회사나 전화회사가 아니라 ISP가 직접 깔아줌

![[NW_1overview_11.png]]

- 5. wireless
	- 와이파이나 셀룰러(LTE) 모두 Shared

![[NW_1overview_12.png]]

- 호스트
	- 역할: 애플리케이션 메시지를 패킷으로 잘라서 액세스 네트워크로 내보낸다
	- 패킷 크기: L bit
	- 액세스네트워크 연결 속도 transmission rate: R bit
	- 패킷 하나를 호스트에서 내보내는 데 걸리는 시간: L bit / R bit 초

![[NW_2_1.png]]

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
### 1.3.1 Packet Switching

패킷 스위치가 R bits/sec 속도로 L bits의 패킷을 송신한다면 그 패킷을 전송하는 데 걸리는 시간은 L/R 초다.

#### 저장 후 전달

패킷의 앞쪽이 라우터에 도착했다고 비트를 전송하지 않는다. 라우터는 저장 후 전달을 채택하고 있으므로 비트를 먼저 저장(buffer, store)한 후 라우터가 패킷의 모든 비트를 수신하면 이후 출력 링크로 패킷을 전송(forword)한다.

d종단간 지연 = N * (L / R)

#### 큐잉 지연과 패킷 손실


- Circuit이 전화 네트워크에서는 유용했지만 데이터 네트워크 등장
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
 
![[NW_2_1 1.png]]

![[NW_2_2.png]]

![[NW_2_3.png]]

![[NW_2_4.png]]

![[NW_2_5.png]]

![[NW_2_6.png]]


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

![[NW_3_1 1.png]]

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

![[NW_3_2 1.png]]

### 2.Packet loss

- 아웃고잉 링크마다 바로 이용 가능하지 않을 때마다 큐가 생기는데 이 큐의 길이는 한정돼 있다.
- 큐가 꽉차면 패킷이 드랍되어 낭비 발생 --> loss

### 3.Throughput

- 단위 시간당 처리량(Source부터 Destination까지 배달한 트래픽의 양)
- Throughput은 Rs에 의해 결정
- Rs --> Rc로 패킷이 하나씩 이어지는 파이프라인으로 볼 수 있는데 Rc보다 더 작은 Rs
- 제일 대역폭이 작은 링크에 의해 Throughput이 결정 --> Bottleneck link

![[NW_3_3 1.png]]


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

![[NW_3_4 1.png]]

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

![[NW_3_5 1.png]]


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

![[NW_3_6.png]]

### 1.7.2 독점 네트워크와 인터네트워킹: 1972~1980
다양한 네트워크 등장
이를 연결하는 인터네트워크 등장 --> 현재 인터넷 아키텍처 

![[NW_3_7.png]]

### 1.7.3 네트워크 확산: 1980~1990

다양한 프로토콜 등장
- 1983 TCP/IP(패킷 스위칭 네트워크를 위한 프로토콜) 등장 

![[NW_3_8.png]]

### 1.7.4 인터넷 급증: 1990년대

1990 HTTP 등장
1990년말 킬러 애플리케이션 등장
보안 문제 대두
![[NW_3_9.png]]
### 17.5 새 천 년

![[NW_3_10.png]]

용어와 개념에 익숙해져야 한다.


## Reference

- [컴퓨터 네트워크 - 이화여대 이미정 교수님](http://kocw.net/home/search/kemView.do?kemId=1046412)
- [컴퓨터 네트워킹 : 하향식 접근](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791185475318)

