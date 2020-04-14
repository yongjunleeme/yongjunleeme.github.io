---
layout  : wiki
title   : 
summary : 
date    : 2020-04-13 22:36:44 +0900
updated : 2020-04-13 23:25:07 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## DBaver


### 

- 콘트롤 + 쉬프트 + e -> 실행계획 조회

- alias
    - SELECT a.first_name FROM customer a

### order by

```SQL
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

```SQL
CREATE TABLE T1 ( ID SERIAL NOT NULL PRIMARY KEY, BCOLOR VARCHAR, FCOLOR VASRCHAR );

"CREATE => DDL -> 입력 시 바로 적용

INSERT
    INTO T1 (BCOLOR, FCOLOR)
VALUES
              ('red', 'red')
            , ('red', 'red')
            , ('red', NULL)
            , (NULL, 'red')
 ;
 
 COMMIT; 
"INSERT 후 커밋

```

```SQL
SELECT
    DISTICT BCOLOR
FROM
    T1
ORDER BY
    BCOLOR
;
```

#### DISTICT ON

```SQL
SELECT
    DISTICT ON (BCOLOR) BCOLOR
  , FCOLOR
FROM
    T1
ORDER BY
    BCOLOR, FCOLOR;
```

- BCOLOR 기준으로 DISTICT

