---
layout  : wiki
title   : 
summary : 
date    : 2022-08-18 16:33:40 +0900
updated : 2022-08-18 16:34:02 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

- Process Synchronization
- 같은 의미: 프로세스 동기화, Concurrency Control, 병행 제어

## OS에서 Race condition은 언제 발생하는가?
- 여러 주체가 하나의 데이터에 동시 접근할 때 발생하는 경쟁 상황(Race Condition)
- `count++`, `count--` 문장을 각 다른 프로세스에서 병행 실행하면 저수준에서는 임의의 순서로 뒤섞여 실행된다(그러나 각 고수준 문장 내에서 순서는 유지된다).

- 1) 커널 수행 중 인터럽트 발생 시
	- 커널이 커널 데이터 건드리는데 인터럽트 처리 루틴(커널 코드)도 커널 데이터를 건드리는 경우
- 2) 프로세스가 시스템 콜을 하여 커널 모드로 수행 중인데 컨텍스트 스위칭이 일어나는 경우	
- 3) 멀티프로세서에서 Shared Memory 내의 커널 데이터
	- User 레벨에서는 몰랐던 문제들이 커널에서는 동시 사용하는 데이터로 인해 문제 발생 여지가 생긴다
  
## 프로세스 Synchronization 문제
- 공유 데이터 동시 접근(concurrent access)은 데이터 불일치 문제(inconsistency)를 발생시킬 수 있다
- 일관성(consistency) 유지를 위해 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘 필요하다.

## Critical Section 문제 (임계구역)
- 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.
- critical section: 공유 데이터에 접근하는 코드
- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 critical section이 존재
- 진입 구역(entry section) : 각 프로세스가 자신의 임계구역으로 진입할 때 허가를 요청하는 코드
- 퇴출 구역(exit section)
- 나머지 구역(remainder section): 코드의 나머지 부분들을 총칭

```c
do{
	entry section   // lock을 걸어서 하나의 프로세스만 critical section에 들어가도록 함
	critical section
	exit section // lock 해제
	remainder section
} while(1)
```

### Critical Section 프로그램적 해결법

임계구역 문제에 대한 해결안은 다음의 세 가지 요구 조건을 충족해야 한다.

#### 1) Mutual Exclusion(상호 배제)

프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 critical section에 들어가면 안 된다.

#### 2) Progress (진행)

critical section이 빈 상태에서 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 한다.

#### 3) Bounded Waiting (유한 대기, 한정된 대기)

특정 프로세스가 지나치게 오래 기다리는 Starvation이 생기지 말아야 한다.

프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.

- 가정
	- 모든 프로세스의 수행 속도는 0보다 크다
	- 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

### 알고리즘 1

- Mutual Exclusion은 만족하지만 Progress는 만족하지 못함
- 반드시 교대로 들어가야 하므로 하나의 프로세스가 여러 번 쓰려면 둘 다 못 들어가는 상황 발생
```c
do{
	while(turn != 0){  // 내 차례가 되면
		critical section;  // 들어가고
		turn = 1;  // 다음 턴으로 바꿔준다.
		remainder section;
	}
}while(1);
```

### 알고리즘 2

- `flag[i] = true`에서 상대방에게 넘어가면 둘 다 들어가려는데 못 들어가는 상황 발생

```c
	do{
		flag[i] = true; // critical section에 들어가면 flag[i] = true로
		while(flag[j])}{ // 상대방이 critical section 들어가려는지 체크
			critical section
			flag[i] = false; // 상대방 들어가도록 나 다 썼다고 표시
			remainder section
		} 
	}while(1);
```

### 알고리즘 3(Peterson's Algorithm) 
- 아무 때나 CPU를 뺏겨도 잘 동작할 수 있는 알고리즘
- 그러나 Busy waiting(=spin lock: 락을 사용할 수 있을 때까지 프로세스가 회전)
	- 체크하면서 CPU와 메모리 낭비
- 고급언어의 특성상 하나의 코드에 여러 가지 Instruction을 수행하는데 중간에 CPU를 뺏기는 상황을 가정하니 복잡해지는데 하드웨어에서 컨트롤하면 쉬워진다.

```c
do{
	flag[i] = true;  // i가 들어가겠다고 의사표시
	turn = j;  // 상대방 순서로 바꿈
	while (flag[j] && turn == j);  // 상대방이 들어가겠다 의사표시 and 상대방 차례면 기다림
	critical section
	flag[i] = false;
	remainder section
}while(1);
```

### Synchronization Hardware
- 데이터를 읽고 쓰는 것을 한 번에 하지 못해서 발생했던 문제
- 하드웨어 고유 Instruction: `Test_and_set(a)`을 사용
- a 데이터 읽고 True로 바꾸는 것을 Atomic하게 수행하면 앞의 문제는 간단히 해결

```c
Synchronization variable:
	boolean lock = false;

Process P1
do {
	while(Test_and_Set(lock));
	critical section
	lock = false;
	remainder section
}
```
## Mutex Locks

mutex 락은 임계구역 문제를 해결하기 위한 소프트웨어 도구로 임계구역에 들어가기 전에 반드시 락을 획득해야 하고 임계구역을 빠져나올 때 락을 반환해야 한다. mutex라는 용어는 mutual exclusion의 축약 형태다.  

