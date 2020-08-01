---
layout  : wiki
title   : 
summary : 
date    : 2020-07-18 09:54:47 +0900
updated : 2020-08-01 13:45:27 +0900
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


## 데이터 엔지니어의 일주일

### 데이터 팀원 백그라운드

- 물리학 박사, 생물학 박사 - 물리학자 대부분 코드를 짜서 실험한다. 데이터사이언티스트로 매우 적합하다. 
- 93년생 경영학 전공 수구선수, 디자이너, 경제학, 공연
    - 서포트팀에서 적극적으로 Mysql 코스를 만들기까지
    - 포인트 - 변화를 두려워하지 말아야 한다.

### 월요일 

- 스프린트(JIRA)
    - Sprint 데모 미팅( 말보다 데모로 ) 30분
    - Sprint 회고 미팅( 화이트 보드 3섹션) 30분
        - 미팅 주도 돌아가면서
        - What went well
        - What could have gone better
        - Any discussion points
    - Sprint 플래닝 미팅 30분
        - 40%는 인프로 코드 리팩토링에 사용
        - 미팅 제외 하루 5시간 일한다고 가정하고 플래닝
- On-Call 엔지니어 새로 지정
    - 다음 일주일간(주말 포함) 모든 data pipeline 실패와 관련한 이슈 해결
    - 데이터 파이프라인 실패율을 Key metriecs로 관리
    - 서머리 테이블에 집중

### 화요일

- Daily standup
    - What did you do in the past 24 hours?
    - What do you plan to do in the next 24 hours?
    - Any blockers/issues to discuss?
- 여러 사람들과 일대일 미팅들
    - 내부 팀원들과의 미팅
        - DE <-> DA
        - DE <-> DS
        - DA <-> DS
    - 다른 팀과의 sync-up 미팅
- 데이터 파이프라인 개발과 최적화

### 수요일/목요일 

- Daily standup (Slack virtual standup)
- 데이터 파이프라인 개발과 최적화
- 미팅

### 금요일

- Daily standup
- 데이터 파이프라인 개발과 최적화
- 주간 스태프 미팅
    - Metrics & Quarterly Goals 리뷰
        - Data Warehouse capacity 리뷰
            - Percentage of Core Data Pipeline Failure
            - SLA of Core Data Pipeline Failure 
    - Recruiting & Retention
    - 주간 Incident 리뷰 - Postmortem 미팅
    - 메인 프로젝트 리뷰
        - External projects
        - Internal projects
    - 팀/개인 업데이트 

### 데이터 엔지니어링 == 노가다?

- Keep data pipline running with high quality data
    - Integration with Slack/Emails for failure
    - Input and output validation!!!
    - SLA(service level agreement) with different stakeholders
        - Marketing team
        - Finance team
- Noticing semantic change is a lot harder than data pipeline failures
- Backfilling becomes very important
    - 백필 - 데이터파이프라인 실패 시 재실행
- Full fresh vs. Incremental update

## Data Warehouse Deep Dive

### Data Warehouse: a separate SQL database

- 2005~2011's 하둡 주목 - SQL은 죽었다?
    - SQL은 작은 규모에서 운용?
    - Map Reduce 각광
        - C++로 웹서비스 만드는 느낌
    - 2010년 이후 다시 SQL로 회귀
        - 스크립트 언어로 웹서비스 만드는 느낌
        - 1960's부터 시작된 성숙된 SQL
- 2010's 이후 하둡 등의 Map Reduce 위에 SQL 지원하는 기능 등장
    - Hive, Spark 빅데이터 프로세싱에도 SQL 사용
- 상황에 맞게 데이터베이스 선택
    - Mysql를 데이터 웨어하우스로 사용하는 기업도 존재
- Data Warehouse isn’t your production database
    - It should be separated from your production database
        - 쿼리 잘못 날리면 production에 영향을 주므로
    - OLAP (OnLine Analytical Processing) vs. OLTP (OnLine Transaction Processing)
        - OLAP은 느려도 된다. 프로덕션인 OLTP는 단방에 결과가 나와야 한다는 암묵적 인식

