---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:07:55 +0900
updated : 2022-02-13 00:07:55 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# CPU Scheduling

## CPU and I/O bursts in Program Execution

어떤 프로그램이든 아래 두 과정을 반복하며 실행된다.
1) CPU Burst: CPU만 연속적으로 쓰는 단계
2) I/O Burst: I/O를 실행하는 단계 wait for I/O

CPU Bound job: CPU Burst 비중 높다, 계산 위주 job
I/O Bound job: I/O Burst 비중 높다
보통 사람이 쓰는 프로그램(Interactive job)은 중간 단계의 비율이 가장 높다. 
CPU 스케줄링으로 효율적인 job 배정해야는데 가능하면 사람이 기다리지 않도록 I/O bound job에게 CPU 스케줄링의 우선권을 준다.

## CPU Scheduler & Dispatcher

CPU 스케줄러
- 하드웨어? 소프트웨어? 프로그램?
	- OS 안에 스케줄링 코드가 있는 것
- Ready 상태의 프로세스 중 이번에 CPU를 줄 프로세스를 고른다. 

Dispatcher
- CPU 제어권을 CPU Scheduler에 의해 선택된 프로세스에게 넘긴다.
- 이 과정을  Context switch(문맥 교환)라고 한다.

CPU 반납 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
1) Running -> Blocked(예: I/O 요청하는 시스템 콜)
2) Running -> Ready(예: 할당시간 만료로 timer interrupt)
3) Blocked -> Ready(예: I/O 완료 후 인터럽트)
4) Terminate

1, 4번 스케줄링은 **비선점형 nonpreemptive(=강제로 빼앗지 않고 자진 반납)**
2, 3번은 **선점형 preemprive(=강제로 빼앗음)**

## Scheduling Criteria
CPU 스케줄링 성능척도

시스템입장 성능척도(중국집 사장 입장)
1) CPU utilization(이용률): 주방장 고용했는데 얼마나 일했는가
전체 시간 중에서 놀지 않고 CPU가 일한 시간
2) Throughput(처리량): 단위 시간당 손님을 얼마나 받았는가
주어진 시간 중에서 몇 개의 일을 처리했는지

프로세스입장 성능척도(고객 입장)
1) Turnaround time(소요시간, 반환시간): 주문하고 먹고 나가기까지 걸린 시간, 코스 요리 먹으면 기다리고 먹고 하는 시간
CPU를 쓰러 줄섰던 시간부터 다 사용하고 나간 시간까지
2) Waiting time(대기 시간): 밥 먹은 시간이 아니라 기다린 시간 요리 기다리고 짜장면 기다리고
CPU 쓰기 위해 줄서서 순수하게 기다렸던 시간
3) Response time(응답 시간): 첫 번째 음식이 나오기까지 기다린 시간
레디큐에 들어가서 처음으로 CPU를 얻기까지 기다렸던 시간
타임 쉐어링에서 중요한 척도

Waiting Time과 Response Time 차이
- Waiting Time: preemprive(선점형) 스케줄링은 CPU 썼다가 뺏겨서 줄서서 또 쓰다가 뺏길 수도 있는데 줄서서 기다린 시간만 합친 개념
- Response Time: 한 번 CPU 쓰기위한 줄을 서서 얻기까지 기다린 시간

## Secheduling Algorithms

### FCFS(First-Come First-Served)
먼저온 순서대로 처리(은행 번호표)
비선점형 nonpreemptive 스케줄링
CPU 오래 쓰는 잡이 처음 큐에 들어가는가 간발의 차이로 두 번째로 들어가는가만 따져도 큰 차이가 난다.
Convoy(호의) effect: 짧은 프로세스인데 긴 프로세스 뒤에서 오래 기다림
왜 호의? 1차세계대전 때 전쟁물자를 나르는데 뒤에서 기다리면 살 수 있는 가능성이 높아지므로.. 

### SJF(Shortest-Job-First)
CPU 짧게 쓰는 잡 먼저 처리하므로 기다리는 평균 시간이 가장 짧아짐
각 프로세스 다음번 CPU burst time을 가지고 스케줄링에 활용
CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
SJF is optimal: 주어진 프로세스들에 대해 minimum average waiting time을 보장
	- 선점형 preemprive 버전에 해당 

비선점형 nonpreemptive
- 일단 CPU를 잡으면 더 짧은 프로세스가 나타나도 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
- 프로세스 하나가 끝나면 스케줄링
선점형 preemprive
- 더 짧은 Burst time 프로세스가 도착하면 CPU를 빼앗김
- 이를 SRTF(Shortest-Remaining-Time-First)라고 부름
- 프로세스가 도착하면 언제든 스케줄링 

