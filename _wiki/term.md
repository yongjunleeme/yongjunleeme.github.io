---
layout  : wiki
title   : term
summary : 
date    : 2020-02-19 16:01:29 +0900
updated : 2020-02-20 20:09:37 +0900
tags    : 
toc     : true
public  : true
parent  : study
latex   : false
---
* TOC
{:toc}

> 개인이 보는 용도의 낙서장입니다;

## 컴퓨터구조

- 인터리빙

- 디미니싱 리턴 - 수확첵감(하드웨어는 발전하는데 되레.. 무엇이 마이너스를 만들어내는 것이었는데 기억이..ㅜㅜ)

- 프로그램 - 순서를 가진 기계어 명령의 시퀀스. 이를 순서대로 Fetch해서 디코딩하고 명령어가 시키는 작업을 한다. 그러면 서 머신 스테이트를 업데이트하는 것.

Memory
* 메인메모리만 DRAM, 나머지는 모두 SRAM. 왜냐면 나머지가 모두 [NAND게이트](https://ko.wikipedia.org/wiki/NAND_%EA%B2%8C%EC%9D%B4%ED%8A%B8) 이기 때문.

- 명령어를 가져올 때 캐시를 쓰면 한 번에 여러 명령어를 가져오고 캐시는 재활용된다.

- 대역폭 - 일정 시간 내 얼마나 많은 일을 하는지 척도

- ILP(Instruction level parallelism) - 사이클당 처리되는 명령어수를 증가시키자. 즉, IPC를 높이자 그러나  한계에 부딪힘. 그래서 
 
- TLP(Thread Level Parallelism)이 나옴 - 칩 안에 하나 코어 복잡하게 만들어봤자 열만 펄펄 난다. 그래서 적당한 코어를 여러 개 배치하는 방식으로 바꿈.

> 멀티코어와 멀티쓰레딩이 비슷한 건가...?

- CPU의 주소는 2개. 1. physical address - DRAM의 주소  2. virtual address - 프로그램의 주소

- 36bit pysical address - 2의 36승 bite -> 64GB

- 컴퓨터 속도측정 -  한 프로그램 내 총 실행할 명령어 수(프로그램당 명령어) * 명령어당 사이클수(CPI) * 1 사이클 시간

### 2
프로그래밍 패러다임
- 1 Impretive - how
- 2 Procedure 
- 3 Object-oriented
- 4 Funcional - What


- 컴파일러 - Translation
- 인터프리터 - Translation과 동시에 바로 실행 예) 셸
- 어셈블리 - 컴파일러의 일부

- lecical -lex, syntax - yark, sementic

- 머신에 상관없이 인터미디어(중간) 코드 생성. 그 이유는 [디커플링](https://books.google.co.kr/books?id=RTJFDwAAQBAJ&pg=PA329&lpg=PA329&dq=%EC%BB%B4%EA%B3%B5+%EB%94%94%EC%BB%A4%ED%94%8C%EB%A7%81+%EB%9C%BB&source=bl&ots=DG0X3EmpQT&sig=ACfU3U36wC3fFZmodaSpB-96aLAa7cuAOg&hl=ko&sa=X&ved=2ahUKEwiCzrm--9_nAhWXHHAKHcmBATEQ6AEwBHoECAkQAQ#v=onepage&q=%EC%BB%B4%EA%B3%B5%20%EB%94%94%EC%BB%A4%ED%94%8C%EB%A7%81%20%EB%9C%BB&f=false) 때문이다. 밀접하게 연결된 두 개체를 떼어 내서 접점을 제거한다는 의미. X86이든 리스크든 어디서도 적용 가능한(?) 코드를 만들기 위해서인가? 아닌가?

- 모든 바이트는 주소를 가진다 (2의 30승 = 1기가)

- 메모리는 프로그램 입장에서 `Linear array bite`

- 주소의 크기가 프로그램 크기 결정(32비트 시대에 최대 프로그램은 4GB에 불과)

- 워드 - 연산의 기본 단위(32비트 머신은 32비트 워드)
- 워드와 레지스터 크기는 같다.

- alignment - 모든 데이터는 배수에 저장 (32비트 워드는 4의 배수에서 시작) int가 2비트인 모양

Machine Instruction
1. 계산 논리
2. ALU 정수 레지스터
3. Floating pointing unit 실수 레지스터

- jump - 명령 순서 변경 (브랜치)
- Oprerand - 데이터
- Input - Memory destination - 레지스터

- data transfer instruction - load, store

- IO 디바이스는 메모리 주소에 맵돼 있다. 이 주소를 포트라고 한다.

- Control Transfer Instruction - 명령어 순서를 바꿔준다. (브랜치라고도 표현)

- Program Counter - 읽어올 다음 명령어 주소를 가리킨다.
















- [Amdahl's law](https://parkcheolu.tistory.com/34](https://parkcheolu.tistory.com/34) - 속도를 계산할 때  병렬화될 수 있는 부분은 병렬가능 개수로 나눈 수치 만큼 향상이 가능하다.








[위키백과](https://ko.wikipedia.org/wiki/NAND_%EA%B2%8C%EC%9D%B4%ED%8A%B8)
