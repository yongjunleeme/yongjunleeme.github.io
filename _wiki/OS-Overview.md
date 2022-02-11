---
layout  : wiki
title   : 
summary : 
date    : 2022-01-08 10:39:55 +0900
updated : 2022-01-08 10:42:47 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

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
- 여러 사용자의 잡이 동시에 메인 메모리를 사용해야 하는 요구사항을 충족 
- 실제 디램(피지컬 메모리)보다 용량이 훨씬 크다. 
- 예: CPU 내부 주소는 모두 가상 메모리

##### Paging

- 고정 사이즈 블록들의 프로세스를 허용?
- 프로그램은 가상 주소를 통해 워드를 참조
- 가상 주소와 메인 메모리 실제 주소 사이의 동적 매핑을 제공

#### 운영체제 역사

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
- 프로세스가 제공하는 두 가지 key abstractions
    - Logical control flow, Private address space
    - Private Address Space -> Virtual Memory
        - 버추얼 메모리 - OS는 각 프로세스에 Private space를 제공
        - 메인메모리(DRAM)의 물리적 한계를 벗어나서 얼마든지 큰 공간을 차지할 수 있다

#### Private Address Spaces

- 피지컬 어드레스 - DRAM의 주소
    - 각 주소는 바이트마다 
- 버추얼 어드레스 - 버추멀 메모리의 주소
    - CPU 내 프로그램 카운터, 명령어, 데이터의 주소 
- 하드웨어 Translation의 의해 Private Address를 DRAM의 주소로 변경
- Heap, Stack - 다이내믹 데이터
    - 프로그램 실행 중에 자기 공간이 생겼다가 없어졌다
    - 멜록하면 생성 프리하면 사라짐
- Shared Library - 다이내믹 라이브러리(.DLL 파일)
    - 예: printf는 모든 프로세스가 가져다 쓰도록 - 링킹
- Heap -> Static - 집이 항상 있다

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

- 한 프로세스 내에서 사용자 프로세스와 커널 프로세스을 오감
- Mode bit - 사용자 모드인지 커널 모드인지 확인
- 익셉션 - 사용자 모드에서 커널로 들어가는 유일한 경우
    - 인터럽트도 광의의 익셉션
- Context
    - 레지스터 내용 저장, 프로그램 카운터 저장 - 다음 스테이트 트랜스레이션할 때 여기서부터 시작
    - preemped -> 잠시 중지
    - 프로세스 컨트롤 블록의 레지스터를 보통 Context라고 부름
- Context Switching

#### Process Control Block

- 스테이트 - 메모리, 레지스터
    - 메모리 - 실제 메모리, 레지스터 - 가상 메모리
- 레지스터의 스테이트 -> Context
    - OS -> 레지스터의 스테이트를 커널에 저장
        - Process Control Block - 프로세스와 관련된 정보 관리
            - 프로세스의 ID, 실행상태, 프로세스의 우선순위 등의 스테이트 관리
- 용도 : 중단되기 전 마지막 명령부터 시작
    - 스케쥴링에 관련된 정보
    - 시그널: 프로세스간 통신의 메시지
- 프리빌리지 레벨 정보
    - 예: 커널 프로세스는 어떤 접근도 가능
- 메모리 정보
    - 세그먼트 : 버추얼 메모리(텍스트, 힙, 스택 등)의 논리적 조각
    - 세그먼트 사이 갭이 존재할 수 있으므로 링크드리스트로 관리
    - 페이지테이블: DRAM에는 어디로 저장 되어 있는지 페이징으로 구분
        - 버추얼보다 작은 실제 메모리와의 트랜스레이션을 위해
- 리소스 관리
    - 메모리 제외한 요소(예: print) 동시 사용 불가능
    - unlock, lock 상태 관리

#### Program Status Word(PSW)

- 특정 레지스터가 프로세스의 실행 스테이트 정보를 관리
    - 예: EEFALGS 레지스터(x86 프로세서) - CPU가 현재 실행하는 프로세스 관리하는 스페셜 레지스터

#### Queung Diagram

- Two-state process model : runnign, not running
    - 실제 1개만 수행 가능하므로 not runnging 프로세스들은 대기하면서 큐가 만들어져