문제점
- Stravation 기아 현상: CPU 사용시간 긴 프로세스는 계속 기다려야 한다.
- CPU 시간을 매번 미리 가늠하기 어렵지만 예측은 가능

다음 CPU Burst Time 예측
- 다음번 CPU burst time 확정이 아닌 추정
- Exponential averaging
	- t: 실제 CPU burst 시간 
	- 타우: CPU 사용 예측 시간
	- 알파: 가중치

![[OS_5_1.png]]

### Priority Scheduling
우선순위 높은 순서대로 할당
우선순위가 높은 순서를 정수가 작은 순서대로 표현
- Preemptive
- nonpreemptive
SJF도 일종의 우선순위 스케줄링(우선순위=CPU burst time)
문제
- Starvation: 낮은 우선순위 프로세스는 실행을 못한다.
솔루션
- Aging(노화): 우선순위가 낮더라도 우선순위를 조금씩 높여준다.

### RR(Round Robin)
현대 컴퓨터의 기반: 할당할 때 시간도 할당하여서 뺏는 구조
각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐(일반적으로 10~100 milliseconds)
장점: 어떤 프로세스는 응답시간이 길어지지 않는다.
단점: CPU 시간 길고 프로세스만 있다면 기다리는 시간이 길어지는 단점이 생길 수 있다.

할당 시간이 지나면 프로세스는 선점(preempted) 당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다.
n개의 프로세스가 ready queue에 있고 할당 시간이 q time인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간을 1/n 얻는다.
	- 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
Performance
- q large -> FCFS(First-Come First-Served)
- q small -> context switch 오버헤드가 커진다

### Multilevel Queue
Ready queue를 여러 개로 분할
2개로 나눈다면
- foreground(interactive)
- background(batch - no human interaction)
각 큐는 독립적인 스케줄링 알고리즘을 가짐
- foreground - RR
- background - FCFS
큐에 대한 스케줄링이 필요
- 문제: Fixed priority scheduling
	- serve all from foreground then from background
	- starvation 가능성
- 해결: Time slice
	- 각 큐에 CPU time을 적절한 비율로 할당
	- 예) 80% RR 20% FCFS

### Multilevel Feedback Queue

프로세스가 다른 큐로 이동 가능
에이징(aging)을 이와 같은 방식으로 구현할 수 있다
Multilevel-feedback-queue scheduler를 정의하는 파라미터들
- Queue의 수
- 각 큐의 scheduling algorithm
- Process를 상위 큐로 보내는 기준
- Process를 하위 큐로 내쫓는 기준
- 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

## Multiple Processor Scheduling

Homogeneous processor인 경우
- queue에 한 줄로 세워서 각 프로세스가 알아서 꺼나가게 할 수 있다
- 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 문제가 복잡해짐
	- 예: 미용실에서 내가 원하는 미용사에게 헤어컷해야만 함
Load sharing
- 일부 프로세서에 잡이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
- 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
Symmetric(대칭의) Multiprocessing(SMP)
- (모든 CPU 대등하게) 각 프로세서가 각자 알아서 스케줄링 결정
Asymmetric(비대칭의) Multiprocessing
- 하나의 프로세서가 시스템 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

## Real-Time Scheduling

Hard real-time systems
- 정해진 시간 안에 반드시 끝내도록 스케줄링
Soft real-time computing
- Soft real-time task는 데드라인을 반드시 지키기보다 일반 프로세스에 비해 높은 Priority를 갖도록 해야 함

## Thread Scheduling

Local Scheduling
- User 레벨 스레드(OS는 스레드의 존재를 모르고 프로세스만 안다)의 경우 사용자 수준의 스레드 라이브러리에 의해 어떤 스레드를 스케줄할 지 결정

Global Scheduling
- 커널 레벨 스레드의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄할 지 결정


## Algorithm Evaluation

Queueing models
- 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 Performance index 값을 계산
- 이론적인 방법

Implementation(구현) & Measurement(성능 측정)
- 실제 시스템에 알고리즘을 구현하여 실제 직업(workload)에 대해서 성능을 측정 비료
- 리눅스 커널을 코드 바꿔서 측정하기는 쉽지 않을 수도 

Simulation(모의 실험)
- 알고리즘을 모의 프로그램을 작성 후 Trace를 입력으로 하여 결과 비교

## Source

- [이화여대 반효경 교수님 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)