### Central Data Storage of your Company

- The first step to become a “true” data organization
    - data warehouse가 생겨야 진정한 데이터 조직
    - 프로덕션과 나뉘기 때문에 자유도가 높아져
- Store raw data from different sources
    - 프로덕션 이외에 소스가 생겨서 더 많은 일이 가능해져
- Create summary tables out of the raw data (and other summary)
    - Expose the summary tables to employees within the company
    - Being consistent is more important than being correct
- Fixed Cost option vs. Scalable Option
    - Scalable options will provide decoupled capacity increase of storage and computation
        - BigQuery and Snowflake
    - Fixed cost option will give stable cost projection
        - Redshift or most on-premise data warehouse (open source based)

## Redshift Introduction

- 페타바이트까지 지원가능하지만 페타까지 가면 느려질 것
- 여전히 OLAP
    - 쿼리 응답 느리다
- 컬럼별 저장
    - 컬럼별 압추 가능하다
- 벌크 업데이트 지원
- SQL로만 쿼리 날려야 하므로 테이블 스키마 디자인 매우 중요

### Redshift is Postgresql 8.x compatible 

- But Redshift doesn’t support full features of Postgresql 8.x
- Accessible through most Postgresql 8.x clients/libraries
    - JDBC/ODBC
- SQL is the main language to interact
- Table schema design matters

### Redshift Optimization can be tricky

- Distkey, Diststyle and Sortkey need to be optimized in 2+ Redshift cluster
    - distyle is to determine how distribution will be done
        - all, even, and key (default is “even”)
    - distkey is to determine which node to store a record
    - sortkey is to determine in what order to store records in a node
- A skewed table can cause a big performance issue
- 예 : 서버 8대 - 테이블 1개 만들고 레코드 넣으면 8대 중 1대로 가야 하는데 어떤 키를 써야 할지 개발자가 정해줘야 한다. 빅쿼리 등과 달리 레드시프트만 개발자가 정해줘야 한다.
    - 랜덤, 모든 노드, 키 값에 맞춰서 Ditribution Key
        - 무조건 Even(랜덤) 쓰는 게 상책

<img width="458" alt="스크린샷 2020-07-26 오후 8 56 23" src="https://user-images.githubusercontent.com/48748376/88478536-32045980-cf84-11ea-8fb0-896f10a32a74.png">

- 기본 SQL이지만 파이썬으로 보강 가능
- 캐시 도입 가능
- Bulk Update Sequence -> S3 -> 레드시프트
    - 카피 커맨드 -> 조단위 레코드 입력도 가능, 몇 백배 차이를 낼 수 있음

## How to Access Redshift

- JDBC/ODBC desktop tools such as
    - SQLWorkBench, Navicat and so on
    - Requires IP registration for outside access
- JDBC/ODBC Library
    - Any PostgreSQL 8.0.x compatible should work
    - In Python, psycopg2 is a preferred module to use

### User and User Group Creation

```python
CREATE GROUP analytics_users;
GRANT USAGE ON SCHEMA analytics TO GROUP analytics_users;
GRANT USAGE ON SCHEMA raw_data TO GROUP analytics_users;
GRANT SELECT ON ALL TABLES IN SCHEMA analytics TO GROUP analytics_users;
GRANT SELECT ON ALL TABLES IN SCHEMA raw_data TO GROUP analytics_users;
GRANT ALL ON SCHEMA adhoc to GROUP analytics_users;
GRANT ALL ON ALL TABLES IN SCHEMA adhoc TO GROUP analytics_users;

CREATE USER keeyong PASSWORD '...';
ALTER GROUP analytics_users ADD USER keeyong;
```

