---
layout  : wiki
title   : 
summary : 
date    : 2022-02-12 00:43:32 +0900
updated : 2022-03-09 11:42:09 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 컴퓨터 시스템 구조
 
- CPU
    - 동작과정
        - 매 클럭마다 Memory에서 Instruction을 하나씩 읽어서 실행
        - Interrupt line의 값 체크. 값이 없으면 다음 메모리 명령어 실행
    - registers: CPU에 붙어 있는 메모리보다 빠른 저장공간
        - 프로그램 카운터 레지스터: 카운터가 가리키는 메모리 주소의 Instruction을 읽어와서 실행하고 다음 Instruction을 순차적으로 실행
    - mode bit: 현 실행 프로그램이 운영체제인지 사용자 프로그램인지 구분
        - mode bit 0: 운영체제가 CPU 실행 중이므로 모두 접근 가능
        - mode bit 1: 사용자 프로그램이 CPU 실행 중이므로 제한된 Instruction만 실행 가능
    - Interrupt line: CPU는 메모리에서만 명령어를 읽어오는데 I/O device에서 작업요청을 보낼 때 사용
        - 예: 디스크에서 데이터를 읽을 때는 CPU가 디스크 컨트롤러에게 일을 시켜놓고 자기는 할 일을 한다(디스크가 백만 배 느림)
    - Timer
        - 목적: 한 프로그램이 CPU를 독점(예: 무한루프)을 방지
        - 운영체제가 사용자 프로그램에 CPU사용권을 줄 때 Timer에 값(보통 수십 ms)을 세팅하고 넘겨준다.
        - 시간이 지나면 Timer가 CPU에게 Interrupt를 걸고 사용자 프로그램에서 운영체제로 다시 CPU 제어권이 넘어간다.
    - DMA Controller
        - 직접 메모리를 액세스할 수 있는 컨트롤러
        - 원래는 메모리 접근할 수 있는 장치는 CPU뿐이었는데 DMA Controller를 두면 DMA Controller도 메모리 접근 가능하다.
        - 잦은 I/O의 Interrupt를 방지하기 위해 CPU 대신 I/O device의 데이터를 메모리로 복사 등의 작업을 수행

- Memory
    - CPU의 작업공간
    - 사용자 프로그램은 I/O 작업을 직접할 수 없다. 보안 등의 이유로 OS만 I/O가능하므로 운영체제에게 권한을 넘기고 운영체제는 CPU를 통해서 I/O 디바이스컨트롤러에게 작업 전달
    - Memory Controller
        - CPU와 DMA 접근의 교통제어 역할
- I/O device 
	- 종류 
		- Disk
			- 보조기억장치이면서 I/O device
			- Input: 하드의 데이터를 읽어서 메모리의 Input으로 보낸다, output: 처리결과를 하드디스크에 저장
		- 키보드, 모니터.. 
	- Device Controller: I/O device들의 작은 CPU 역할, 모든 기기마다 하나씩 있음
		- 예: 디스크 헤드를 어떻게 돌릴지를 CPU가 아니라 디바이스마다 있는 디바이스 컨트롤러가 제어
	- local buffer: I/O device들의 작은 Memory 역할, 모든 기기마다 하나씩 있음


### Mode bit
 
### Timer
 
### Device Controller
 
- CPU가 I/O에 작업을 맡길 때
	- I/O device 제어 레지스터(device controller)가 제어를 담당한다.
	- I/O device 로컬 버퍼에 데이터를 담는다.
	- device driver
		- 각 디바이스의 인터페이스에 맞게 접근 가능하게 해주는 소프트웨어 
		- 실제 I/O device를 움직이는 코드는 아님
		- CPU가 직접 일하는 게 아니라 메모리의 Instruction을 받아서 일하는 것처럼 디바이스를 실행하는 메뉴얼은 펌웨어에 있다?
	
### I/O 수행
 
- I/O는 운영체제를 통해서만 접근 가능
- 사용자프로그램이 운영체제에 부탁하는 것 --> System call
	- 운영체제에 해당하는 주소로 넘어가야 하는데 Mode bit이 1인 상태
	- 사용자 프로그램이 직접 인터럽트 라인을 세팅
	- Mode bit이 0으로 바뀌고 운영체제가 I/O Device 컨트롤러에게 요청
- Trap(소프트웨어 인터럽트) : 사용자프로그램
	- Exception: 프로그램이 오류를 범한 경우
	- System call: 프로그램이 커널 함수를 호출하는경우

### Interrupt

- 현대의 운영체제는 인터럽트에 의해 구동됨
	- 인터럽트가 들어올 때만 CPU가 운영체제에 넘어감
	- 그렇지 않으면 보통 사용자 프로그램이 CPU를 쓴다.

- 인터럽트 관련 용어
	- 인터럽트 벡터
		- 인터럽트 종류마다 어떤 함수를 실행할지 주소를 정의해놓은 일종의 테이블
		- 해당 인터럽트 처리 루틴 주소를 가지고 있음
	- 인터럽트 처리 루틴
		- 실제 인터럽트를 처리하는 부분 


### 동기식 입출력과 비동기식 입출력

Synchronous I/O
- 동기식으로 번역이 불충분(예: 립싱크의 싱크)
- 상황에 따라 의미가 다르게 쓰인다.
- 작업이 끝나고 다음 작업 실행
ASynchronous I/O
- 인터럽트로 작업 완료를 알림

### DMA

### 서로 다른 입출력 명령어

- 메모리 접근 명령어, I/O 접근 명령어 따로
- Memory Mapped I/O: 메모리 주소와 I/O 접근 주소 연속적으로
  
### 저장장치 계층 구조
 
- Primary(Executable): CPU 직접 접근 가능(바이트 단위여야 한다)
- Secondary: CPU 직접 접근 불가능(바이트 단위가 아니라 섹터단위라서)
- 캐시 메모리 : CPU 1클락당 하나 처리하는데 DRAM 접근하려면 10~100클락까지 걸리므로 속도 완충

### 프로그램의 실행(메모리 로드)

- 실행파일을 실행하면 버추얼메모리에 올라가고 이후 메모리로 올라가 프로세스가 된다.
- 실행 시 독자적인 버추얼 메모리 주소 공간이 생긴다.
- 당장 필요한 부분만 버추얼 메모리에서 메인 메모리에 올린다. 그렇지 않은 메모리는 디스크(Swap area)로 내린다.
	- Swap area: 메인 메모리의 연장 공간으로써 하드디스크. 전원이 나가면 프로세스 종료되므로 의미 없어짐
- Address Translation: 로지컬 메모리 주소를 메모리 주소로 변환


### 커널 주소 공간의 내용
 
- PCB(Process Control Block): 프로그램이 돌아가면 이를 관리하기 위한 자료구조가 커널에 만들어지는데 이것이 PCB다.
- Stack: 커널 코드를 함수로 사용하므로 스택, 사용자 프로그램마다 스택을 따로 씀
- 커널함수, 라이브러리 함수, 사용자가 쓴 함수 모두 다른 영역
- 커널 함수의 호출: 시스템 콜

## Source

- [이화여대 반효경 교수님 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)
