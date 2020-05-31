---
layout  : wiki
title   : term
summary : 
date    : 2020-02-19 16:01:29 +0900
updated : 2020-05-31 14:44:53 +0900
tags    : 
toc     : true
public  : true
parent  : diary 
latex   : false
---
* TOC
{:toc}

> 개인이 보는 용도의 낙서장입니다;

## 운영체제

### CPU 시장 점유

- IBM 대형컴퓨터, PC
    - IBM의 CPU -> Intel
    - IBM의 OS -> MicroSoft
        - MS-DOS(Disk operating system) -> DOS의 원래 주인은 CPM
        - 83's -> IBM이 MS 밀어줘서 CPM은 사라짐
        - 90년대 CISCO > MS > Intel > IBM
            - Intel 2년 반 동안 주식 3번 스플릿 -> 8배 성장
        - 2000년
- Intel - IA64로 PC 시장 석권, 서버 시장까지 진출
    - HP 백기 투항
    - IA64가 들어가는 Itanium, Itanium2 CPU 설계 엔지니어 600명(50명은 HP)
- 실리콘 그래픽스 - MIPS - RISC 프로세서
    - 쥬라기공원만드는 데 워크스테이션 제공했지만 인텔에게 밀림
    - 임베디드 프로세서로 전환
- 썬 - SPARC -> 서버용 CPU 강자였지만 인텔에게 밀림
    - 오라클에 흡수
- IBM - PowerPC -> 10년 전 애플에 들어감
    - IBM + 모토롤라 + 애플 -> PowerPC
    - 인텔에게 밀려서 나머지는 손털고 IBM만 씀
- 애플 ARM의 ISA 라이선스만 가져와서 나머지 자체 설계
- 삼성 ARM의 설계를 가져와서 제조만 자체 (로얄티 애플보다 많이 주겠지)

+ 버전 업
    - 세대가 발전 -> 내부 설계가 변한 것
    - 공정기술이 발전 -> 실리콘 와이어의  람다(미니멈 피처사이즈)가 줄어드는

+ 128b processor -> 플레이스테이션3

### OS Overview

#### 운영체제

- 애플리케이션 프로그램의 실행을 제어하고 애플리케이션과 하드웨어 사이 인터페이스를 제공하는 편의 서비스
- 하드웨어와 애플리케이션 사이의 소프트웨어 레이어

1 쉬운 사용
2 자원 쉐어
3 보호

- 디지털 시스템은 모두 컴퓨터? 노
- 스테이트 - 메모리
- 명령어 - 머신의 스테이트를 바꾸는 것
- 포트 - 메모리에 연결된 인풋 아웃풋

<img width="845" alt="1" src="https://user-images.githubusercontent.com/48748376/81625350-9870e580-9433-11ea-8fa4-05cdfe3c7f53.png">

#### Key Interfaces

- API - 라이브러리와 애플리케이션 프로그램을 나누는 경계 
- ABI Application binary interface - 라이브러리와 OS를 나누는 경계
- ISA 머신 인스트럭션 셋 아키텍쳐 - 하드웨어 소프트웨어 경계

#### Terminology

##### Microprocessor : 한 개의 칩 프로세서 (예: Intel i7, Pentium IV, AMD Aholon, SUN Ultrasparc, ARM, MIPS, ...)

##### ISA (Instruction Set Architecture)
    - 레지스터나 메모리와 같은 머신의 스테이트를 비저블하게 만들고 머신의 명령어를 정의한다.
    - Examples
        - X86 - 인텔의 32비트 ISA
        - 모바일은 Arm의 SoC(System on Chip) -(cpu gpu 등 통합)의 ISA

##### Microarchitectur
    - ISA에 따라 구현한 하드웨어
    - 파이프라이닝, 캐시, 브랜치, 프레딕션, 버퍼

##### CISC(Complex Instruction Set Computer)
    - 80년대 중반까지 아키텍처 (예: x86)
    - 큰 사이즈의 ISA
    - 많은 명령어 포맷들, 각기 다른 사이즈의 명령어들 

##### RISC(Reduced Instruction Set Computer)
    - 80년대 이래 만들어진 대부분의 아키텍쳐(예: MIPS, ARM, PowerPC, Alpha, ...)
    - 로드-스토어 구조
        - 오직 레지스터로 컴퓨테이션
    - 작은 사이즈의 ISA
    - 각 명령어 심플, 고정된 사이즈

##### Word
    - Defaul data size for computation
    - GPR & ALU 데이터 사이즈
        - GPR(General Purpose Registers), ALU(Arithmetic and Logic Unit)
    - 워드 사이즈는 프로세서(8b, 16b, 32b, or 64b)에 의해 결정

