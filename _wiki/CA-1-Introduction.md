---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:20:20 +0900
updated : 2022-02-13 00:20:20 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 요약

1. Instruction Set Architecture as an ADT
ADT(Abstract Data Type)을 정의하기 위한 두 가지
1) State(레지스터와 메모리)
ISA의 State는? 레지스터와 메모리의 내용

2) Operations(스테이트를 Transform)
ISA의 Operation? Instruction

예) 
Jump는 어떤 State을 바꾸나? 프로그램 카운터 스테이트 1개만 바꾼다
Store? 프로그램 카운터의 값, 메모리의 한 location의 값을 바꾼다.

Stack
- State: 데이터와 오더링
- Operation: pop, push.. 

Instruction Set Architecture
- State: 레지스터와 메모리의 내용
- Operation: Instruction

Load Instruction
- State: 타겟 레지스터의 값이 바뀌고 프로그램 카운터의 값이 바뀐다.
- Operation: 메모리 내용을 레지스터로 옮긴다.

Branch
- State: 프로그램 카운터 
- 조건 만족하면 어디로 브랜치할 것인지 타겟 주소 지정
- 조건이 맞지 않으면 시퀀셜한 인스트럭션 실행

2. Engineering methodology
Rule1: Common 케이스는 성능 최적화
Rule2: Rare 케이스는 성능보다 Correct

Rule1: Common 케이스는 성능 최적화
- 메인메모리: DRAM으로 구성(캐시보다 훨씬 큰 규모)
- 캐시: SRAM(가격비싸고 속도 빠름)으로 구성
- SRAM과 DRAM 사이의 Trade off: cost, performance
- 목표: 새로운 메모리 소자가 성능은 SRAM이며 가격은 DRAM인 것 같은 일루젼

Spatial locality(공간적 지역성): 어떤 주소가 참조되면 가까운 미래에 그 주소가 또 참조될 확률이 높다.
	- array의 index 시퀀셜
Temporal locality(시간적 지역성): 가까운 과거에 참조되면 가까운 미래에 또 참조될 확률이 높다.
	- for loop의 i 변수

캐시와 메인메모리의 Common Case
- Spatial locality: 요청된 워드로 움직이지 않고 그 주변에 있는 block 단위로 캐시에 올려 놓는다.
- Temporal locality: 여태까지 참조된 블록을 줄을 세운다. 그 다음에 캐시에 집어넣을 수 있는 곳까지만 끊어서 올린다.

보통의 DRAM 2GB인데 캐시를 2MB만 써도 99% 참조가 캐시에서 hit가 나오는데 그 이유가 위 Common Case를 잡아냈기 때문

Rule2: Rare 케이스는 성능보다 Correct
 I/O가 메인메모리(DRAM) 데이터 변경했는데 프로세서가 캐시에서 이전 데이터를 참조하는 잘못된 Rare 케이스
- I/O Input작업이 끝나면 프로세서는 캐시가 아니라 메인메모리를 참조하고 캐시에 있는 이전 데이터는 지우도록 하여 해결

3. Performance 측정 방법
- Time
	- 클라이언트측에서 본 퍼포먼스 메트릭(중국집 손님)
	- response time
	- execution time
- Rate
	- 서버측에서 본 퍼포먼스 메트릭(중국집 주인)
	- throughput: MIPS, MFLOPS
	- bandwidth: Mbps
- Ratio
	- relative performance(both time and rate)
 
# Introduction
1. Interfaces
− Instruction Set Architecture (“The Hardware/Software Interface”)

2. Engineering methodology/ Correctness criteria/ Evaluation methods/ Technology trends involved in the following design techniques
	- Pipelining
	-  Cache
	-  Multiprocessor  
		- Cache Coherence  
		- Synchronization  
		- Interconnection Network
		
## Instruction Set Architecture as an ADT
- ISA? 인터페이스다. 목적은 분업
- **레지스터와 메모리로 정의된 스테이트, 그 스테이트를 Transform시킬 수 있는 명령어로 구성**
- ADT(Abstract Data Type)	
	- 무엇이 정의되어야 하나? **1 State**와 State를 변경하는 **2 Operation**
	- ISA의 State는 무엇? **메모리와 레지스터의 내용** Operation은 무엇? **Instruction**

- 예:
	- Jump는 어떤 State을 바꾸나? 프로그램 카운터 스테이트 1개만 바꾼다
	- Store? 프로그램 카운터의 값, 메모리의 한 location의 값을 바꾼다.
	- State는 레지스터의 집합 + 메모리로 정의, Operation은 Instruction으로 정의

## Design Techniques

Engineering methodology
Correctness criteria  
Evaluation methods  
Technology trends
 
Pipelined execution : 한 명령어 실행 이후 끝마치기 전에 다음 명령어 실행
out-of-order execution: 뒤죽박죽 실행
Speculative Execution(추측에 근거한 실행): 브랜치를 만나면 결과를 보지 않고 예측을 해서 양쪽 중에 한 방향으로 가능 가버리는 것?

## Engineering methodology

- Rule1: Common 케이스는 성능 최적화
- Rule2: Rare 케이스는 성능보다 Correct 

#### Rule1: Common 케이스는 성능 최적화
메인메모리: DRAM으로 구성(캐시보다 훨씬 큰 규모)
캐시: SRAM(가격비싸고 속도 빠름)으로 구성
SRAM과 DRAM 사이의 Trade off: cost, performance
목표: 새로운 메모리 소자가 성능은 SRAM이며 가격은 DRAM인 것 같은 일루젼 

- **Spatial locality(공간적 지역성): 어떤 주소가 참조되면 가까운 미래에 그 주소가 또 참조될 확률이 높다.**
- **Temporal locality(시간적 지역성): 가까운 과거에 참조되면 가까운 미래에 또 참조될 확률이 높다.**
	- for loop의 i 변수