```c
aquire() {
	while (!available)
		; // busy wait
	available = false;
}
```

```c
release() {
	available = true;
}
```

```c
while(true) {
	acuire lock
		critical section
	release lock
		remainder section	
}
```

프로세스가 임계구역에 있는 동안 임계구역에 들어가기를 원하는 다른 프로세스들은 `aquire()` 함수를 호출하는 반복문을 계속 실행해야 한다(busy waiting).

mutex 락 유형을 스핀락(spinlock)이라고도 한다. 락을 사용할 수 있을 때까지 프로세스가 회저하기 때문이다.

최신 다중 코어 컴퓨팅 시스템에서 스핀락은 많은 운영체제에서 널리 사용된다.

## Semaphores 

추상자료형으로 실제 구현과 다르다.

세마포 S는 정수 변수로서 초기화를 제외하고는 단지 두 개의 표준 원자적 연산 `wait()`와 `signal()`로만 접근할 수 있다.

네덜란드 컴퓨터 과학자 다익스트라에 의해 고안된 세마포의 `wait()` 연산은 검사하다를 의미하는 네덜란드어 proberen에서 P, `signal()` 연산은 증가하다를 의미하는 verhogen에서 V를 의미한다.

- `wait()`
```c
wait(S) {
	while (S <= 0)
		; // busy wait
	S--;
}
```

- `signal()`
```c
signal(S) {
	S++;
}
```

각 `wait()`와 `signal()`은 연산 시 원자적으로 수행되며 인터럽트되지 않아야 한다.

### 세마포 사용법

운영체제는 이진 세마포와 카운팅 세마포를 구분한다. 이진 세마포는 mutex와 유사하게 0, 1 값만 가능하다.

카운팅 세마포는 가용한 자원의 개수로 초기화 된다. 0이 되면 모든 자원이 사용 중임을 나타낸다. 이후 자원을 사용하려는 프로세스는 세마포 값이 0보다 커질 때까지 봉쇄된다.

세마포는 동기화 문제를 해결하기 위해서도 사용할 수 있다. 

### 세마포 구현

세마포 역시 mutex와 같이 바쁜 대기를 해야 한다.  이를 극복하기 위해 `wait()` 연산을 실행한 프로세스가 세마포 값이 양수가 아니라면 자신을 일시 중지시킨다.

일시 중지 연산은 큐에 넣고 프로세스 상태를 대기 상태로 전환한다. 이후 제어는 CPU 스케줄러로 넘어가고 다른 프로세스가 선택된다.

다른 프로세스가 `signal()` 연산을 하면서 `wakeup()` 연산을 하면 대기 상태 프로세스가 준비 완료 상태로 변경된다. 그리고 프로세스는 준비 완료 큐에 넣어진다.

```c
type struct
{
	int value;  // semarphore
	struct process *list;  // 프로세스를 기다리는 큐
} semaphore;
```

각 세마포는 한 개의 정수 value와 프로세스 리스트 list를 가진다. `signal()` 연산은 프로세스 리스트에서 한 프로세스를 꺼내서 그 프로세스를 깨워준다.

```c
wait(semaphore *S) {
	S -> value--;
	if (S -> value < 0) {
		add this process to S -> list;
		sleep();
	}
}
```

```c
signal(semaphore *S) {
	S -> value++;
	if (S -> value <= 0) {
		remove a process P from S -> list;
		wakeup(P);
	}
}
```

세마포는 원자적으로 실행되어야 한다. 같은 세마포에 대해 두 프로세스가 동시에 `wait()`와 `signal()` 연산들을 실행할 수 없어야 한다.

이러한 임계구역 문제를 단일 처리기 환경에서는 인터럽트를 금지함으로써 간단히 해결할 수 있다.

그러나 다중 처리기 환경에서는 모든 코어의 인터럽트를 금지하는 작업은 매우 어렵고 성능을 심각하게 감소시킨다.

결국 바쁜 대기를 완전히 제거하지 못하고 진입 코드에서 임계구역으로 이동한 모습이다. 

## 모니터 Monitor
 - 모니터는 프로그래밍 언어 측면에서 동기화 문제를 해결하여 프로그래머 실수를 방지
- 모니터를 통해서만 공유 데이터 접근
	- 공유버퍼가 모니터 안에 정의돼 있으므로 락을 걸 필요가 없다.
- 생산자든 소비자든 하나의 프로세스만 활성화된다.
	- 생산자 입장에서는 비어있는 버퍼가 없다면 줄 서서 기다린다.
	- 생산 이후에는 잠들어 있는 소비할 게 없어서 잠들어 있는 소비자가 있다면 깨운다.
- `condition` : 조건 만족하지 않아서 오래 기다릴 때 사용하는 용도
	- `x.wait()`: x조건을 만족하지 않아서 잠 재운다.
	- `x.signal()`: 잠든 프로세스 하나를 깨운다.  

## Sources

- [운영체제 이화여대 반효경 교수님](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
- [운영체제와 정보기술의 원리](http://www.yes24.com/Product/Goods/90124877)
- [운영체제 제10판](http://www.yes24.com/Product/Goods/89496122)

