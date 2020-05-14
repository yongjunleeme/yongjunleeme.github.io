---
layout  : wiki
title   : 
summary : 
date    : 2020-04-13 22:36:44 +0900
updated : 2020-05-14 21:18:43 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## SQL 


### DBaver

- 콘트롤 + 쉬프트 + e -> 실행계획 조회
- 콘트롤 + 쉬프트 + f -> 자동정
- 콘트롤 + 쉬프트 + x -> 대문자로 변경
- alias
    - SELECT a.first_name FROM customer a

### Basic

```python
"컬럼 목록 출력"
SHOW COLUMNS FROM table_name;

"order by"
SELECT ID, NAME FROM CUSTOMER ORDER BY NAME ASC;

"distinct"
SELECT DISTINCT NAME FROM CUSTOMER;

"distinct on" - postgres만 가능 - on 컬럼 기준으로 
SELECT DISTINCT ON NAME, COLOR FROM CUSTOMER; 

"where"
SELECT LAST_NAME, FIRST_NAME FROM CUSTOMER WHERE FIRST_NAME = 'JAMIE' AND LAST_NAME = 'RICE';

"limit" - 출력 행의 수 한정 "offset" - 시작위치 지정
SELECT FILM_ID FROM FILM LIMIT 4 OFFSET 3;

"in" - 존재여부 판단
SELECT CUSTOMER_ID, RENTAL_ID FROM RENTAL WHERE CUSTOMER_ID IN (1, 2); "OR 조건"

SELECT CUSTOMER_ID, RENTAL_ID FROM RENTAL WHERE CUSTOMER_ID NOT IN (1, 2); "AND 조건"

"between"
SELECT CUSTOMER_ID, PAYMENT_ID FROM PAYMENT WHERE AMOUT 8 AND 9;

"like" - 패턴 검색, % -> 모든 문자, _ -> 문자 1개
SELECT FIRST_NAME FROM CUSTOMER WHERE FIRST_NAME LIKE '_HER%';

"is null" - null값 여부 판단
SELECT ID, FIRST_NAME FROM CONTACTS WHERE FIRST_NAME IS NULL;
```

```python
select sum(salary) from salaries where playerid = 'rodrial01'

"self join" - 같은 필드(컬럼) 내 다른 필드(로우)들을 조인 
select nameFirst || ' ' || nameLast from master limit 10;

select count(distinct(nameFirst || ' ' || nameLast)) from master;

select nameFirst || ' ' || nameLast as name, count(*) from master group by name having count(*) > 1

select teamID, SUM(salary) AS total_salary FROM salaries group by teamID order by SUM(salary) DESC
```

### JOIN

<img width="931" alt="sql join" src="https://user-images.githubusercontent.com/48748376/81392400-91a75180-9159-11ea-913a-c1f2a7ead442.png">

```python
"join"
SELECT
    t2.nameFirst || ' ' || t2.namaLast As name,
    t1.salary
FROM
    Salaries t1
JOIN
    Master t2 ON t2.playerID = t1.playerID
ORDER  BY salary DESC
LIMIT 20;

"left join"
select
    count(distinct t1.playerid)
from
    master t1
left join |
    allstarfull on t2.playerid = t1.playerid
    

select
    count(distinct t1.playerid)
from
    master t1
left join
    allstarfull on t2.playerid = t1.playerid
where t2.playerid is null
```

### create, insert, update, replace

```python
create table mytable (id int, name varchar(255), debut date);

create table mytable2 (id integer primary key autoincrement, name varchar(255), debut date);

insert into mytable2 (name, debut) values ('yongjun', '2020-05-08');

update mytable2 set debut = '2010-09-01' where id = 1234;

replace into mytable2 (id, name, debut) values (1234, 'yongjun', '2020-05-08');

insert or ignore into mytable2 (id, name, debut) values (1, 'yongjun', '2020-05-08');

```

### delete, alter, drop

```python
delete from mytable2 where id=1;

alter table rename to players;
alter table add column dob date;

drop table mytable;
```

### create

