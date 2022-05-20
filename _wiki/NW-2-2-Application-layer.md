---
layout  : wiki
title   : 
summary : 
date    : 2022-02-13 00:13:18 +0900
updated : 2022-05-20 17:19:13 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

애플리케이션 계층 
- 2.3 인터넷 전자메일
- 2.3.1 SMTP
- 2.3.2 HTTP와의 비교
- 2.3.3 메일 메시지 포맷
- 2.3.4 메일 접속 프로토콜

- 2.4 DNS-인터넷의 디렉터리 서비스
- 2.4.1 DNS가 제공하는 서비스
- 2.4.2 DNS 동작 원리 개요
- 2.4.3 DNS 레코드와 메시지

- 2.5 P2P 파일 분배

- 2.6 비디오 스트리밍과 컨텐츠 분배 네트워크
- 2.6.1 인터넷 비디오
- 2.6.2 HTTP 스트리밍 대쉬(DASH)
- 2.6.3 콘텐츠 분배 네트워크(CDN)
- 2.6.4 사례연구 : 넷플릭스, 유튜브, Kankan

- 2.7 소켓 프로그래밍: 네트워크 애플리케이션 생성
- 2.7.1 UDP를 이용한 소켓 프로그래밍
- 2.7.2 TCP 소켓 프로그래밍

# 2.2 Application layer 
## 2.3 전자메일
### User agents
아웃룻과 같이 메일 읽고 쓰게해주는 프로그램

mailbox
- 배송되는 메시지 담아
- 사용자별로 존재

message queue
- 발송하는 메시지 담아
- 사용자 구분이 없다.

메일 프로토콜
- Simple Mail Transfer protocol(SMTP)
	- TCP 위에서 동작
- POP3, IMAP, HTTP

프로세스
- 보내는측, 받는 측 각각 유저 에이전트와 유저 서버 존재
보내는 User Agent -- SMTP로 --> sender's mail server -- SMTP로 --> receiver's mail server -- POP, IMAP, HTTP로 --> user agent로
- 유저에이전트에서 메일 서버로 메시지 보냄
- 메일 서버는 메시지큐에 넣고 차례대로 리시버 메일 서버로 보냄
- 받는 사람이 메일 함을 열면 받는 사람 유저에이전트에서 리시버 메일 서버에 저장된 메시지를 읽어온다
	- 여기서는 메시지를 보내는 게 아니라 가져와야 하니깐 SMTP 프로토콜이 아니라 Mail access 프로토콜(예: POP, IMAP, HTTP)을 사용

STMP
- TCP, 25번 포트(Well known port number)
- 1)핸드쉐이킹 2) 메시지 트랜스퍼 3) closure(폐쇄)
- Command / 응답 Interaction
- 1번 연결되면 클라가 QUIT하고 싶을 떄까지 계속 보낼 수 있음
- HTTP: pull(가져오니깐) / SMTP: push
- 둘 다 아스키코드

### Mail Protocols

#### POP3
메시지 리스트 차례로 읽고 지우는 과정
download and delete mode 버전: 메일박스가 비워져(옛날 버전)

download and keep 버전: 메일박스 유지, 읽었던 것도 계속 다시 retrieve
서버간 stateless : 지난 번에 뭘 retrieve했는지 기억 X
메일을 가져오고 읽고 지우는 작업 모두 로컬에서 작업

#### IMAP
로컬이 아닌 서버에서 메시지 처리작업 수행
유저의 세션을 유지
POP3에 비해 복잡하고 무거움

## 2.4 DNS-인터넷의 디렉터리 서비스

DNS: Domain Name System
호스트네임(도메인 네임, www.yahoo.com)을 IP주소로 매핑
분산 데이터베이스에 도메인네임을 저장
네트워크가 아닌 애플리케이션 계층 프로토콜로 구현
	- 패킷만 전달하고 복잡한 것은 네트워크의 Edge로 몰아내자

1) Host aliasing(가명)
- (실제 호스트 네임과 외부에서 쓰는 호스트 네임이 다를 때) alias names(가명들)을 canonical(실제) 이름으로 변환
2) mail server
- 도메인을 담당하는 메일 총괄 서버
3) load distribution
- 로드 밸런싱

왜 중앙화된 DNS를 안 쓸까?
- single point of failure
- Traffic volume
- 먼 곳에서도 하나로 보내야 하면 비효율
- 유지보수 

### Distributed, Hierarchical database

Local(=default name server, =proxy) name server
- 하이라키에 속하지 X
- Who: Residential ISP, 회사, 학교
- 캐시를 설치하면 웹 액세스 요청이 캐시로 먼저 오는 것처럼 로컬로
- 요청 응답 가능하면 응답, 없으면 하이라키 탑인 DNS root로 전달
	- authoritative에 보내면 간단하겠지만 authoritative를 모르니깐 root에 보냄

하이라키: Root DNS > TLD(Top level Domain)> authoritative 기관별 DNS

DNS: root name server
- TLD 서버가 누군지 알고 있음

TLD(Top level Domain) servers
- 도메인 담당하는 authoritative DNS servers를 알고 있음
- 국가: com, org, net, kr, uk, fr, ca, jp
- 네트워크 솔루션(업체이름): com 관리
- 한국인터넷정보센터: kr 관리

authoritative DNS servers
- 관리 서버: (예:학교)기관 내 hostname to IP 매핑
- 결국 IP는 여기에 저장

### DNS name resolution example

iterated query
- 쿼리 응답 못하면 컨택할 다음 상대를 알려줌
- 컨택 순서 local-> root -> local -> TLD -> local ->..

recursive query
- 쿼리에 대해 직접 찾아서 응답해줌
- 컨택 순서 local -> root -> TLD -> authoritative -> TLD -> root ...

query수 어디가 더 많은가? iterated 2개일 때 recursive 4개
load 더 부담? root > TLD
	- TLD보다 root가 담당해야 할 도메인 수가 더 많을테니


DNS 캐싱
- local name server는 TLD 캐싱, root에 안 물어봐도 된다
- out-of-date 문제: TTL(time to leave) 마킹해서 보냄
	- 만료 전에 데이터 바뀌면 그래도 문제
	- 이름 바뀌면 명시적으로 알려야 함

## 2.5 P2P 파일 분배

2.6 비디오 스트리밍과 컨텐츠 분배 네트워크
2.6.1 인터넷 비디오
2.6.2 HTTP 스트리밍 대쉬(DASH)
2.6.3 콘텐츠 분배 네트워크(CDN)
2.6.4 사례연구 : 넷플릭스, 유튜브, Kankan

2.7 소켓 프로그래밍: 네트워크 애플리케이션 생성
2.7.1 UDP를 이용한 소켓 프로그래밍
2.7.2 TCP 소켓 프로그래밍


## Reference

- [컴퓨터 네트워크 - 이화여대 이미정 교수님](http://kocw.net/home/search/kemView.do?kemId=1046412)
