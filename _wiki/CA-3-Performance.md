---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:20:59 +0900
updated : 2022-02-13 00:28:50 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Time – the first type of performance

Wall-clock time, response time, or elapsed time
-   actual time from start to completion
-   includes everything: CPU time for other programs as well as for itself, I/O, operating system overheads, etc
성능 측정방법
- Wall clock time(CPU를 점유하는 모든 사례를 합친 시간)을 퍼포먼스 측정으로 하면 어떤 문제? 불확실성(unpredictability)
	- 다른 프로세서 개수, 우선순위 등에 의해 불확실성 야기
- CPU Time: 내가 쓴 시간(user CPU time + system CPU time)
- 어떤 프로세서를 위해 쓴 시간인지 애매한 작은 문제가 남음(예: Timer 인터럽트가 돌면 갑자기 그 때 돌던 프로세서에게 차지가 되니깐)
```shell
time command
```

##  Decomposition of CPU (Execution) Time

1GHz의 1 Cycle time? 1ns

ISA가 Instruction program에 영향 미치는 이유 - 예) RISC인지 CISC인지에 따라 하나의 기능을 수행하는데 필요한 명령어의 수가 차이날 수도 있으므로
ISA가 Instruction당 평균 사이클수에도 영향 이유 - 예) 복잡하면 여러 사이클 걸릴테니깐

Sequential 5사이클 vs pipelined 1사이클(예: 컨베이어 벨트 1사이클에서 차가 생산되어 나오는?)

MIPS (typical RISC) vs. VAX8700 (typical CISC)
- 퍼포먼스는 RISC가 2~3배 더 나은 것으로 측정하는데 왜 X86(CISC)을 쓰는가?
	- x86이 ISA는 CISC 스타일이지만 내부에서는 RISC 방식의 Operation으로 바꾼 뒤 실행시킨다.(무늬만 CISC)
	- 그럼 왜 인텔은 RISC로 바꾸지 않고 CISC를 쓰는가? **Installed base**, 기존 시스템이 CISC로 되어 있어서 매몰비용이 더 클 수 있으므로?

CISC가 나온 배경
- 프로세서가 메모리에 비해 너무 빨라서 한 번에 많은 양을 처리하려고
- 하지만 캐시가 나와서 메모리 속도가 올라감

ARM
- ARM은 처음 만드는 것이므로 CISC와 같은 배경이 없고 Installed base도 없었으므로 RISC 기반으로 출발
- Power 효율성 때문에 전력 소모에 신경을 씀(배터리 2시간 쓰고 방전되면 안 되니깐)
- 이러한 ARM 개발 이후에 데이터센터나 클라우드 컴퓨팅 프로세서도 모바일 프로세서를 쓴다(그린 컴퓨팅)

MIPS 값(1초당 백만개 인스트럭션 실행) cf) MIPS ISA와는 다른 것
문제
- 단위시간에 얼마나 실행만 수행, 각 인스트럭션이 어느 작업을 수행하는지는 상관 X
- 똑같은 프로그램이라도 컴퓨터마다 다를 수 있다
- 경우에 따라 퍼포먼스에 반비례
	- A: 4사이클 걸리는 instruction 2개
	- B: 1사이클 걸리는 instruction 10개
	- MIPS값은 B가 높지만 총 성능은 A가 높다(A: 8사이클, B:10사이클이므로)
 
A:2초, B:4초
A는 B보다 2배 빠르다
A는 B보다 100% 빠르다

## Benchmarks
컴퓨터 작업의 특징을 잘 표현한 엑기스
공평하게 경쟁할 환경을 만들어주는 것
도메인별 벤치마크 다름
- Relevant: meaningful within the target domain
- Coverage: does not oversimplify important factors in the target domain   
- Understandable  
- Good metric(s)  
- Scalable  
- Acceptance: vendors and users embrace it

일반적인 벤치마크
- SPEC: CPU performance   
- TPC: OLTP (On-Line Transaction Processing) performance

챕터 1을 다 읽으라
강의는 큰 그림만 그려줄 뿐 세세한 것은 책을 보아야 함

## Source

- [서울대학교 민상렬 교수님 2011 Computer Architecture](https://olc.kr/course/course_online_view.jsp?id=240)