캐시와 메인메모리의 Common Case
Spatial locality: 요청된 워드로 움직이지 않고 그 주변에 있는 block 단위로 캐시에 올려 놓는다? Why? Spatial locality를 잘 이용하기 위해서
Temporal locality: 여태까지 참조된 블록을 줄을 세운다. 그 다음에 캐시에 집어넣을 수 있는 곳까지만 끊어서 올린다? 

보통의 DRAM 2GB인데 캐시를 2MB만 써도 99% 참조가 캐시에서 hit가 나오는데 그 이유가 위 Common Case를 잡아냈기 때문

#### Rule2: Rare 케이스는 성능보다 Correct
CPU 캐시메모리에 정확성에 문제를 일으킬만한 Rare 케이스는 무엇?
- I/O가 메인메모리(DRAM) 데이터 변경했는데 프로세서가 캐시에서 이전 데이터를 참조하는 잘못된 Rare 케이스
- I/O Input작업이 끝나면 프로세서는 캐시가 아니라 메인메모리를 참조하고 캐시에 있는 이전 데이터는 지우도록 하여 해결 

캐시 메모리 구현 Correctness 기준
- 캐시 메모리가 없는 시스템에서 Instruction 실행한 것과 똑같은 결과	
- 파이프라인 실행: 시퀀셜 실행과 똑같은 결과

### Performance 측정 방법

엔지니어링 95%는 정말 쉽다. 마지막 5%가 정말 어렵다. 이게 5~10배 더 힘들다.
정확성이 담보되었다면 성능을 최적화

- Performance types
- 1클라이언트가 중요하게 생각하는 메트릭
- 2서버가 중요하게 생각하는 메트릭

- Time
	- 클라이언트측에서 본 퍼포먼스 메트릭(중국집 손님)
	- response time
	- execution time
- Rate
	- 서버측에서 본 퍼포먼스 메트릭(중국집 주인)
	- throughput: MIPS, MFLOPS
	- bandwidth: Mbps
- Ratio
	- relative performance(both time and rate)

Load Instruction
- State: 타겟 레지스터의 값이 바뀌고 프로그램 카운터의 값이 바뀐다.
- Operation: 메모리 내용을 레지스터로 옮긴다.

Branch
- State: 프로그램 카운터 
- 조건 만족하면 어디로 브랜치할 것인지 타겟 주소 지정
- 조건이 맞지 않으면 시퀀셜한 인스트럭션 실행

## Technology Trends
 
1 MIPS(1초에 백만개 인스트럭션 실행)
64KB : 메모리
1M : 백만불 
가격 / MIPS: MIPS당 가격

익스포낸셜한 가격과 성능 상승
그러나 그 흐름이 더뎌진다면?

신뢰성을 높이는게 큰 챌린지
- 소프트웨어는 가끔 죽는데 비행기나 자동차가 그런가?

## Big Picture

데이터센터--> 하나 크기가 축구장 정도
왜 강 옆에 데이터센터가 있는가?
강 옆에는 수력발전해서 전기료 절감 / 물로 쿨링

블레이드 서버(CPU보드 낱장) 고장나면?
그냥 빼오고 다른 스탠바이 유닛을 넣는다. 하나라도 다운타임이 생기면 안 되니깐

SDK, 하드디스크 프로세서 몇 개? 1~2개
자동차 프로세서 몇 개? 300개 이상
동시에 부팅시키는 것만 해도 대단할 것이다.

BMW 브레이크 밟으면 양쪽이 속도를 맞추기 위해 계속해서 한쪽을 조정한다.
BMW 차 터는 법?
천정에 올라가 밟으면 된다.
전복됐을 때 대비

평소에는 파워 스티어링
Fault Function--> 이빨 꽉 깨물고 하면 핸들, 브레이크 동작한다.
 
실리콘 얇게 썰어 --> Black Wafer
트랜지스터

Infant Mortality --> 완성 되기 몇 
태어난 후 이틀이 가장 위험하다.

 ## Processor Performance Trends

익스포낸셜한 성능 향상
전자측 기여
- 한 다이 안에 들어가는 트랜지스터의 크기를 더 작게 
	- 더 빠르게 스위칭 가능
- Scalable CMOS
	- 사이즈가 작아지더라도 다시 사용 가능하도록 애초에 심볼릭하게 디자인

성장의 방향
프로세서: 성능
DRAM: 용량 capacity

무슨 문제가 발생할까?
스피드 갭 점점 커져
싱글 레벨 캐시로 막아봤다.
3단계 이상의 캐시까지

컴퓨터 아키텍처
클락 프리퀀시가 집적도를 따라가지 못하고 멀티코어로 선회 
멀티코어로 프로그래밍을 하는 인간의 시퀀셜한 특징의 머리가 못 따라가서 발전속도 느림

lithography 트랜지스터 계속 작게
계속 작게 한계가 있으므로 10년 내 신뢰성 문제 대두될 것이다.

더 큰 Mega-Trend
- 창조주가 우리를 만드신 방법을 배우는 것
-  한 번 프로그래밍한 뒤에 자체적 evolve하도록 내버려둔 것
- 프로그래밍을 한 번 해두고 냅두는 게 가장 좋지 않을까
- 생물학에 키워드가 있을지도 모른다. 생물을 꼭 들으라.
- 지금 눈 앞 트렌드가 아니라 10년 100년 1000년 뒤 트렌드에 도전과제를 삼으라. 

## Source

- [서울대학교 민상렬 교수님 2011 Computer Architecture](https://olc.kr/course/course_online_view.jsp?id=240)