##### Address (or pointer)
    - 메모리의 위치를 가리킨다
    - 각 주소는 바이트를 가리킨다(byte addressable)
    - 32b 주소라면 2의 32승 바이트 = 4GB
    - 256MB 메모리라면, 적어도 28비트 어드레스가 필요하다(2의 28승은 256MB)

##### Caches
    - Faster but smaller memory close to processor
        - 빠르고 작은 SRAMs, 그러나 비싸다

##### Interrupt
    - 프로세서의 시퀀싱(특정 명령어들의 순서)을 정지하게 만드는 I/O 장치들의 메커니즘
    - 프로세서보다 느린 I/O 장치들의 프로세서 이용을 개선하기 위한 방법
    - 외부 이벤트나 잘못된 상태(Exceptions)에 의해 절차의 제어가 강제된다(Handler).
    - 외부 인터럽트는 외부 이벤트나 비동기에 의해 발생

#### OS Basic

##### Process

- 실행 중 프로그램 하나의 인스턴스
- 프로세스의 3가지 컴포넌트
    - 실행 가능한 프로그램
    - The associated data
    - 실행 컨텍스트(프로세스 상태)
        - 프로세스 레지스터
        - 프로세스 우선순위와 같은 정보를 포함
        - OS가 프로세스를 제어할 수 있는 내부 데이터

#### Memory Management

##### Virtual Memory

- 피지컬 메인 메모리와 관계없이 논리적 관점에서 주어지는 프로그램 어드레스
- 여러 사용자의 잡이 동시에 메인 메모리를 사용해해 하는 요구사항을 충족 
- 실제 디램(피지컬 메모리)보다 용량이 훨씬 크다. 
- 예: CPU 내부 주소는 모두 가상 메모

##### Paging

- 고정 사이즈 블록들의 프로세스를 허용?
- 프로그램은 가상 주소를 통해 워드를 참조
- 가상 주소와 메인 메모리의 실제 주소 사이의 동적 매핑을 제공


### 운영체제 역사

Serial Processing(운영체제 없을 때) -> Simple Batch Systems(최초 운영체제) -> Multiprogrammed Batch Systems(멀티 태스킹) -> Time Sharing Systems

### Process

- 실행 중인 한 프로그램의 인스턴스
- 프로그램은 정적인 개념, 스테이트가 없다
- 프로세스 라이프 사이클?
    - 더블클릭하는 순간 각각의 프로세스 생성
    - 메모리에 로드되면 실행할 준비상태인 레디
    - Time Sharing에 의해 자신의 차례가 오면 실행 상태
    - IO를 요청하면 이벤트를 기다리는 블록 상태
    - 실행을 마치면 약간의 상태정보만 살아있는 좀비 상태
    - 부모 클래스에 의해 컬렉션되는 하비스트가 실행되면 완전히 사라진다
- 각 프로세스는 개별 버추얼 메모리, 독립적, CPU의 할당 경쟁
    - 자신이 혼자 CPU를 Exclusive하게 사용한다는 일루젼
        - 멀티태스킹으로 일정시간을 주고 돌아가는데 정지 후 다시 시작이 거의 연속적
    - 메모리도 혼자 사용한다는 일루젼
        - Private Adress Space -> Virtual Memory
            - 메인메모리(DRAM)의 물리적 한계를 벗어나서 얼마든지 큰 공간을 차지할 수 있다

#### Private Address Spaces

- 피지컬 어드레스 - DRAM의 주소
    - 각 주소는 바이트마다 
- 버우얼 어드레스 - 버추멀 메모리의 주소
    - CPU 내 프로그램 카운터, 명령어, 데이터의 주소 
- 하드웨어 Translation의 의해 Private Address를 DRAM의 주소로 변경


사진 

- Heap, Stack - 다이내믹 데이터
    - 프로그램 실행 중에 자기 공간이 생겼다가 없어졌다
    - 멜록하면 생성 프리하면 사라짐
- Shared Library - 다이내믹 라이브러리(.DLL 파일)
    - 예: printf는 모든 프로세스가 가져다 쓰도록 - 링킹
- Heap -> Static - 집이 항상 있다

#### Life and Scope of an Object

##### Life vs scope

- 메모리 공간을 차지하고 있으면 -> Live
- 살아있으나 스콥에 없으면 -> Live but not visible

##### 로컬 변수

