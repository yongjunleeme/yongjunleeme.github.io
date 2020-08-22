---
layout  : wiki
title   : 
summary : 
date    : 2020-07-18 09:54:47 +0900
updated : 2020-08-17 14:49:02 +0900
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

#### 숙제

- [깃헙](https://github.com/yongjunleeme/data-engineering-study/blob/master/sql_week3.ipynb)


## Airflow

### Quick Airflow Intro

- Airflow is a platform for data pipelines in Python (beyond a scheduler)
    - Not just a scheduler but also also a worker(s)
        - Provides a web UI as well
- Airflow is a cluster of servers
    - DAG and scheduling info are stored in a DB (SQLite is used by default)
        - MySQL or Postgres is preferred for real production usage
- DAG -> ETL
    - 태스크로 구성
    - 오퍼레이터의 인스턴스
- 단점
    - 서버 늘어나면 유지보수 복잡해진다
    - GCP provides “Cloud Composer”를 쓰면 편하다

### DAG Common Configuration Example

```python
from datetime import datetime, timedelta
from airflow import DAG
default_args = {
   'owner': '',
   'start_date': datetime(2020, 8, 7, hour=0, minute=00), # 백필할 때, 인크리멘탈 업데이트할 때 유용(복잡도는 상승) 
   'end_date': datetime(2020, 8, 31, hour=23, minute=00),
   'email': [''],
   'retries': 1, # 리트라이 횟수
   'retry_delay': timedelta(minutes=3), # 3분 기다렸다가 리트라이
}
```

```python
test_dag = DAG(
   "dag_v1", # DAG name, 웹 UI에 나옴, 유니크해야
   # schedule (same as cronjob)
   schedule_interval="0 9 * * *", # 매일 9시 0분에 실행하라, year는 생략 상태
   # common settings
   default_args=default_args  # timezone 파라미터는 설치 시 환경파일에 지정, 기본은 UTC
)
```

<img width="707" alt="스크린샷 2020-08-16 오후 11 28 33" src="https://user-images.githubusercontent.com/48748376/90336672-50f08b80-e018-11ea-9ae2-c1cd8a585345.png">

```python
t1 = BashOperator(
   task_id='print_date',
   bash_command='date',
   dag=test_dag)
t2 = BashOperator(
   task_id='sleep',
   bash_command='sleep 5',
   retries=3,
   dag=test_dag)
t3 = BashOperator(
   task_id='ls',
   bash_command='ls /tmp',
   dag=test_dag)
   
t1 >> t2 # t1이 끝나면 t2를 실행하라, t2.set_upstream(t1)
t1 >> t3 # t1이 끝나면 t3를 실행하라, t3.set_upstream(t1)
```

아래 설치 명령들은 우분투를 기반으로 한다.

http://www.marknagelberg.com/getting-started-with-airflow-using-docker/

## Docker 설치

먼저 Docker를 설치하기 위헤 apt 패키지 매니저 자체를 업데이트하고 설치를 진행한다.

```python
$ sudo apt-get update
$ sudo apt install -y docker.io
$ sudo systemctl start docker
$ sudo systemctl enable docker
$ docker --version
```

다음으로 hello-world를 실행하여 설치가 제대로 되었는지 확인한다. 출력문에 "Hello from Docker!"가 있어야 한다.

```python
$ sudo docker run hello-world
```

## Airflow 설치

먼저 dags 폴더를 하나 만든다. 이 폴더 아래 Python으로 만든 DAG 코드가 존재해야 한다.

```python
$ pwd
/home/ubuntu/
$ mkdir dags
$ ls -tl
total 4
drwxrwxr-x 2 ubuntu ubuntu 4096 Aug  3 02:43 dags
```

### Docker를 이용해 Airflow 설치

간단 설치 방법:

```python
$ sudo docker pull puckel/docker-airflow
```

Airflow가 설치된 Docker 이미지 이름을 보는 방법 (puckel/docker-airflow를 찾는다)

```python
$ sudo docker images 
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
puckel/docker-airflow   latest              ce92b0f4d1d5        5 months ago        797MB
hello-world             latest              bf756fb1ae65        7 months ago        13.3kB
```

더 자세히 설치하려면 https://medium.com/@xnuinside/quick-guide-how-to-run-apache-airflow-cluster-in-docker-compose-615eb8abd67a를 참고

### Airflow 실행

다음으로 이 폴더를 dags 폴더로 지정해서 Airflow를 실행한다.

```python
sudo docker run -d -p 8080:8080 -v /home/ubuntu/dags:/usr/local/airflow/dags puckel/docker-airflow webserver
```

### Airflow 웹서버 방문

http://호스트이름:8080/

![](images/airflow-docker.png)


## 기타 

### Airflow Container 실행 중단하기

먼저 Airflow Docker instance의 이름을 알아낸다. "sudo docker ps"를 명령을 실행하여 NAMES 컬럼밑에 나오는 이름을 기억한다. 여기서는 angry_wu인데 매번 바뀐다는 점에 유의한다.

```python
$ sudo docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                                        NAMES
a56dbb111b5b        puckel/docker-airflow   "/entrypoint.sh webs…"   21 minutes ago      Up 21 minutes       5555/tcp, 8793/tcp, 0.0.0.0:8080->8080/tcp   angry_wu
```

이 이름을 가지고 중단한다

```python
$ sudo docker stop angry_wu
```

### data-engineering repo내의 HelloWorld.py를 Airflow로 올리기

/home/ubuntu에서 아래를 실행

```python
$ git clone https://github.com/keeyong/data-engineering.git
```

다음으로 data-engineering/dags/HelloWorld.py를 /home/ubuntu/dags 폴더로 복사

```python
cp data-engineering/dags/HelloWorld.py /home/ubuntu/dags/
```

Airflow를 중단하기 위해 Docker Container의 이름을 알아낸다.

```python
$ sudo docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                                        NAMES
a56dbb111b5b        puckel/docker-airflow   "/entrypoint.sh webs…"   21 minutes ago      Up 21 minutes       5555/tcp, 8793/tcp, 0.0.0.0:8080->8080/tcp   angry_wu

$ sudo docker stop angry_wu
```

Airflow를 재실행한다.

```python
sudo docker run -d -p 8080:8080 -v /home/ubuntu/dags:/usr/local/airflow/dags puckel/docker-airflow webserver
```

### 이미 실행된 Airflow Docker Container를 같은 인자로 다시 재실행하고 싶다면 
awesome_shaw가 Container의 이름인 경우 아래와 같이 실행한다.

```python
sudo docker restart awesome_shaw
```

### Docker 내 Airflow DAG 폴더로 이동하기

이는 DAG 개발을 위해서 필요하다. 먼저 "sudo docker ps"를 명령을 실행하여 NAMES 컬럼밑에 나오는 이름을 기억한다. 

```python
$ sudo docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                                        NAMES
a56dbb111b5b        puckel/docker-airflow   "/entrypoint.sh webs…"   21 minutes ago      Up 21 minutes       5555/tcp, 8793/tcp, 0.0.0.0:8080->8080/tcp   angry_wu
```

이 이름을 가지고 "sudo docker exec -ti"으로 컨테이너 안으로 이동한다.

```python
$ sudo docker exec -ti angry_wu bash
airflow@a56dbb111b5b:~$ pwd
/usr/local/airflow
airflow@a56dbb111b5b:~$ cd dags
```

#### 개발

여기에서 ls -tl과 같은 명령을 실행해보면 HelloWorld.py를 볼 수 있어야 한다 (앞서 과정을 거쳤다면). 새로운 DAG 개발을 원한다면 여기서 파이썬 코드 파일을 만든다. 근데 Docker는 기본적으로 아무런 에디터 설치가 되어 있지 않다. 아래와 같이 vim을 설치해 사용한다:

```python
apt-get update
apt-get install vim
```

#### Airflow 명령 실행

여기서 여러가지 airflow 명령어들을 실행해볼 수 있다. 예를 들어 list_dags를 실행하면 현재 설치되어 있는 모든 DAG들이 리스트된다.

```python
airflow@a56dbb111b5b:~$ airflow list_dags
-------------------------------------------------------------------
DAGS
-------------------------------------------------------------------
example_bash_operator
example_branch_dop_operator_v3
example_branch_operator
example_complex
example_external_task_marker_child
example_external_task_marker_parent
example_http_operator
example_passing_params_via_test_command
example_pig_operator
example_python_operator
example_short_circuit_operator
example_skip_dag
example_subdag_operator
example_subdag_operator.section-1
example_subdag_operator.section-2
example_trigger_controller_dag
example_trigger_target_dag
example_xcom
latest_only
latest_only_with_trigger
test_utils
tutorial
my_first_dag
```

 - "airflow list_tasks DAG이름"을 실행하면 DAG에 속한 태스크들의 이름이 모두 나열된다. 
 - 특정 태스트를 실행하고 싶다면 예를 들어 task ID가 print_hello라면 "airflow test DAG이름 print_hello 2020-08-09" 이렇게 실행하면 된다. 여기서 주의할 점은 2020-08-09의 경우 DAG의 start_date보다는 뒤어야 하지만 현재 시간보다 미래이면 안된다.
 
#### Docker Container의 내용을 저장하기

- https://stackoverflow.com/questions/44480740/how-to-save-a-docker-container-state

## Airflow Deepdive

### 4주차 리뷰

- 변수 설정 기능
    - Connction 설정도 가능
- secret_key로 네이밍하면 저절로 암호화

<img width="1157" alt="스크린샷 2020-08-17 오전 10 16 26" src="https://user-images.githubusercontent.com/48748376/90348923-c5581880-e072-11ea-8c3d-144e1d4b6dc4.png">

- Docker 명령 리뷰

```python
sudo docker run -d -p 8080:8080 -v /home/ubuntu/dags:/usr/local/airflow/dags puckel/docker-airflow webserver
```

- /home/ubuntu/dags의 내용이 docker container내의 /usr/local/airflow/dags로 미러링이 됨
- 예를 들어 /home/ubuntu/dags에 있는 파일의 내용을 바꾸는대로 바로 반영이 됨

### Airflow Operation, Variables and Connections

#### DAG Configuration Example

- dag 인자를 딕셔너리롤 만들어서 할당

```python
from datetime import datetime, timedelta

from airflow import DAG
default_args = {
   'owner': 'keeyong',
   'start_date': datetime(2020, 8, 7, hour=0, minute=00),
   'end_date': datetime(2020, 8, 31, hour=23, minute=00),
   'email': ['keeyonghan@hotmail.com'],
   'retries': 1,
   'retry_delay': timedelta(minutes=3),
}

dag = DAG(
   "dag_v1", # DAG name
   # schedule (same as cronjob)
   schedule_interval="0 9 * * *", 
   # common settings
   default_args=default_args 
)
```

- 주의할 점
- Note that Airflow will run the first job at start_date + interval.
    - start_date에 바로 시작이 아니라 start_date + interval에 처음 시작 
    - A daily dag with the start_date '2020-07-26 01:00' starts at 2020-07-27 01:00.
- Start_date 이해하기(catchup 설정은 디폴트가 true)
    - 2020-08-10 02:00:00로 start date로 설정된 daily job이 있다
        - catchup이 True로 설정되어 있다고 가정 (디폴트가 True)
    - 지금 시간이 2020-08-13 20:00:00이고 처음으로 이 job이 활성화되었다
        - 질문: 이 경우 이 job은 몇번 실행될까?
            - 2020-08-10 02:00:00 -> 2020 08 11 실행
            - 2020-08-11 02:00:00 -> 2020 08 12 실행
            - 2020-08-12 02:00:00 -> 2020 08 13 실행
            - 2020-08-13 02:00:00 -> 2020 08 14 실행

#### Important DAG parameters (not task parameters)

- 꼭 DAG의 파라미터로 써줘야 한다
    - max_active_runs: # of DAGs instance
        - 실행할 DAG의 수
    - concurrency: # of tasks that can run in parallel 
    - catchup # whether to backfill past runs
        - 이전 시점 실행 여부, 디폴트가 true

#### Operators - PythonOperator

- 파라미터를 넘기려면 `provide_context = True`
    - params 딕셔너리 넘겨야
    - excution_date는 start_date와 같이 실행 시간, 실제 실행 시점은 interval을 더해야

```python
load_nps = PythonOperator(
   dag=dag,
   task_id='task_id',
   python_callable=python_func,
   params={
       'table': 'delighted_nps',
       'schema': 'raw_data'
   },
   provide_context=True
)

from airflow.exceptions import AirflowException

def python_func(**cxt):
  table = cxt["params"]["table"]
  schema = cxt["params"]["schema"]
  ex_date = cxt["execution_date"]

  # do what you need to do
  ...
```

#### How to Trigger DAGs

- 스케쥴과 상관 없이 UI에서 Trigger

<img width="859" alt="1" src="https://user-images.githubusercontent.com/48748376/90352499-8b8d0f00-e07e-11ea-8fe3-83f6c109dadb.png">


- How to Trigger a DAG - From Terminal
- Airflow 도커 컨테이너에 접근한 다음
    - airflow list_dags
    - airflow list_tasks (DAG이름)
    - airflow test DAG이름 Task이름 날짜
        - 날짜는 YYYY-MM-DD
            - start_date보다 과거이거나 오늘 날짜보다 미래인 경우 실행 안됨
    - airflow test second_assignment_v3 extract 2020-08-09

#### Instructions to follow

- Airflow 컨테이너가 실행중이라면 일단 중단. 컨테이너 이름을 찾아 아래 명령 실행

```python
$ sudo docker stop (컨테이너이름)
```

- keeyong/data-engineering repo를 Airflow 서버에서 다운로드
    - 이미 다운받았다면 data-engineering 폴더안에서 git pull 실행

```python
$ git clone https://github.com/keeyong/data-engineering
```

- 이 repo의 dags 폴더를 가지고 Docker container를 실행
- Docker run 할때 —name [이름] -> 이름 지정 가능

```python
$ sudo docker run -d -p 8080:8080 -v /home/ubuntu/data-engineering/dags:/usr/local/airflow/dags puckel/docker-airflow webserver
```

```python
$ sudo docker run --name yongjunlee -it -d -p 8080:8080 -v /home/ubuntu/data-engineering/dags:/usr/local/airflow/dags puckel/docker-airflow webserver
```


- 해당 docker  container의 이름을 찾아서 container안으로 이동
- 컨테이너 이름 찾기

```python
$ sudo docker ps
```

- 컨테이너 안으로 이동하기

```python
$ sudo docker exec -ti (컨테이너이름) bash
```

- 여기서 필요한 2개의 파이썬 모듈을 설치

```python
$ pip install botocore
$ pip install boto3
```

- 컨테이너를 빠져나와서 컨테이너를 재시작

```python
$ sudo docker restart (컨테이너이름)
```

- Airflow 웹서버 접근: http://(airflow서버이름):8080/

<img width="935" alt="1" src="https://user-images.githubusercontent.com/48748376/90355711-2807df00-e088-11ea-8e5a-d32da18b164b.png">


<img width="939" alt="2" src="https://user-images.githubusercontent.com/48748376/90355710-276f4880-e088-11ea-82dd-d11a56590736.png">

<img width="908" alt="3" src="https://user-images.githubusercontent.com/48748376/90355706-25a58500-e088-11ea-90f7-4471eb8d86a1.png">

- Variables
    - csv_url = https://s3-geospatial.s3-us-west-2.amazonaws.com/name_gender.csv
    - iam_role_for_copy_access_token = arn:aws:iam::080705373126:role/redshift.read.s3

- Redshift 테이블 생성

```python
CREATE TABLE (본인스키마).customer_variants (
    customer_id int,
    variant varchar(8),
    primary key (customer_id)
);
CREATE TABLE (본인스키마).customer_interactions (
    customer_id int,
    datestamp date,
    item_id int,
    clicked int,
    purchased int ,
    paidamount int,
    primary key(customer_id, datestamp, item_id, clicked,purchased)
);

CREATE TABLE (본인스키마).item_features (
    item_id int,
    quality float,
    popularity varchar(16),
    price int,
    primary key(item_id)
);
CREATE TABLE (본인스키마).customer_features (
      customer_id int,
     engagement_level varchar(16),
     gender varchar(16),
     PRIMARY KEY (customer_id)
);
```


## Link

- [실리콘밸리에서 날아온 데이터 엔지니어링 with Python](https://programmers.co.kr/learn/courses/10505)