- 디스패처 : 커널에서 스케쥴링 담당하는 코드

<img width="1129" alt="스크린샷 2020-06-03 오후 1 25 27" src="https://user-images.githubusercontent.com/48748376/83595734-ca421b80-a59d-11ea-80e4-fbb116ae1f3e.png">

#### Process Creation and Termination

- Process spawning
    - 프로세스 생성
        - 부모 프로세스가 포크를 통해 차일드 프로세스 생성
- Process termination
    - 의도된 종료(예:로그아웃)
    - 의도되지 않은 종료(예:자기에게 할당된 영역이 아닌 다른 영역 침범)
    - 메인 함수 마지막에는 자동으로 Exit

#### fork: Creating new processes

- UNIX -> ios, 리눅스 등 파생
    - ID, 차일드 프로세스 생성, 시그널 통해 메시지 전달 가능
- int fork
    - 차일드 프로스세의 pid 리턴
    - 생성된 순간에는 자기와 차일드가 같다(복사)
        - 실행하면 각각 같은 코드를 실행하지만 리턴값이 다르다
            - 누가 먼저 실행되는지는 그때그떄 다르다
        - call once return twice
        - 차일드 프로세스는 0을 리턴
        - 부모 프로세스는 차일드 프로세스의 pid를 리턴
- 포크 이후 또 포크 -> 하이라키 생성
- Process graph
    - 각 horizontal arrow - 프로세스 플로우
    - 각 vertical arrow - 포크 실행 후 새로운 분기

<img width="495" alt="스크린샷 2020-06-05 오후 1 46 36" src="https://user-images.githubusercontent.com/48748376/83838181-723d1d80-a733-11ea-94b6-dd7433fb27fd.png">


#### exit: Destroying Process

- atexit() - 나중에 exit을 실행할 때 실행할 함수를 미리 레지스터    
    - 특정 함수를 실행하고 바로 죽는다
    - `atexite(cleanup)`

#### Five-State Process Model

<img width="1158" alt="1" src="https://user-images.githubusercontent.com/48748376/84226305-6038f180-ab1c-11ea-82c3-2823a8da8250.png">

- Ready Queue - 메모리에 있으며 대기하는 프로세스들
- Block Queue - IO 리소스마다 다른 이벤트별 대기하는 프로세스들

#### Suspended Processes

- suspend 2가지 의미 혼용
    - interrupt 시 프로세스 중단 시 suspend, pause
    - 메모리 바깥으로 쫓아내는 -> swapping
- Swapping
    - 프로세스가 갑자기 많아졌는데 모든 프로세스가 IO 요청 -> 모든 프로세스 블록상태 -> CPU가 모두 블록상태라 할당할 수 없다 -> 강제로 블록상태를 일단 바깥에서 기다려
- Suspended Process
    - 메모리에서 쫓겨나서 Suspended State에
- page fault - CPU가 프로세스 액세스하려는데 디램(메인 메모리)에 없는 것
    - exceptino이 되어서 CPU가 아닌 운영체제가 디스크에서 메인메모리로 프로세스를 가져온 다음 page fault난 명령부터 다시 실행

<img width="1140" alt="스크린샷 2020-06-10 오후 1 37 00" src="https://user-images.githubusercontent.com/48748376/84231085-5321ff80-ab28-11ea-835e-9ea6c0938cd7.png">


#### Interrupt, Exception

- Exception
    - 현재 프로세스의 특정 명령에서 발생한 예외상황
    - Internel하다고 표현- CPU 내에서 발생하므로
    - OS 코드로 들어가는 모든 것 -> Exception의 Interrupt를 통해서만 커널로 들어간다
    - Sychronous - 순서대로 발생하므로
        - Traps
            - 명령어 실행 마치자마자 발생(예: 디버깅, 시스템콜)
            - 의도적인 Exception
            - Return to 'next' instruction
        - Faults
            - 명령어를 다 마치지 못한 것 (예: page fault)
            - 의도하지 않았지만 recoverable(명령어를 디스크에서 가져온 다음 번에는 Faults가 나올리 없기 때문)
            - Return to the faulting instruction
        - Aborts
            - 의도하지 않았고 회복도 불가능한 심각한 에러
            - 인텔에서는 Aborts를 MCA(Machine Check Aborts)라고 부른다
                - 예) 패러티 에러

