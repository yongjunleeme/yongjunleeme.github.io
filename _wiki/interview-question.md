---
layout  : wiki
title   : 
summary : 
date    : 2020-05-11 21:46:27 +0900
updated : 2020-05-11 22:15:18 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# Database

- DB 스케일링 업을 무한정 할 수 있는데 안하는 이유
- PK를 구분하는 이유
- DB 모델링 시 고려하는 부분
- ERD 변경을 한 적이 있는가?
- 정규화 기준
- MySQL 이외에 하둡 같은 분산 처리를 사용하는 이유
- NoSQL을 사용하는 이유 및 사례, 개념
- NoSQL, SQL 장단점
- Database가 물리적으로 저장되는 과정
- DB Indexing? 왜 사용하는가?
- DB replication? 사용하는 이유?
- MongoDB
- sharding
- 모델링 절차
- DB tuning
- 상용화된 서비스의 skima가 잘못되었다고 판단되어 skima 수정이 필요 할 시, 어떻게 해야되는가?
- Data란? Data 전처리?
- SQL normalization
- JOIN

# Deploy

- AWS EC2, RDS, S3
- 위의 서비스 선택할때 c,m,r로 컴퓨터 스펙에 관련하여 차이가 있는데 아는지?
- 그럼 C는 왜 제일까고 쓰는 이유는 뭘까요?
- 대규모 트래픽 처리한 경험?
- Docker, 사용이유? Container, Image
- 클라우드 컴퓨팅
- AWS Lambda?
- 람다 함수와 파라미터 관련 event, context
- 내 서버의 API가 10000명의 동시접속자가 있다고 가정해보자, 견딜수있나?
- Kubernetes
- Serverless
- 쓰로틀
- Firebase
- Reverse proxy
- load balancer
- MSA
- 배포 절차

# DevOps

- DevOps? (문화에서 직업으로)
- CI/CD
- 소프트웨어 생명 주기 관련 데브옵스의 역할, 하는 일
- 빌드, 테스트, 배포, 운영이라는 4가지 관점에서 본인이 생각하는 최적화는?

# 크롤링

- Selenium의 구동방식? 따로 쓰는 이유?
- Xpath vs by class name

# 암호화

- JWT 구성요소 세가지
- JWT의 페이로드에 들어가서는 안될 내용? 이유는?
- JWT의 유효기간? 그 이유는?
- 페북/인스타는 로그인 만료기간이 엄청 긴데, 어떻게 보안 관리를 하는지 아는가?
- JWT 변조 공격 어떻게 대처 하겠는지?
- Bcrypt 작동원리? 왜 사용? 단점?
- 다른 암호화 라이브러리 아는게 있는지?
- Token 말고 다른 방식 인증?
- JWT 인증 방식 vs 세션 인증 방식
- 암호화 Tool(Bcrypt, Argon2) 를 사용하는 이유?
- Access Token, Refresh Token
- JWT를 활용하여 이상적인 Scalable한 인증서버를 만들려고 하고자 할때 어떻게 구성을 할 것인가?

# Django

- ORM
- ORM과 쿼리 중 무엇을 선호? 차이점?
- Why Django?
- MVC 패턴에 대하여(Django MVT 방식대로 설명)
- 라이브러리 vs 프레임워크
- Django vs Flask
- Django vs Java Spring
- ORM에서 Users.objects.all()을 한 다음 다른 변수에 저장하고, 또다른 변수에 저장하여 Filter를 걸고 이후에 Print를 했다. Database의 Read는 몇 번 일어났는가?
- 같은 API에 Get요청을 여러 번 보내서 똑같은 요청을 할 경우, 효율성을 증대시키는 방법이 있는데 장고는 이를 자동으로 해준다. 이게 뭔지 알고있는지?(정답은 캐싱)
- DRF
- APP 어떤 기준으로 분리했는지?
- 클래스 인스턴스 활용
- 일대다, 다대다 관계
- request 요청시 동작 순서
- Django 장단점
- test.py, 유닛테스트, 사용한 모듈
- 로컬 DB와 서버 DB의 구조 차이로 인한 migrate가 conflict 날 경우 해결법?

# Python

- 데코레이터, 이터레이터, 제너레이터
- JS의 Callback vs Python Decorator
- Decorator 사용시 주의점
- Why Python?
- 파이썬 가비지콜렉터 동작방식, 그리고 집적 구현한다면 어떤식으로 하겠는가?
- GCC(GNU Compiler Collection)이란 (파이썬 엔진과 연관지어서)
- 파이썬 인터프리터가 기존 컴파일러 언어에 비해서 퍼포먼스가 낮은데도 불구하고 파이썬이 주언어인 이유?
- 스크립트 언어 vs 컴파일 언어
- 디스크럽터
- 정규표현식
- 클래스 상속 시 메서드 실행 방식
- PyPy가 CPython보다 빠른 이유
- (JS와 비교하여) python의 장단점