- 포멀 parameter - 함수 내 선언, Actual parameter - 그 반대
- 블록 스콥
- 함수 실행 시 로컬 변수 메모리 할당 -> **스택 프레임**에 푸시, 함수 종료 시 팝
- Static(함수 내) - 스콥은 로컬이지만 스택이 아닌 스테틱에 저장되므로 함수가 사라져도 메모리를 차지하고 있다. 다시 불러올 때 메모리를 다시 할당하지 않아도 된다.
- Global - 스콥은 프로그램 전체, 메모리 고정 할당
    - Static(글로벌) - 스콥은 파일 내부, 특정 파일 내에서만 접근 가능해야 할 때 용이

#### Linux Run-time Memory Image

#### Context Switching

한 프로세스 내에서 사용자 프로세스와 커널 프로세스을 오감
Mode bit - 사용자 모드인지 커널 모드인지 확인
익셉션 - 사용자 모드에서 커널로 들어가는 유일한 경우
    - 인터럽트도 광의의 익셉션
    - 시스템 콜 - 트랩
- Context
    - 레지스터 내용 저장, 프로그램 카운터 저장 - 다음 스테이트 트랜스레이션할 때 여기서부터 시작
    - preemped -> 잠시 중지
    - 프로세스 컨트롤 블록의 레지스터를 보통 Context라고 부름
- Context Switching



#### Process Contrul Block



### Thread

### Mutual Exclusion and Synchronization

### Deadlock and Starvation

### Memory Management

### Virtual Memory

### Uniprocessor Scheduling

### Multiprocessor and Realtime Scheduling

### IO

### File Manamement

### Embedded OS

### Distributed OS

## 컴퓨터구조

### 1