- Interrupt
    - Exception과 달리 CPU 외부 이벤트(프로세스와 상관 없음)
        - 예) 키보드 누르면 Interrupt -> 그러나 CPU에겐 한 세월
        - 예) 리부트
    - 매 클럭사이클마다 CPU는 Interrupt 체크
    - Asychronous
        - 졸다가 언제 파워버튼 무릎으로 누를지 아무도 모른다
- 모든 이벤트 번호가 있다 -> Interrupt vector, exception vector
    - 특정 이벤트 정의한 핸들러 -> Interrupt handler, exception handler
        - 펌웨어 - OS와 하드웨어를 연결하는 브릿지 

- CPU 내 2개 핀(INT(인터럽트) 핀, NMI(Non Maskable Interrupt) 핀)
- Interrupt Classification
    - Maskable Interrupt - interrupt disable 가능(INT 핀에 의해)
    - Non-Maskable Interrupt - disable 불가능

#### UNIX system 5(V) Process Manangement

- 원래 UNIX는 AT&T에서 개발 -> System 5 계열
    - 다른 학교버전은 UC버클리에서 개발
- System Process
    - 유저프로세스에서 익셉션으로 시스템프로세스로 들어가는 게 이니라 원래부터 시스템 프로세스도 있다
        - 예) administrative 잡(메모리할당, 프로세스 swapping)
            - 유닉스에서는 디몬이라고 부름(커널 역할, 백그라운드)
- User Process
    - 커널과 유저 프로세스를 왔다갔다

- UNIX Process States

<img width="1045" alt="1" src="https://user-images.githubusercontent.com/48748376/84483132-dd5a9700-acd3-11ea-892a-f73ea1fd43ca.png">

<img width="1218" alt="2" src="https://user-images.githubusercontent.com/48748376/84483126-db90d380-acd3-11ea-9fc7-a5bbcfbd9fcf.png">


### Thread

- lightweigh process
- 하드웨어 스레드와는 다른 스레드
    - 요즘에 한 코어 내 프로그램카운터 2개 -> CPU 유틸라이제이션 높여
        - 멀티스레드 -> 여기서 말하는 스레드는 프로세스다
- OS의 스레드
    - 대부분의 소프트웨어 애플리케이션은 멀티스레드로 동작
        - 브라우저에서 탭이 늘어나는 것은 프로세스보다 스레드일 가능성이 크다
- 프로세스와 달리 스레드는 메모리와 자원을 모두 공유한다

#### 프로세스 특성

- 리소스를 갖고 있는 주체
    - 메모리(일부) 할당, 파일, I/O 디바이스 할당
        - 리소스오너십을 갖는다 - 뮤추얼 익스클루젼 - 독립적
        - 프로세스들은 리소스를 사용하기 위한 경쟁관계
    - 각 프로세스는 독립적인 버추얼 메모리를 갖는다
- 스케쥴 유닛
    - 프로세스단위로 스케쥴링의 우선순위
    - 실제 - 타임 슬라이싱에 의한 인터리빙, 프로세스 입장 - 연속적으로 실행하는 것처럼 보이고 concurrent(병행 컴퓨팅)한 것처럼 보인다
- 리소스는 프로세스별로 할당 스케쥴링(CPU)은 스레드별로 할당

#### 멀티스레딩

- 싱글 프로세스 내 여러 개의 councurrent paths의 실행을 지원하는 OS 기능
    - 프로세스 리소스 할당의 단위, 스레드는 스케쥴링의 단위
    - 스레드의 실행 스테이트는 제한적(Ready, Run, Blocked)
        - Blocked - 디스크로 쫓겨나는 suspend - 프로세스 단위로 쫓겨날 수 있다
    - 스레드 콘트롤 블록 - 스레드별 상태정보 관리
        - 스레드별 상태정보
        - 스레드별 레지스터 Context
        - 스레드별 스택 프레임

