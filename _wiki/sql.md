---
layout  : wiki
title   : 
summary : 
date    : 2020-04-13 22:36:44 +0900
updated : 2020-04-20 21:10:24 +0900
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
SHOW COLUMNS FROM table_name


```

```python
"pament 테이블에서 단일 거래의 amount 액수가 가장 많은 고객들의 customer_id 추출, 단 customer_id 값은 유일해야 한다"

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

중복값 제거

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
"INSERT 후 커밋

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

###

```python
--PAYMENT 테이블에서 단일 거래의 AMOUNT의 액수가 가장 많은 고객들의 CUSTOMER_ID를 추출하라. 
--단, CUSTOMER_ID의 값은 유일해야 한다.

SELECT 
	   DISTINCT A.CUSTOMER_ID 
  FROM PAYMENT A
 WHERE A.AMOUNT = 
	(
		SELECT 
				K.AMOUNT 
		FROM PAYMENT K
	        ORDER BY K.AMOUNT DESC
		LIMIT 1 --LIMIT절
				)
;

--실무 -> 한국말 -> sql작성 -> 정답 -> 실력 안늘죠
--정답 -> 어떻게든 해결하자 입니다. 

```