# API

- REST API / REST 탄생 배경 / RESTful
- 서로 다른 API로 요청이 동시에 들어오면서 해당 API들이 내 DB의 같은테이블 하나를조회한다고 가정했을때, 병목현상이 생길텐데 어떻게 처리할 것인가?
- 이미지 업로드 어떻게 하는지?
- 자동 문서 도구 툴
- Graphql과 RestfulAPI를 비교했을 때 Graphql의 장단점은?

# Network

- web server(Apache, Nginx)? 장점?
- HTTP 1.0/1.1/2.0/3.0의 특징 (Keepalive -> TCP/IP -> Header Compression -> UDP)
- 웹브라우저 동작원리(프론트 ->브라우저 -> 백엔드 -> DB -> OS 순으로)
- HTTP vs HTTPS
- HTTP 요청/응답 헤더
- TCP/IP와 UDP의 특징과 차이에 대해. 각각의 header
- localStorage, sessionStorage, Cookies / 그 외에 브라우저에 저장하는 방법?
- url에 [google.com](http://google.com) 주소 치면 어떤 과정 생기나?
- 단순히 글을 조회하지만, 글을 조회할때 조회수가 올라간다 이럴때 http 메소드는 뭘써야 하겠는가?
- HTTP request 구성
- Response code
- 500대 코드 에러 처리
- 서버가 죽으면 어떻게 500 에러를 내는 것인가
- www
- protocol
- DNS
- 크로스 브라우징
- 웹 성능과 관련된 이슈
- 서버 사이드 렌더링, 클라이언트 사이드 렌더링
- HTTP 인증 방식
- 데이터 주고받을때 json형식밖에 없나요? 왜 제이슨을 쓰나요
- 이미지나 데이터 로딩이 많을때 로딩시간 최적화 방법은?
- Socket.io vs WebSocket
- 동기 vs 비동기
- 비동기 처리는 왜 하나?
- CORS 통신
- GET, POST 메서드
- Frame, Packet, Segment, Datagram

# Git

- git, github
- 협업 시 git 관리 어떻게 했는가?
- 코드 리뷰를 해본 적이 있는지?
- Git Flow / branch 관리
- git rebase / squash
- git branch 어떻게 따는가?
- 이미 서비스가 런칭된 상태인데 기획자가 바뀐 기획의도를 가지고 웹서비스를 다시 만들라고 한다면 git을 어떻게 사용할 것인가?
- Git Conflict 상황에서 해결 방법

# 자료구조

- FIFO
- FIFO 반대 개념?
- Set / Set을 쓰면 좋은 예시?
- 숫자가 정리되지 않은 리스트가 있고, 해당 리스트에 특정 값이 있는지 확인해야한다. 효율적인 방법을 제시해 보아라 (정답은 binary tree)
- List의 resizing이 언제 일어나는가? 크기가 어떻게 변하는가? 두배로 늘린다고 하면 문제점? 얼마만큼의 분량을 재할당 하는게 효율적인가?
- Tree vs Graph. 깊이 탐색과 너비 탐색
- Array vs Linked list
- HashTable
- Stack
- Queue
- Binary Heap
- Red-Black Tree
- B+ Tree

# 기타

- CDN 써보았는지? 개념은?
- 복잡도에 대해 설명하라. ex)빅오 표기법
- 정렬에 대해서 아는가?
- soft delete
- 총 4대의 서버가 존재하고 동시에 크론탭을 돌릴 때 어떻게 처리?
- Scrum 방식
- 레거시 코드
- Scale up / Scale out
- 인증/인가
- 시멘틱 마크업? 왜 준수해야 하나?
- DOM? DOM이 만들어지는 과정
- 미디어 쿼리
- SPA / 장단점 / 단점 보완
- 객체 지향 프로그래밍
- 함수형 프로그래밍
- 레이지 로딩
- 클린아키텍쳐
- 부하테스트
- TDD
- Agile
- 0.2 + 0.1의 결과값?
- Reference Error
- Garbage collector
- Serializer
- OOP
- Linux, 명령어
- CRUD
- IDE
- Compiler VS Interpreter
- Thread process
- PIG란(Apache PIG인듯)
- Hash, Hash 충돌, 해결책
- 추상화란?
- Call by Reference Vs. Call by Value
- PWA
- GIL과 그로인한 성능 문제
- GC 작동 방식
- Celery
- 메모리 누수가 발생할 수 있는 경우
- Duck Typing
- 딥러닝
- ML
- AI
- IoT
- 분산락
- 64bit CPU와 32bit CPU 차이
- CVS, SVN, Git
- 웹 서버(Web Server)와 웹 어플리케이션 서버(WAS)의 차이
- Servlet과 JSP
- Memcached와 Redis의 차이
- Maven과 Gradle의 차이 