- 싱글스레드 - MS-DOX, UNIX
- 요즘은 다 멀티스레드 - Windows, Solaris, Modern Unix
    - 자바 버추얼머신
        - 자바 가상머신의 인스트럭션
        - 자바코드 컴파일한된 바이트코드 -> 네이티브 머신코드로 트랜스레이션되어야
        - 인터프리터 형태 - 트랜스레이션하면서 실행을 같이
        - 코드가 여러 개의 스레드로 실행된다

- 아무리 스레드가 많아도 하나의 프로세스는 하나의 버추얼 메모리
- 한 프로세스 모든 스레드는 같은 메모리를 공유한다
    - 프로세스끼리는 커널 중재 하에 시그널로 통신해야 하지만 스레드끼리는 그냥 메모릴 읽고 쓰면 되므로 오버헤드가 적다

<img width="1121" alt="1" src="https://user-images.githubusercontent.com/48748376/84727652-75a19600-afca-11ea-8993-45e75eeafcd1.png">

#### 멀티스레딩의 장점

- 스레드를 만드는 게 프로세스 생성보다 약 10배 빠르다 in UNIX
- 종료 시 프로세스처럼 좀비되어 부모가 하비스트하는 과정도 필요없다
- 스위칭도 빠르고 커뮤니케이션도 빨라진다
- 일부 액션은 스레드 전체에 영향을 미친다. 메모리, 자원을 공유하므로.
- 예: FTP
    - 스레드만 하나 생성(spawing)하면 된다.
    - 멀티코어 활용도 높아진다.
    - 코디네이션 수월, 파일 쉐어링 수월
- 예: 사용자 애플리케이션 입장
    - 여러 기능을 동시에 처리 가능
- Paraller Processing 
- 커널 레벨 아닌 유저 레벨에서 스레드 라이브러리를 통해 포크, 스케쥴링 등 가능. 훨씬 빠르다.

#### 스레드 상태

- 스레드 상태
    - Ready, Run, Blocked
    - Suspend는 해당하지 않는다 -> 디스크로 옮겨가므로 프로세스 레벨 스테이트
    - Spawn
        - 포크에 해당 -> 생성
        - 프로세스 생성되면 기본적으로 싱글스레드 만들어진다 ->  Spawn하면 같은 프로세스 안에 새로운 스레드 만들어진다 -> 스레드마다 자신만의 레지스터 콘텍스, 스택 공간을 가지게 된다
    - Block
        - IO 요청하면 다른 이벤트를 기다리는 상태(레지스터 세이브), 그러더라도 프로세스는 다른 프로세스나 스레드를 실행
    - Unblock
        - Ready
    - Finish

#### User-Level Threads

- 장점
    - 커널 모드가 필요 없이 스레드 스위칭(빠른 스위칭)
    - 유저레벨에서 지원하므로 OS 종류와 무관
- 단점
    - 한 프로세스 내 스레드간 통신은 가능하지만 시스템 콜로 블락되면 모두 블락
    - 멀티 프로세싱 장점 이용할 수 없다. 한 프로세스는 한 프로세서에만 할당하므로
- 커널레벨보다 가볍고 빠르지만 결국 OS 입장에서 스레드를 지원해야 여러 CPU에서 지원해 멀티프로세싱 등을 십분 활용 가능

#### Kernel-Level Threads

- OS 커널이 스레드 관리
- 스레드 4가지 장점 모두 살릴 수 있다
    - concurrency 
    - 멀티 프로세싱
    - 스레드간 커뮤니케이션
    - 가벼운 스위칭, 생성 
- 뭘 할 때마다 커널로 들어가니 오버헤드 크다
    - 유저레벨과 오버헤드 10배 차이(an order of magnitude)
        - two order of magnitude -> 100배, three -> 1000배

#### Combined Approach

- 스레드 생성은 유저 레벨
    - 필요한 경우 커널 레벨로 매핑

#### Performance of Multi Processing

- 무어의 법칙
    - 골든 무어 - 인텔 창업자 
    - IC칩에 집적할 수 있는 트랜지스터의 수가 1년반에 2배, 3년에 4배씩 증가
    - 9년 64배, 10년 100배, 20년 10000배
- 암달의 법칙
    - 애플리케이션 성능 - 시간, 트루풋 성능 - 초당 실행하는 명령어의 수
    - n개의 프로세서에서 프로그램 실행할 때 하나의 애플리케이션를 쪼개서 병렬로 만들어야
        - inherently serial한 부분이 스피드업의 걸림돌