```python
mysql> CREATE TABLE artists(id VARCHAR(255), name VARCHAR(255), followers INTEGER, popularity INTEGER, url VARCHAR(255), image_url VARCHAR(255), PRIMARY KEY(id)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

" 이모티콘포함 -> CHARSET='utf8mb4' COLLATE 'utf8mb4_unicode_ci'

mysql> CREATE TABLE artist_genres (artist_id VARCHAR(255), genre VARCHAR(255)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

mysql> show create table artists;
```

### insert

```python
mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');

mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');
```

### delete

```python
mysql> DELETE FROM artist_genres;

mysql> DROP TABLE artist_genres;
```

### update

```python
mysql> CREATE TABLE artist_genres (artist_id VARCHAR(255), genre VARCHAR(255), UNIQUE KEY(artist_id, genre)) ENGINE=InnoDB DEFAULT CHARSET='utf8';
```

- 같은 id의 값들을 넣어도 1개러 업데이트된다
- 그러나 모든 정보를 알고 있어야 해서 잘 안 쓴다?

```python
mysql>UPDATE artist_genres SET genre='pop' where artist_id = '1234';
```

### replace

- 컬럼추가

```python
mysql>ALTER TABLE artist_genres ADD COLUMN country VARCHAR(255);
```

- 업데이트 시간 자동 업데이트
    - 디폴트가 현재 시간이고 업데이트될 떄마다 현재 시간으로 업데이트 한다

```python
mysql>ALTER TABLE artist_genres ADD COLUMN updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE_TIMESTAMP;
```

- insert 
  
```python
INSERT INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'pop', 'UK');
```

- replace - 기존 데이터에 없으면 생성, 있으면 지운 다음 생성
    - 단점
        - 1지우고 2다시 생성 2스텝
        - id가 변할 수 있다

```python
mysql>REPLACE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'pop', 'UK');
```

### insert ignore

- 기존 데이터에 있으면 무시

```python
mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'UK');

mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR');
```

### duplicate key update

- 지우지 않고 업데이트
- 거의 이것만 쓴다.

```python
mysql>INSERT INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR') ON DUPLICATE KEY UPDATE artist_id='1234', genre='rock', country='FR';
```

### id autoincrement

```python
mysql>id primary_key auto_increment
```


### sql function

```python
select substr(name, 1, 4) from players2
select upper(name) from players
select length(name) from players

max, avg, count, sum

--시간 관련

select date('now')
select date('current_timestamp')

select datetime('now')

- 고정 시간 변경( 데이터베이스마다 문법 상이 )
select datetime(current_timestmap, '+1 day')

mysql -> select current_timestamp - interval '7 days'

select
  case when
    name = 'yongjun' then 'ok'
  when name = 'yongpal' then 'ok2'
  else 'no ok'
  end as name_ok
from
  players2
```


## 예제

```python
"payment 테이블에서 단일 거래의 amount 액수가 가장 많은 고객들의 customer_id 추출, 단 customer_id 값은 유일해야 한다"

SELECT
       DISTINCT A.CUSTOMER_ID
  FROM PAYMENT A
  WHERE A.AMOUNT =  "한개의 값과 비교하므로 등호 씀"
            (
             SELECT
                    K.AMOUNT
               FROM PAYMENT K
            ORDER BY K.AMOUNT DESC
                LIMIT 1
            )
```


### order by

```python
    SELECT
            first_name, last_name
      FROM
            customer
  ORDER BY
            FIRST_NAME ASC
    "ASC -> 오름차순 a, b, c .. 순서
    
    SELECT
            FIRST_NAME, LAST_NAME
      FROM
            CUSTOMER
  ORDER BY
            1 ASC, 2 DESC
    "숫자를 변수처럼 활용 가능
    "그러나 가독성이 좋지 않음
```

### DISTINCT

- 중복값 제거

```python
CREATE TABLE T1 ( ID SERIAL NOT NULL PRIMARY KEY, BCOLOR VARCHAR, FCOLOR VASRCHAR );

"CREATE => DDL -> 입력 시 바로 적용

INSERT
        INTO T1 (BCOLOR, FCOLOR)
VALUES
        ('red', 'red')
        , ('red', 'red')
        , ('red', NULL)
        , (NULL, 'red');

COMMIT; 
```

```python
SELECT
        DISTINCT BCOLOR
FROM
        T1
ORDER BY
        BCOLOR;
```

