---
layout  : wiki
title   : 
summary : 
date    : 2020-04-13 22:36:44 +0900
updated : 2020-04-14 23:25:41 +0900
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

- alias
    - SELECT a.first_name FROM customer a

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
        DISTICT BCOLOR
FROM
        T1
ORDER BY
        BCOLOR;
```

#### DISTICT ON

```python
SELECT
    DISTICT ON (BCOLOR) BCOLOR
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