#### Application for Mulicores

- 자바 버추얼 머신 
    - 싱글 프로세스인데 멀티 스레딩을 구현
- Solaris - 썬(서버 만드는)의 OS

<img width="1276" alt="스크린샷 2020-06-22 오전 10 35 07" src="https://user-images.githubusercontent.com/48748376/85240730-17662e80-b474-11ea-80b2-c8d6f3294dbc.png">

### Concurrency: Mutual Exclusion and Synchronization

#### Process Interaction

- 싱글 프로세서 시스템
    - 프로세스 실행은 CPU Utilization 증가를 위해 인터리빙된다.
        - Concurrent
    - 프로세스 실행 속도는 예측할 수 없다.
        - OS의 Interrupts 핸들이나 스케쥴링 정책에 의해 프로세서의 행동이 좌우되므로
    - The following difficulties arise
        - Mutual exclusion(상호배타적)
            - 자원 액세스할 때 상호배타적으로 보장해줘야 한다.
        - Deadlock
            - 각각 프로세스 가지고 있는데 각각 남의 자원을 기다리고 있는 상태
        - Starvation
            - 자원 스케쥴링하는데 한 프로세스만 소외시키는 상태
            - (Infinite Postponement) 스케쥴러에 의해 특정 자원의 할당이 무한정 딜레이 되는 것
        - Race condition
            - 프로세스 스레드가 어떤 순서로 액세스될지 알 수 없으므로 두 개의 프로세스나 스레드가 레이싱하는 상태 
                - 프로그래밍 버그에 해당
            - 글로벌 데이터를 쉐어하는 경우 의도하지 않은 결과가 나왔을 때
            - 뮤추얼 익스클루전한 크리티컬 섹션으로 묶어 놓아야 한다.
        - Livelock
            - 프로세스는 진행되는데 상태의 변화를 만들어내지 못하는 것
