---
layout  : wiki
title   : 
summary : 
date    : 2020-04-20 21:31:38 +0900
updated : 2020-05-04 21:52:27 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

![Visual_SQL_JOINS_orig](https://user-images.githubusercontent.com/48748376/80087912-c088c980-8596-11ea-9d04-4030331f8f00.jpg)

## JOIN

- INNER 조인 - 특정 컬럼을 기준으로 저오학히 매칭된 집합을 출력
- OUTER 조인 - 특정 컬럼을 기준으로 매칭된 집합을 출력하지만 한쪽 집합은 모두 출력하고 다른 한쪽의 집합은 매칭되는 컬럼의 값만을 출력
- SELF 조인 - 동일한 테이블끼리 특정 컬럼을 기준으로 매칭되는 집합을 출력
- FULL OUTER 조인 - INNER, LEFT OUTER, RIGHT OUTER 조인 집합을 모두 출력
- CROSS 조인
    - 가능한 경우의 수 모두 출력?
    - Catesian Product라고도 하며 조인되는 두 테이블에서 곱집합을 반환
    - 데이터 복제에 많이 쓰이는 기법
- NATURAL 조인 - 특정 테이블의 같은 이름을 가진 컬럼 ₩간의 조인집합을 출력


```python
CREATE TABLE BASKET_A 
(
   ID INT PRIMARY KEY
 , FRUIT VARCHAR (100) NOT NULL
);

SELECT * FROM BASKET_A; --공집합 
------------------------------------------------------------------------------
CREATE TABLE BASKET_B 
(
   ID INT PRIMARY KEY
 , FRUIT VARCHAR (100) NOT NULL
);

SELECT * FROM BASKET_B; 
-------------------------------------------
INSERT INTO BASKET_A (ID, FRUIT)
VALUES
(1, 'Apple'),
(2, 'Orange'),
(3, 'Banana'),
(4, 'Cucumber')
;
--INSERT, UPDATE, DELETE -> 데이터의 삽입 및 갱신 -> COMMIT; , ROLLBACK; -> 트랜잭션 처리 
COMMIT; 
SELECT * FROM BASKET_A;
----------------------------------------
INSERT INTO BASKET_B (ID, FRUIT)
VALUES
(1, 'Orange'),
(2, 'Apple'),
(3, 'Watermelon'),
(4, 'Pear')
;
COMMIT; 
SELECT * FROM BASKET_B; 
-------------------------------------------
SELECT * FROM BASKET_A; 
------------------------------------------
SELECT * FROM BASKET_B; 
```

### INNER JOIN

```python
SELECT
       A.ID ID_A
     , A.FRUIT FRUIT_A
     , B.ID ID_B
     , B.FRUIT FRUIT_B
  FROM BASKET_A A
INNER JOIN BASKET_B B 
   ON A.FRUIT = B.FRUIT;
--------------------------------------------------------
SELECT
       A.CUSTOMER_ID, A.FIRST_NAME
     , A.LAST_NAME, A.EMAIL
     , B.AMOUNT, B.PAYMENT_DATE
  FROM CUSTOMER A 
 INNER JOIN PAYMENT B 
ON A.CUSTOMER_ID = B.CUSTOMER_ID;

--고객 여러건의 결제를 할수있다. 고객1:결제M -> 1:M 관계 된다. 
SELECT count(*) FROM PAYMENT; 
--------------------------------------------------------
SELECT
       A.CUSTOMER_ID, A.FIRST_NAME
     , A.LAST_NAME, A.EMAIL
     , B.AMOUNT, B.PAYMENT_DATE
  FROM CUSTOMER A 
INNER JOIN PAYMENT B 
ON A.CUSTOMER_ID = B.CUSTOMER_ID
WHERE A.CUSTOMER_ID = 4;
--------------------------------------------------------
SELECT
       A.CUSTOMER_ID, A.FIRST_NAME
     , A.LAST_NAME, A.EMAIL
     , B.AMOUNT, B.PAYMENT_DATE
     , C.FIRST_NAME AS S_FIRST_NAME 
     , C.LAST_NAME AS S_LAST_NAME
  FROM CUSTOMER A 
INNER JOIN PAYMENT B 
ON A.CUSTOMER_ID = B.CUSTOMER_ID
INNER JOIN STAFF C 
ON B.STAFF_ID = C.STAFF_ID;
--고객1:결제m:직원1
```

### OUTER JOIN

```python
SELECT
       A.ID    AS ID_A
     , A.FRUIT AS FRUIT_A
     , B.ID    AS ID_B
     , B.FRUIT AS FRUIT_B
  FROM
       BASKET_A A LEFT OUTER JOIN BASKET_B B 
  ON A.FRUIT = B.FRUIT;
----------------------------------------
 SELECT
      A.ID    AS ID_A
    , A.FRUIT AS FRUIT_A
    , B.ID    AS ID_B
    , B.FRUIT AS FRUIT_B
 FROM
      BASKET_A A
LEFT OUTER JOIN BASKET_B B 
  ON A.FRUIT = B.FRUIT
WHERE B.ID IS NULL;
--LEFT ONLY 
--------------------------------------------
SELECT
       A.ID    AS ID_A
     , A.FRUIT AS FRUIT_A
     , B.ID    AS ID_B
     , B.FRUIT AS FRUIT_B
  FROM
       BASKET_A A
RIGHT OUTER JOIN BASKET_B B 
  ON A.FRUIT = B.FRUIT;
----------------------------------------------
 SELECT
       A.ID AS ID_A
     , A.FRUIT AS FRUIT_A
     , B.ID AS ID_B
     , B.FRUIT AS FRUIT_B
  FROM
       BASKET_A A
RIGHT OUTER JOIN BASKET_B B 
  ON A.FRUIT = B.FRUIT
WHERE A.ID IS NULL;
```

### SELF JOIN

```python
CREATE TABLE EMPLOYEE 
(
  EMPLOYEE_ID INT PRIMARY KEY
, FIRST_NAME VARCHAR (255) NOT NULL
, LAST_NAME VARCHAR (255) NOT NULL
, MANAGER_ID INT --관리자
, FOREIGN KEY (MANAGER_ID) 
  REFERENCES EMPLOYEE (EMPLOYEE_ID) 
  ON DELETE CASCADE
);
-----------------------------------------
INSERT INTO EMPLOYEE (
  EMPLOYEE_ID
, FIRST_NAME
, LAST_NAME
, MANAGER_ID
)
VALUES
(1, 'Windy', 'Hays', NULL),
(2, 'Ava', 'Christensen', 1),
(3, 'Hassan', 'Conner', 1),
(4, 'Anna', 'Reeves', 2),
(5, 'Sau', 'Norman', 2),
(6, 'Kelsie', 'Hays', 3),
(7, 'Tory', 'Goff', 3),
(8, 'Salley', 'Lester', 3);

COMMIT; 
----------------------------------------
SELECT * FROM EMPLOYEE; 

SELECT
  E.FIRST_NAME || ' ' || E.LAST_NAME EMPLOYEE
, M.FIRST_NAME || ' ' || M .LAST_NAME MANAGER
FROM
     EMPLOYEE E
INNER JOIN EMPLOYEE M 
ON M .EMPLOYEE_ID = E.MANAGER_ID
ORDER BY MANAGER; 

SELECT
  E.FIRST_NAME || ' ' || E.LAST_NAME EMPLOYEE
, M.FIRST_NAME || ' ' || M .LAST_NAME MANAGER
FROM
     EMPLOYEE E
LEFT OUTER JOIN EMPLOYEE M 
ON M .EMPLOYEE_ID = E.MANAGER_ID
ORDER BY MANAGER;
-------------------------------------------------
SELECT
       F1.TITLE
     , F2.TITLE
     , F1.LENGTH
  FROM
       FILM F1
INNER JOIN FILM F2 
ON F1.FILM_ID <> F2.FILM_ID
AND F1. LENGTH = F2.LENGTH;
-----------------------------------------
SELECT * 
FROM film f1 --테이블을 조회 
WHERE f1.LENGTH = f1.length  
AND F1.FILM_ID <> f1.FILM_ID
;
```


### Cross Join

```python
CREATE TABLE CROSS_T1
(
  LABEL CHAR(1) PRIMARY KEY
);

CREATE TABLE CROSS_T2 
(
  SCORE INT PRIMARY KEY
);


INSERT INTO CROSS_T1 (LABEL)
VALUES
('A'),
('B');

COMMIT; 

SELECT * FROM CROSS_T1; 

INSERT INTO CROSS_T2 (SCORE)
VALUES
(1),
(2),
(3);

COMMIT;

SELECT * FROM CROSS_T2;

------------------------------------------------------
SELECT
      *
 FROM CROSS_T1
CROSS JOIN 
      CROSS_T2
ORDER BY LABEL       
;

SELECT * 
FROM CROSS_T1, CROSS_T2
ORDER BY LABEL 
; --inner 조인을 표현하는 다른방법 

--위 2개의  sql문 결과 집합이 동일하죠~ -> 같은 sql문입니다. 
--sql문의 목적이 집합을 출력하는 것이다. -> 정보는 ~ -> 정보가 같다면 sql문 자체는 다르더라도~ 동일한 sql문이다. 

SELECT LABEL, 
      CASE WHEN LABEL = 'A' THEN sum(score)  
           WHEN LABEL = 'B' THEN sum(score) * -1
           ELSE 0 
           END AS calc
 FROM CROSS_T1
CROSS JOIN 
      CROSS_T2
GROUP BY LABEL      
ORDER BY LABEL    
```