#### DISTINCT ON

```python
SELECT
    DISTINCT ON (BCOLOR) BCOLOR
  , FCOLOR
FROM
    T1
ORDER BY
    BCOLOR, FCOLOR;
```

- BCOLOR 기준으로 DISTICT

### WHERE

- 조건

```python
SELECT
    LAST_NAME,
    FIRST_NAME
FROM
    CUSTOMER
WHERE
    FIRST_NAME = 'Jamie'
AND LAST_NAME  = 'Rice'
```

### LIMIT

- 출력하는 행의 수 한정

```python
SELECT
       *
FROM
       TABLE_NAME
LIMIT  N
```
- OFFSET -> 시작위치 지정

```python
SELECT
        FILM_ID
FROM
        FILM
LIMIT   4
OFFSET  3;  "3은 0, 1, 2, 3 즉, 4번째 행부터 시작
```

### FETCH

-  출력하는 행의 수를 한정. 부분 범위 처리 시사용

```python
SELECT
      FILM_ID
	, TITLE
 FROM
	  FILM
ORDER BY TITLE 
FETCH FIRST [N] ROW ONLY  "출력하는 행의 수를 지정한다. N을 입력하지 않고 ROW ONLY만 입력하면 한 건만 출력
;

--------------------------------------------------
SELECT
       FILM_ID
     , TITLE
  FROM
       FILM
ORDER BY TITLE 
FETCH FIRST 1 ROW ONLY
;
----------------------------------------------------
SELECT
        FILM_ID
      , TITLE
  FROM
        FILM
ORDER BY TITLE 
     OFFSET 5 ROWS "출력하는 시작위치 지정"
FETCH FIRST 5 ROW ONLY
```

### IN

- 특정 집합(컬럼 혹은 리스트)에서 특정 집합 혹은 리스트가 존재하는지 판단하는 연산자

```python
SELECT
       CUSTOMER_ID
     , RENTAL_ID
     , RETURN_DATE
  FROM RENTAL
 WHERE
       CUSTOMER_ID IN (1, 2)  "CUSTOMER_ID가 1 혹은 1인 행을 출력 (OR 조건)"
ORDER BY RETURN_DATE DESC;
--------------------------------------
SELECT
       CUSTOMER_ID
     , RENTAL_ID
     , RETURN_DATE
  FROM RENTAL
 WHERE
          CUSTOMER_ID = 1 
       OR CUSTOMER_ID = 2  "OR보다 위에 있는IN을 쓰는 게 가독성 더 좋다"
ORDER BY RETURN_DATE DESC;
---------------------------------------
SELECT
       CUSTOMER_ID
     , RENTAL_ID
     , RETURN_DATE
  FROM RENTAL
 WHERE
       CUSTOMER_ID NOT IN (1, 2) "1도 2도 아닌 행을 출력. 1, 2를 제외한 모든 행 출력"
ORDER BY RETURN_DATE DESC;
---------------------------------------
SELECT
      CUSTOMER_ID
     , RENTAL_ID
     , RETURN_DATE
  FROM RENTAL
 WHERE
       CUSTOMER_ID <> 1   "앤드 조건, 가독성 in보다 좋지 않아"
   AND CUSTOMER_ID <> 2
ORDER BY RETURN_DATE DESC;
----------------------------------------------------
SELECT
             CUSTOMER_ID
FROM
             RENTAL
WHERE
    CAST (RETURN_DATE AS DATE) = '2005-05-27';
-------------------------------------------------------
SELECT
      FIRST_NAME
    , LAST_NAME
 FROM CUSTOMER
WHERE CUSTOMER_ID IN ( "RETURN_DATE가 2005년 5월 27일인 CUSTOMER_ID의 FIRST_DATE, LAST_DATE를 출력"
	SELECT
		CUSTOMER_ID
	FROM
		RENTAL
	WHERE
		CAST (RETURN_DATE AS DATE) = '2005-05-27' 
       				);
```

### BETWEEN

- 특정 집합에서 어떠한 컬럼의 값이 특정 범위 안에 들어가는 집합을 출력하는 연산자

