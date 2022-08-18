---
layout  : wiki
title   : 
summary : 
date    : 2022-08-18 16:34:27 +0900
updated : 2022-08-18 16:34:58 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Deadlock Problem  
교착상태: 일련의 프로세스들이 서로가 가진 자원을 기다리며 Block된 상태

- 리소스(자원)
	- 하드웨어, 소프트웨어 등을 포함하는 개념
	- 예) I/O Device, CPU cycle, memory, space, semaphore 등
	- 프로세스가 자원을 사용하는 절차
		- 1) 요청, 2) 획득, 3) 사용, 4) 반납
- 예시
	- I/O Drive 시스템에 2개의 tape drive가 있다.
	- 프로세스 P1과 P2 각각 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

## Deadlock 발생의 4가지 조건 
- Mutual exclusion(상호배제)
	- 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
- No preemption(비선점)
	- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
- Hold and wait(보유대기)
	- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 내놓지 않고 계속 가지고 있음
	- 내가 가진 자원을 자발적으로 내놓지 않고 다른 자원을 기다림
- Circular wait(순환대기)
	- 자원을 기다리는 프로세스간에 사이클이 형성되어야 함
 
## Deadlock 처리 방법
- 1) Deadlock Prevention
	- 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
- 2) Deadlock Avoidance
	- 자원 요청에 대한 부가적인 정보를 이용해서 Deadlock의 가능성이 없는 경우에만 자원을 할당
	- 시스템 State가 원래 State로 돌아올 수 있는 경우에만 자원 할당
- 3) Deadlock Detection and recovery
	- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover
- 4) Deadlock Ignorance
	- Deadlock을 시스템이 책임지지 않음
	- UNIX를 포함한 대부분 OS가 채택
	- 데드락이 빈번하게 발생하지 않으므로 사용자가 알아서 프로세스 종료하는 방식
 
### 1) Deadlock Prevention
- Mutual exclusion(상호배제)
	- 공유해서는 안 되는 자원의 경우 반드시 성립해야 함
- Hold and wait(보유대기)
	- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
	- 방법 1: 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법
	- 방법 2: 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청
- No preemption(비선점)
	- 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
	- 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
	- State를 쉽게 Save하고 Restore할 수 있는 자원에서 주로 사용(CPU, Memory)
- Circular wait(순환대기)
	- 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
	- 예를 들어 순서가 3인 자원 Ri을 보유 중인 프로세스가 순서가 1인 자원 Rj를 할당받기 위해서는 우선 Ri를 Release해야 한다.
- 결과: Utilization 저하, Throughput 감소, Starvation 문제

### 2) Deadlock Avoidance 
- 평생에 최대 쓸 자원을 이미 알고 있다는 가정
- 자원 요청에 대한 부가정보를 이용해서 자원 할당 Deadlock으로부터 안전한지를 동적으로 조사해서 안정한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법
- Safe State
	- 시스템 내의 프로세스들에 대한 Safe Sequence가 존재하는 상태

#### 자원할당 그래프 알고리즘
- 자원당 인스턴스 하나
- 이용가능한 자원이 있다고 다 내놓는 게 아니라 데드락의 가능성(점선)이 있다면 자원을 주지 않음 

#### 뱅커스 알고리즘
- 자원당 인스턴스 여러 개
- 가용자원과 추가요청 가능 자원을 따져서 최악의 경우를 가정해서 최대 요청 자원에 이르지 않도록 함  

### 3) Deadlock Detection and recovery

미연에 방지하려는 1, 2번 방법이 아니라 발견되면 수행 

### 4) Deadlock Ignorance

데드락이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음
 
## Sources 
- [운영체제 이화여대 반효경 교수님](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
- [운영체제와 정보기술의 원리](http://www.yes24.com/Product/Goods/90124877)