- [인터리빙](https://technote.kr/217)

#### 프로세스 경쟁

- Mutual Exclusion
    - 하나의 프린터에서 두 가지 프로세스를 수행한다는 가정
    - nonsharable resource
        - critical setion을 통해서 한 프로세스만 사용하도록 뮤츄얼 익스클루전을 보장
        - 애플리케이션 레벨에서 가능한 방법?
            - Interrupt를 critical 들어갈 때 disale, 나올 때 enable시키면 된다

#### Concurrency: Key terminologies

- 크리티컬 섹션을 구현하려면? 인터럽트는 싱글 코어에서는 가능하지만 멀티 코어에서는 제너럴한 솔루션이 될 수 없다.
- Atomic
    - indivisible -> 더이상 쪼갤 수 없는(지금은 쪼개지지만..;)
    - uninteruptable -> 중간에 방해할 수 없는
    - 성공 - 시스템 상태 변경, 실패 - 시스템 상태에 아무런 영향을 끼치지 못함
- 아토믹 오퍼레이션 - all or nothing, 하나 또는 많은 명령어를 구현하는 함수가 개별적이 되는 것. 실행이 보장거나 아예 실행되지 않는, Concurrent 프로세스에서 아토믹한 개런티가 고립을 보장해주는?
    - 하드웨어 레벨 atomic operations
        - 인스트럭션 셋 아키텍처로 지원
            - Test-and-set, fetch-and-add, compare-and-swap, load-link/store-conditional
            - 보통 2개의 명령어를 하나로 처리
        - 이게 없으면 여러 프로세스간 문제를 해결할 수 없다. 
        - 문제
            - Busy waiting - 0이 1이 될 때까지 계속 기다리는데 들어갈 수 있는지 계속 확인을 하니깐 자원 낭비
    - 소프트웨어 레벨
        - 크리티컬 섹션을 구현

- Special Instruction 장, 단점
    - 장점
        - 싱글 프로세서에서 여러 프로세스 또는 여러 프로세서가 메모리를 쉐어 가능
        - 심플하고 쉽게 verify
        - 여러 크리티컬 섹션을 서포트할 수 있고 각 크리티컬 섹션은 자신의 변수에 의해 정의될 수 있다.
    - 단점
        - Busy-waiting
        - 어떤 프로세스가 들어올지 모르므로 Starvation이 발생할 수도 있다
        - 데드락 발생할 수도

#### 세마포

- [세마포 작동원리, Busy-wation 나무위키](https://namu.wiki/w/%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4)
- 뮤추얼 익스클루젼 어떻게?
    - 싱글프로세서면 인터럽트되지만 멀티프로세서면 안 되고
    - 아토믹 오퍼레이션은 크리티컬 섹션이 가능하지만 위와 같은 문제가 있기에
    - 이상적인 개념이 없을까 해서 나온 것이 세마포
- 세마포
    - 리소스 액세스를 제어하기 위한 단순한 추상화를 제공하는 변수?
    - 자원의 개수로 이니셜라이즈됨
    - 오퍼레이션 2가지
        - P -> 요청 -> 변수를 디크리먼트(Wait operation - 2, 1, 0이 되면 0 다음에는 기다려야 하므로)
        - V -> 변수를 인크리먼트, 쓰고 돌려준다(Signal operation)
- 세마포 타입
    - Binary
        - 0(locked, unavaliable), 1(unlocked, avaliable)
    - Counting
        - n값 가능
- busy wating은 없고 queue가 있다
- Strong/Weak 세마포
    - String - 큐의 FIFO - 가장 오래된 것부터 웨이크업
    - Weak - 아무나 웨이크업 시키는
- 크리티컬 섹션에서는 시리얼라이즈되어야 한다

<img width="1256" alt="스크린샷 2020-07-08 오후 12 14 43" src="https://user-images.githubusercontent.com/48748376/86871160-b5b0f000-c114-11ea-85a8-d9c5b660a039.png">

<img width="1247" alt="스크린샷 2020-07-08 오후 12 14 33" src="https://user-images.githubusercontent.com/48748376/86871169-ba75a400-c114-11ea-9037-cae22dd4b417.png">

#### Producer / Consumer

- 상황
    - Producer - 데이터를 생성해서 버퍼에 삽입
    - Consumer - 버퍼에서 하나씩 끄집어내서 consume
    - 여러 개의 프로듀서가 생성하고 하나의 컨슈머가 소비
- 문제
    - 생산자는 꽉찬 버퍼에 더이상 삽입할 수 없다.
    - 소비자는 비어 있는 버퍼를 더이상 리무브할 수 없다.

- 컨슈머가 0이 되기 전에 프로듀서가 실행 되어서 컨슈머는 0인 줄알고 계속 기다리는데 컨슈머는 1이어서 계속 실행하므로 싱크로나이제셔인이 맞지 않게 된다.
- 보조 변수 m을 둬서 해결

<img width="709" alt="스크린샷 2020-07-09 오후 12 21 46" src="https://user-images.githubusercontent.com/48748376/86993567-494ef300-c1df-11ea-930d-5056e1263f51.png">

<img width="673" alt="스크린샷 2020-07-09 오후 12 21 39" src="https://user-images.githubusercontent.com/48748376/86993575-4d7b1080-c1df-11ea-9252-92092869f948.png">

- 세마포로 비지 웨이팅해결 가능하지만 프로그램 구현이 쉽지 않기 때문에 대안으로 나온 것이 Monitor, 메시지 패싱
- Monitor - 프로그래밍 언어 내에서 제공하는 컨스트럭처
    - 한 번에 하나의 프로세스만 모니터 안에서 실행 가능(뮤추얼 익스클루전 자동) 
    - 모니터에 정의된 프로시저에 의해서만 액세스 가능
    - 컨디션 변수 정의
        - C wait - 콜링 프로세스는 컨디션 c에서 대기
        - C Signal - 같은 컨디션에서 블록된 프로세스를 웨이크업시킨다


### Deadlock and Starvation

### Memory Management

### Virtual Memory

### Uniprocessor Scheduling

### Multiprocessor and Realtime Scheduling

### IO

### File Manamement

### Embedded OS

### Distributed OS


## Link 

- [최린 운영체제](https://www.youtube.com/playlist?list=PLOh92BQ5xeWnjt_S9zLOtndYzUfysSuzF)