- [인터리빙](http://www.ktword.co.kr/abbr_view.php?m_temp1=1061) - 페이딩 등 연집 에러(Burst Error)가 발생하기 쉬운 무선채널 환경 등에서 집중적인 비트 에러를 시간 또는 주파수 상에서 분산시키는 기술
    - 비트 오류 발생을 시간 또는 주파수 상에서 랜덤하게 분산시키는 기술

- [디미니싱 리턴 Diminishing Returns](https://en.wikipedia.org/wiki/Diminishing_returns) - 수확 체감의 법칙

- 프로그램 - 순서를 가진 기계어 명령의 시퀀스. 이를 순서대로 Fetch해서 디코딩하고 명령어가 시키는 작업을 한다. 그러면서 머신 스테이트를 업데이트하는 것.

- Memory
	* 메인메모리만 DRAM, 나머지는 모두 SRAM. 왜냐면 나머지가 모두 [NAND게이트](https://ko.wikipedia.org/wiki/NAND_%EA%B2%8C%EC%9D%B4%ED%8A%B8) 이기 때문.

 대역폭 - 일정 시간 내 얼마나 많은 일을 하느냐의 척도

- ILP(Instruction level parallelism) - 사이클당 처리되는 명령어수를 증가시키자. 즉, IPC를 높이자 그러나 한계에 부딪힘. 그래서 TLP 나옴. 
 
- TLP(Thread Level Parallelism) - 칩 안에 하나 코어 복잡하게 만들어봤자 열만 펄펄 난다. 그래서 적당한 코어를 여러 개 배치하는 방식으로 바꿈.

- 코어와 쓰레드 차이? -  코어는 칩에 물리적으로 존재하는 연산부를 의미하고, 스레드는 실제 OS에서 인식하고 작동하는 작업 단위

- CPU의 주소는 2개
	- 1. physical address - DRAM의 주소
	- 2. virtual address - 프로그램의 주소

- 32bit - 2^32bit -> 42억 byte -> 4GB
	- 1byte - 8bit(2^8개의 값 가능, 256)
	- 1kB - 1000byte(8000bit), 1KB - 1024byte
	- 1MB = 10^6byte
	- 1GB = 10^9byte
	 
- 컴퓨터 속도측정 -  한 프로그램 내 총 실행할 명령어 수(프로그램당 명령어) * 명령어당 사이클수(CPI) * 1 사이클 시간

### 2

- 프로그래밍 패러다임
	- 1 Impretive - how
	- 2 Procedure 
	- 3 Object-oriented
	- 4 Funcional - What

- 컴파일러 - Translation
- 인터프리터 - Translation과 동시에 바로 실행 예) 셸
- 어셈블리 - 컴파일러의 일부

- lecical -lex, syntax - yark, sementic

- 머신에 상관없이 인터미디어(중간) 코드 생성. 그 이유는 [디커플링](https://books.google.co.kr/books?id=RTJFDwAAQBAJ&pg=PA329&lpg=PA329&dq=%EC%BB%B4%EA%B3%B5+%EB%94%94%EC%BB%A4%ED%94%8C%EB%A7%81+%EB%9C%BB&source=bl&ots=DG0X3EmpQT&sig=ACfU3U36wC3fFZmodaSpB-96aLAa7cuAOg&hl=ko&sa=X&ved=2ahUKEwiCzrm--9_nAhWXHHAKHcmBATEQ6AEwBHoECAkQAQ#v=onepage&q=%EC%BB%B4%EA%B3%B5%20%EB%94%94%EC%BB%A4%ED%94%8C%EB%A7%81%20%EB%9C%BB&f=false) 때문이다. 밀접하게 연결된 두 개체를 떼어 내서 접점을 제거한다는 의미. X86이든 리스크든 어디서도 적용 가능한(?) 코드를 만들기 위해서인가? 아닌가?

- 메모리는 프로그램 입장에서 `Linear array byte`

- 주소의 크기가 프로그램 크기 결정(32비트 시대에 최대 프로그램은 4GB에 불과)

- 모든 바이트는 주소를 가진다 (2의 30승 = 1기가)

- 워드 - 연산의 기본 단위(32비트 머신은 32비트 워드)

- 워드와 레지스터 크기는 같다.

- alignment - 모든 데이터는 배수에 저장 (32비트 워드는 4의 배수에서 시작) int가 2비트인 모양

- Machine Instruction
	- 1. 계산 논리
	- 2. ALU 정수 레지스터
	- 3. Floating pointing unit 실수 레지스터

- jump - 명령 순서 변경 (브랜치)
- Operand - 데이터
- Input - Memory destination - 레지스터
- data transfer instruction - load, store

- IO 디바이스는 메모리 주소에 맵돼 있다. 이 주소를 포트라고 한다.

- Control Transfer Instruction - 명령어 순서를 바꿔준다. (브랜치라고도 표현)

- Program Counter - 읽어올 다음 명령어 주소를 가리킨다.

### 3

- 리스크는 프로그램 크기 커지면서 명령어 개수 적어졌지만 그만큼 코드 사이즈는 커졌다.

- 밉스 Instruction format
	- ALU
	- Data transfer instruction
	- branch instruction

- 32bit 명령어 안에 어떻게 인코딩?
	- 오퍼레이션 나타내는 필드를 오프코드?
	- 나머지 부분은 오퍼랜드를 Specify

- input, output이 되는 오퍼랜드는 모두 레지스터(인풋 2개, 아웃풋 1개)
- RS RT RD
- 소스 레지스터 RS RT 각각 소스1, 소스2
- RD Destination 레지스터
- RT 오퍼랜드 레지스터
- RRS RT 메모리 오퍼랜드

- Operation에 6비트 할당, 2의 6승인 64개의 Operation이 있군.

- Shift에는 상수를 인코딩. 이런 것을 이미디엇이라고 부름
- 레지스터는 달러로 표현, 가장 왼쪽이 데스티네이션

- 로드 - 메모리에 있는 것 레지스터로
- 스토어 - 레지스터에 있는 것 메모리로

- 베이스 어드레싱 - 레지스터 상수를 더해서
- RS 베이스 레지스터에 저장된 32비트 부소에 어드레스를 더하는데 이 어드레스를 오프셋이라고 한다. 이것이 디스플레이스먼트

### 4

- function 실행 시에만 메모리 할당, 리턴하면 메모리 반환
- 스택 - function마다 사용할 로컬변수 저장한 공간

- 메모리 안의 스택 - 시스템 스택

- 스택 포인터는 스택의 탑을 가리키고 있다.

- 스택 프레임 - function에 국한된 공간. function마다 자신의 스택 프레임을 갖는다. activation record라고도 부름

- 연속적으로 function을 부르면 스택의 프레임이 또 할당

- 리턴은 레지스터를 통해 알려준다.
- V0, V! 레지스터, 실제로는 $2, $3. 왜 2개? 부동소숫점인 경우 떄문
- 연속적으로 function 호출하면 리턴 어드레스 레지스터를 덮어쓰니 콜하기 전에 스택에 저장. 리턴할 때는 다시 복구. 세이브드 레지스터에.

- [Amdahl's law](https://parkcheolu.tistory.com/34](https://parkcheolu.tistory.com/34) - 속도를 계산할 때  병렬화될 수 있는 부분은 병렬가능 개수로 나눈 수치 만큼 향상이 가능하다.


## Link

- [최린 컴퓨터 구조](https://www.youtube.com/watch?v=Ewe9S1E969k&t=2s)
