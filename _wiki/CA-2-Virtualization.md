---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:20:49 +0900
updated : 2022-02-13 00:20:49 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

 ## Virtualization
Virtualization의 핵심은 일루젼
컴퓨터 역사는 Virtualization의 역사다.

## Virtualization Example: Process abstraction
Process는 Logical CPU다.
피지컬 1개지만 로지컬로 여러 개(Multiple logical CPUs)
피지컬 1개의 핵심? ADT의 State다.
State: 레지스터(버추얼 메모리에 일부 세이브했다가 CPU 차지하면 사용)와 메모리(버추얼 메모리로)
Timer Interrupt로 독점 제어

## Virtualization Example: Virtual Memory
Virtual Machine
CPU 하나인데 무한개 컴퓨터 시스템인 것처럼
State: 컴퓨터 시스템의 모든 State
VMM(virtual machine monitor) 버추얼 머신 모니터
캐시- locality(Spatial 예) array[0], array[1], Temporal 예)for-loop)

환상의 만남
1) 메모리 기술(다양한 메모리 소자 속도, 가격, 용량 트레이드 오프)
2) locality

SRAM 액세스 시간? 10 nano초 이하
DRAM 액세스 시간? 100 nano초 이하 
하드 액세스 시간? 10 milli초

SRAM과 DRAM 가상화 - 캐시는 하드웨어로 동작. OS 소프트웨어 관여 못한다. 인터럽트 한 번 걸면 micro초 단위로 걸리니 속도에 이점이 없어지므로
DRAM과 하드디스크 가상화 - OS과 관여, 정말 디스크로 가는 것은 느리니 프로그램으로 막아야 하니깐?

## Two Aspects of Virtualization
1) 기능
- 하드웨어: MMU(Memory management unit)과 exception 메커니즘(Page fault)
	- MMU: 프로그램 주소를 주면 그 주소가 피지컬에서 어디있는지 트랜스레이션
	- page fault: 메인 메모리에 내용이 없으면 하드 디스크에서 가져와야 하는데 그때 Page fault를 알려주는 역할
- 소프트웨어: OS가 제어하는 버추얼 메모리의 프로세스?
2) 성능
- 메모리 참조 최적화 
	- Temporal locality
	- Spatial locality
- 페이지 테이블 참조
	- 참조하는 주소 테이블이 하드웨어로?--> TLB(Translation Look-aside Buffer) 

## Interrupt와 Exception 차이
둘 다 이벤트, OS가 제어권 가져간다.
- Exception:지금 돌고 있는 프로그램이 유발, 동기적  예) Page fault
- Interrupt: 외부 이벤트에 의해 유발, 비동기적  예) Timer  

그냥 있는 내용을 받아들이는 것으로는 밖에 나가서 문제를 풀 수 없다.

## Source 
- [서울대학교 민상렬 교수님 2011 Computer Architecture](https://olc.kr/course/course_online_view.jsp?id=240)