```python
 SELECT
        CUSTOMER_ID
      , PAYMENT_ID
      , AMOUNT
   FROM
        PAYMENT
  WHERE AMOUNT BETWEEN 8 AND 9;
-----------------------------------------------

  SELECT
        CUSTOMER_ID
      , PAYMENT_ID
      , AMOUNT
   FROM
        PAYMENT
WHERE amount >= 8 
AND amount <= 9
;
-------------------------------------------------------
SELECT
        CUSTOMER_ID
      , PAYMENT_ID
      , AMOUNT
   FROM
        PAYMENT
  WHERE AMOUNT NOT BETWEEN 8 AND 9;
---------------------------------------------------------- 
 SELECT
        CUSTOMER_ID
      , PAYMENT_ID
      , AMOUNT
   FROM
        PAYMENT
  WHERE AMOUNT < 8 OR amount > 9;
-------------------------------------------------
SELECT
        CUSTOMER_ID, PAYMENT_ID
      , AMOUNT         , PAYMENT_DATE
  FROM PAYMENT
 WHERE CAST(PAYMENT_DATE AS DATE) BETWEEN '2007-02-07' AND '2007-02-15';
---------------------------------------------------

SELECT
        CUSTOMER_ID, PAYMENT_ID
      , AMOUNT, PAYMENT_DATE, to_char(PAYMENT_DATE, 'yyyy-mm-dd')
      , CAST(PAYMENT_DATE AS DATE)
  FROM PAYMENT
  WHERE to_char(PAYMENT_DATE, 'yyyy-mm-dd') BETWEEN '2007-02-07' AND '2007-02-15';
```

### Like

- 컬럼 값이 특정 값과 유사한 패턴을 갖는 집합을 출력하는 연산자.
- `%` - 무엇이든 매칭
- `_` - 한 개 문자 매칭


```python
SELECT
      FIRST_NAME
    , LAST_NAME
 FROM
      CUSTOMER
WHERE
    FIRST_NAME LIKE 'Jen%';
   ------------------------------------------------
SELECT
	    'FOO' LIKE 'FOO'
	  , 'FOO' LIKE 'F%'
      , 'FOO' LIKE '_O_'
	  , 'BAR' LIKE 'B_'
;
-----------------------------------------------
SELECT
      FIRST_NAME
    , LAST_NAME
 FROM
      CUSTOMER
WHERE
    FIRST_NAME LIKE '%er%';
----------------------------------------------
   SELECT
      FIRST_NAME
    , LAST_NAME
 FROM
      CUSTOMER
WHERE
    FIRST_NAME LIKE '_her%';
----------------------------------------------
   SELECT
      FIRST_NAME
    , LAST_NAME
 FROM
      CUSTOMER
WHERE
    FIRST_NAME NOT LIKE 'Jen%';

```

### IS NULL

- 특정 컬럼 혹은 값이 널값인지 판단하는 연산자

```python
CREATE TABLE CONTACTS 
(
    ID INT GENERATED BY DEFAULT AS IDENTITY
  , FIRST_NAME VARCHAR(50) NOT NULL
  , LAST_NAME VARCHAR(50) NOT NULL
  , EMAIL VARCHAR(255) NOT NULL
  , PHONE VARCHAR(15)
  , PRIMARY KEY (ID) 
);

 INSERT 
   INTO 
   CONTACTS(FIRST_NAME, LAST_NAME, EMAIL, PHONE) 
 VALUES
   ('John','Doe','john.doe@example.com',NULL),
       ('Lily','Bush','lily.bush@example.com','(408-234-2764)');

COMMIT;       
      
SELECT
	*
FROM
	CONTACTS;
-----------------------------------------------
SELECT
       ID
     , FIRST_NAME
     , LAST_NAME
     , EMAIL
     , PHONE
  FROM
       CONTACTS
 WHERE PHONE = NULL;
-----------------------------------------------
 SELECT
       ID
     , FIRST_NAME
     , LAST_NAME
     , EMAIL
     , PHONE
  FROM
       CONTACTS
WHERE PHONE IS NULL;
----------------------------------------------------
SELECT
	*
FROM
	CONTACTS;

-----------------------------------------------
SELECT
       ID
     , FIRST_NAME
     , LAST_NAME
     , EMAIL
     , PHONE
  FROM
       CONTACTS
WHERE PHONE IS NOT NULL;
```

