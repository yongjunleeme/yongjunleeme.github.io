---
layout  : wiki
title   : 
summary : 
date    : 2020-07-18 09:54:47 +0900
updated : 2020-07-18 13:38:01 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 데이터 팀의 역할 소개

- 데이터 유무 > 데이터 지식

### 데이터 조직의 목표

- Data Flow
    - Data Warehouse - 한 곳으로 모아 저장해야 편의성이 높아진다.
    - Business Insights
    - Site Services - Scalalble Product Improvement
- 예시
    - Build Leverage for the company through Trustworthy Data - Airbnb
        - 부수적인 뉘앙스
        - 데이터팀의 공헌도 설명이 중요한 포인트
    - Providing higth quality data in timely fashion so that(애널리스트)
        - data informed decisions can be made
            - 데이터 기반과는 다른 데이터를 참고하겠다는 뉘앙스
            - 데이터 기반의 시기적 특성은 미래보다 과거부터 현재까지이므로 기반을 삼아서는 방향을 바꾸기는 어렵고 참고로 삼아야 더 나은 결정의 가능성 생긴다.
    - Providing higth quality data in timely fashion so that(사이언티스트)
        - scalabe product improvement(personalization) by algorithm
            - But don't undervalue hubam-in-the-loop

### 데이터 조직 직군별 역할

- Data Scientest(Product Science)
    - 엔드유저를 대상으로 하므로 External 성격
    - 워터폴보다 애자일이 효율적
        - 데이터 모으기 - 분석 - 알고리즘 업데이트 - 알고리즘 개발(업데이트에서 개발은 자동화) - 실험
    - 알고리즘을 통해 고격경험의 만족도 높여
        - 예측 모델을 만들어 제품 퀄리티를 높인다
    - 실용성과 인내심 필요
    - 스킬
        - 머신러닝에 관한 깊은 지식과 경험
        - Python/Spark 코딩
        - SQL/Hive
        - R/SAS/Matlab (수학, 통계학)
- Data Analyse(Decision Science)
    - 내부 오디언스를 대상으로 하므로 Internal 성격
    - 비즈니스 인텔리전스의 책임
        - key metrics 정의, 대시보드 생성
    - 내부 직원들의 질문 답변
        - 내부 질문이 많이 들어온다.
    - 스킬
        - 비즈니스 도메인 지식
        - SQL
- Data Engineer
    - 데이터 웨어하우스(vs. 데이터 레이크)
        - 데이터베이스 매니징 - RedShift, BigQuery, Snowflake
        - 데이터 파이프라인 매니징 (ETL)
            - 3rd party SaaS ETL - FiveTran, StitchData, Segment
    - A/B Test
    - Data Tools

### 데이터 팀 역할 

- 초기 스타트업
    - 마케팅이 가장 중요
        - 마케팅 채널 어트리뷰션과 코호트 분석
            - 비용 차이 크다
    - 액티브 유저 측정
        - 액티브 유저 정의가 중요
- 성장하는 스타트업
    - 키 Metrics 정의
        - AAA(Actionable, Accessible, Auditable)
    - 데이터 인프라에 적절한 투자 시기 

### 데이터 조직 구조

- 중앙 조직
    - 소통이 어렵다
    - 도메인 지식 습득이 어렵다
    - 데이터 팀 커리어에 도움
- 분산 조직
    - 데이터 조직의 사람이 2등시민이 될 수 있다
- 하이브리드
    - 2년에 한 번 조직 개편(에어비앤비)

### 경험에서 얻은 교훈

- Data Should Drive Business Results
- Infrastructure (and best practices) First
- Quality of Data is Important
- Metrics First 
- The Simpler a Solution is The Better


## 데이터 엔지니어링

### 데이터 웨어하우스 관리

- Redshift vs BigQuery, Snowflake?
    - snowflake, bigquery는 스토리지와 컴퓨테이션 분리해서 업그레이드 가능
        - 예) 매우 큰 조인 쿼리 - 더 갖다 붙이면서 돈 많이 낸다. 쿼리 하나에 만불 나왔다
        - 매일 아침 비싼 쿼리 쓴 사람 top 10을 공개한다.
        - 당장 돈 많이 쓰는 것처럼 보일 수도 있지만 인프라가 커질수록 당연히 더 효율적이다.
    - 레드시프트는 스토리지 컴퓨테이션 같이 해야
        - Fixed cost
        - 예) 매우 큰 조인 쿼리 - 그냥 서버가 죽는다. 
        - 직원 200명 수준까지는 레드시프트가 나을 수도 있다.
- 데이터 웨어하우스 구조
    - 프로덕션
        - raw_data, analytics, adhoc, archive

### Data Pipelines

- Copying data from sources to a destination
    - The destination is Data Warehouse
    - Sometimes data transformation is required
- Data Pipelines == ETL (Extract, Transform and Load) == Data Jobs
    - In Airflow, this is called DAG
- Maintenance is a lot of work
    - Handling failures and backfilling! 
    - How to detect data schema and semantic changes as early as possible is important
- Scheduling
    - By time and by data availability
- Job Dependency

### 데이터 분석가와 과학자 지원

- 서머리 테이블 만들기
    - 로우 데이터가 수많은 트러블의 원인이 될 수 있다.
    - Being consistent is more important being correct
- 앤지니어 없이 분석가가 서머리 테이블 만들 수 있도록 지원
- 과학자가 배포 모델을 빌드할 수 있도록 지원

### 트렌드

- 클라우드 데이터 웨어하우스
- 배치가 아닌 스트리밍, 실시간
    - 카프카 or Kinesis
- SaaS 기반 ETL
- Data DevOps 

## Link

- [실리콘밸리에서 날아온 데이터 엔지니어링 with Python](https://programmers.co.kr/learn/courses/10505)