- [과제](https://github.com/yongjunleeme/data-engineering-study/commit/ce8eda0e40a175386e029ea176bdd9c0135bf099)
- GROUP BY 1, ORDER BY 1 are preferred (concise)
- TO_CHAR (ts, ‘YYYY-MM’)
    - LEFT(ts, 7) or DATE_TRUNC(‘month’, ts) does the same
- Counting distinct userid not sessionid

- Criteria of Good Summary Tables
    - Make your job or other’s job faster and easier and most importantly accurate/consistent
        - Key Metrics Calculation (Revenue, User Growth, …)
        - Marketing Performance
    - Build around key assets of your service/product
- Items you are selling, Customers


## SQL for Data Engineer

### Easy to Use and Vetted over Time

- 맵리듀스에 비해 쉽다
- SQL was originally developed in IBM in early 70s
- There are two parts
    - DDL: Data Definition Language
        - CREATE TABLE, DROP TABLE, ALTER TABLE
    - DML: Data Manipulation Language
        - SELECT, INSERT INTO
- In mid 2000s it was less popular as new big data technologies emerged
    - But eventually all of them supported some forms of SQLs

### Disadvatage of SQL

- Optimized for structured data handling
    - Regular expression and JSON can be used for unstructured data handling to a certain degree
    - Redshift can only handle a flat structure (not nested)
        - Big Query supports a nested structure (very powerful)
- Star schema isn’t always great vs. Denormalized schema
- There is no standard syntax (various SQL dialects)
    - Mostly similar though
- Not very flexible in handling semi-structured or unstructured data:
    - More flexible large scale unstructured data processing requires another framework
    - Spark, Hadoop (MapReduce -> Hive) and so on

### Redshift SQL

- 데이터 퀄러티 - 현업에 클린 데이터란 없다.
    - 처음 쓰는 데이터는 절대 믿지 말라.
    - 스키마만 보고 짐작하면 오판 가능성 높다.
    - 실제 레코드를 몇 개 살펴보는 것이 정답!
        - 어떤 데이터인지 이해할 수 있을 정도는 확인해야
- 같은 PK가 있어도 삽입 가능
    - PK 유니크 보장하려면 확인 절차가 퍼포먼스에 걸림돌이 된다.
    - 엔지니어가 보장해야


- Comment
    - -- for single line comment, /* */ multi line comment

#### DDL (Data Definition Language)

- CREATE TABLE
    - Primary key: Big Data 데이터웨어하우스에서는 지켜지지 않음 (Redshift, Snowflake, BigQuery)
    - CREATE TABLE table_name AS SELECT
        - vs. CREATE TABLE and then INSERT
- DROP TABLE
    - DROP TABLE IF EXISTS table_name 존재하면 삭제 아니면 말고
- ALTER TABLE
 - Add new columns, Rename existing columns, dropping existing columns


#### SELECT

- SELECT COUNT(1) FROM raw_data.session_timestamp;
    - COUNT(1) vs. COUNT(*)  - count(1)이 더 빠르다
    - CASE WHEN
        - Multiple CASE WHEN: to convert values from integer to string or vice versa
- GROUP BY
    - Count by channel
- ORDER BY
    - ASC vs. DESC
    - Multi-Column ordering
        - ORDER BY 1, DESC 2;  -> 첫 번째 컬럼은 오름차순으로 하되 같은 값들이 존재하면 두 번째 컬럼의 내림차순으로 

#### WHERE

- IN
    - WHERE channel in (‘Google’, ‘Youtube’)
        - WHERE channel = ‘Google’ AND channel = ‘Youtube’
    - NOT IN
- LIKE and ILIKE
    - LIKE is a case sensitive string match. ILIKE is a case-insensitive string match
    - WHERE channel LIKE ‘G%’ 
    - NOT LIKE or NOT ILIKE
        - ILKE - 대소문자 구별 노노
- BETWEEN
    - Used for date range matching

#### STRING Funcions

LEFT(str, N)
REPLACE(str, exp1, exp2)
UPPER(str)
LOWER(str)
LEN(str)

#### INSERT INTO vs. COPY

- INSERT INTO is slower than COPY
    - COPY is a batch insertion mechanism.
- INSERT INTO table_name SELECT * FROM …
    - This is better than CTAS (CREATE TABLE table_name AS SELECT) if you want to control the types of the fields
    - 필드 타입과 이름 지정 불가, text 타입과 크기도 사용 불가
    - But matching varchar length can be challenging
    - Snowflake and BigQuery support String type (no need to worry about string length)

#### Type Cast and Conversion

- DATE Conversion:
    - CONVERT_TIMEZONE
        - CONVERT_TIMEZONE('America/Los_Angeles', ts)
    - DATE, TRUNCATE
    - DATE_TRUNC
        - 첫번째 인자가 어떤 값을 추출하는지 지정 (week, month, day, …)
    - EXTRACT or DATE_PART: 날짜시간에서 특정 부분의 값을 추출가능
- Type Casting:
    - cast or :: operator
    - category::int or cast(category as int)
- TO_CHAR, TO_TIMESTAMP

#### Prctice

```python
1)
CREATE TABLE adhoc.이름_channel (
   channel varchar(32) primary key
);

INSERT INTO adhoc.이름_channel VALUES ('FACEBOOK'), ('GOOGLE');

SELECT * FROM adhoc.이름_channel;

2)
DROP TABLE adhoc.이름_channel;

CREATE TABLE adhoc.이름_channel AS
SELECT DISTINCT channel
FROM raw_data.user_session_channel;

3) 

ALTER TABLE adhoc.이름_channel RENAME channel to channelname;
INSERT INTO adhoc.이름_channel VALUES ('TIKTOK');
```

```python
%%sql

CREATE TABLE adhoc.yongjunleeme_channel as
select distinct channel
from raw_data.user_session_channel;

ALTER TABLE adhoc.yongjunleeme_channel RENAME channel to channelname;
INSERT INTO adhoc.yongjunleeme_channel.value('TICTOK');
```

```python
SELECT
    LEN(channelname),
    UPPER(channelname),
    LOWER(channelname), 
    REPLACE(UPPER(LEFT(channelname, 4)), 'OO', 'XX')
FROM adhoc.yongjunleeme_channel;
```

- Session이 가장 많이 생성되는 시간대?

```python
SELECT EXTRACT(HOUR FROM st.ts),
COUNT(DISTINCT(usc.userid))
FROM raw_data.user_session_channel usc
JOIN raw_data.session_timestamp st
ON usc.sessionid = st.sessionid
GROUP BY 1
ORDER BY 2 DESC;
```

#### JOIN

- [이미지](https://theartofpostgresql.com/blog/2019-09-sql-joins/)
- Be Careful in using JOIN
- JOIN을 하는 테이블들간의 관계
    - One to one 
        - 조인 시 레코드 수 그대로 또는 교집합 수로 줄어든다
    - One to many?
    - Many to one?
    - Many to many?
        - 조인 시 레코드 수 늘어난다.
            - 데이터가 클린하지 않으면 결과의 레코드수가 확 늘어나 문제
- When to use INNER JOIN vs. LEFT JOIN
    - Outer JOIN or RIGHT JOIN are relatively rare to use

```python
SELECT c.channelname, COUNT(DISTINCT(usc.userid)) FROM raw_data.channel c JOIN raw_data.user_session_channel usc ON c.channelname = usc.channel JOIN raw_data.session_timestamp st ON usc.sessionid = st.sessionid GROUP BY 1 ORDER BY 2 DESC; 
Keeyong에서 모두에게: (11:48 오전)
```

```python
SELECT c.channelname, COUNT(DISTINCT(usc.userid)) FROM raw_data.channel c LEFT JOIN raw_data.user_session_channel usc ON c.channelname = usc.channel LEFT JOIN raw_data.session_timestamp st ON usc.sessionid = st.sessionid GROUP BY 1 ORDER BY 2 DESC; 
```

```python
GRANT SELECT ON TABLE raw_data.channel to group analytics_users; 
```

#### NULL

- 값이 존재하지 않음을 의미
    - 0이나 비어있는 string과는 다름을 분명히 인지
- IS NULL or IS NOT NULL
    - 노노 - NULL or <> NULL
    - Boolean 타입의 필드도 “IS TRUE” 혹은 “IS FALSE”로 비교
- LEFT JOIN시 매칭되는 것이 있는지 확인하는데 아주 유용
- NULL 값을 다른 값으로 변환하고 싶다면
    - COALESCE를 사용

```python
SELECT SUM(amount)
FROM raw_data.session_transaction
WHERE refunded IS True;
```

#### Advanced SQL

- UNION (합집합)
    - 여러개의 테이블들이나 SELECT 결과를 하나의 결과로 합쳐줌
    - UNION vs. UNION ALL
    - UNION은 중복을 제거
- EXCEPT (MINUS)
    - 하나의 SELECT 결과에서 다른 SELECT 결과를 빼주는 것이 가능 
- INTERSECT (교집합)
    - 여러 개의 SELECT문에서 같은 레코드들만 찾아줌

#### COALESCE, NULLIF

- COALESCE(Expression1, Expression2, …):
    - 첫번째 Expression부터 값이 NULL이 아닌 것이 나오면 그 값을 리턴하고 모두 NULL이면 NULL을 리턴한다.
    - NULL값을 다른 값으로 바꾸고 싶을 때 사용한다.
- NULLIF(Expression1, Expression2):
    - Expression1과 Expression2의 값이 같으면 NULL을 리턴한다

#### DELETE FROM vs. TRUNCATE

- DELETE * FROM vs. DELETE FROM
- DELETE FROM table_name 
    - 테이블에서 모든 레코드를 삭제
    - vs. DROP TABLE table_name
    - WHERE 사용해 특정 레코드만 삭제 가능:
        - DELETE  FROM raw_data.user_session_channel WHERE channel = ‘Google’
- TRUNCATE table_name도 테이블에서 모든 레코드를 삭제
    - DELETE FROM은 속도가 느림
    - TRUNCATE이 전체 테이블의 내용 삭제시에는 여러모로 유리
    - 하지만 TRUNCATE는 WHERE을 지원하지 않음	

#### WINDOW

- Syntax:
    - function(expression) OVER ( [ PARTITION BY expression] [ ORDER BY expression ] )
- [Useful functions:](https://docs.aws.amazon.com/redshift/latest/dg/c_Window_functions.html)
    - ROW_NUMBER, FIRST_VALUE, LAST_VALUE
    - Math functions: AVG, SUM, COUNT, MAX, MIN, MEDIAN, NTH_VALUE

#### Practice

- 사용자별로 처음 채널과 마지막 채널 알아내기
- ROW_NUMBER vs. FIRST_VALUE/LAST_VALUE
- 사용자 251번의 시간순으로 봤을 때 첫 번째 채널과 마지막 채널은 무엇인가?
    - 노가다를 하자면 아래 쿼리를 실행해서 처음과 마지막 채널을 보면 된다.

```python
SELECT ts, channel
FROM raw_data.user_session_channel usc
JOIN raw_data.session_timestamp st ON usc.sessionid = st.sessionid
WHERE userid = 251
ORDER BY 1
```

- ROW_NUMBER 이용해서 해보자
    - ROW_NUMBER() OVER (PARTITION BY field1  ORDER BY field2)

```python
SELECT ts, channel, ROW_NUMBER() OVER (partition by userid order by ts) as N
FROM raw_data.user_session_channel usc
JOIN raw_data.session_timestamp st
ON usc.sessionid = st.sessionid WHERE userid = 251; 
```

```python
SELECT * FROM (SELECT ts, channel, ROW_NUMBER()
OVER (partition by userid order by ts) as N
FROM raw_data.user_session_channel usc
JOIN raw_data.session_timestamp st
ON usc.sessionid = st.sessionid
WHERE userid = 251 ) WHERE N = 1
```

#### [JSON Parsing Functions](https://docs.aws.amazon.com/redshift/latest/dg/json-functions.html)

- JSON의 포맷을 이미 아는 상황에서만 사용가능한 함수
- JSON String을 입력으로 받아 특정 필드의 값을 추출가능 (nested 구조 지원)
    - 예제) JSON_EXTRACT_PATH_TEXT

```python
SELECT JSON_EXTRACT_PATH_TEXT
('{"f2":{"f3":1},"f4":{"f5":99,"f6":"star"}}','f4', 'f6');
```

## Link

- [실리콘밸리에서 날아온 데이터 엔지니어링 with Python](https://programmers.co.kr/learn/courses/10505)
