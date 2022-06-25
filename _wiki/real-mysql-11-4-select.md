---
layout  : wiki
title   : 
summary : 
date    : 2022-06-25 21:53:49 +0900
updated : 2022-06-25 21:57:11 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 11.4 SELECT

### 11.4.1 SELECT 절의 처리 순서

SELECT 문장은 SQL 전체를 SELECT 절은 SELECT 키워드와 실제 가져올 칼럼을 명시한 부분을 의미한다.

```sql
SELCT s.emp_no, COUNT(DISTINCT e.first_name) AS cnt
FROM salaries s INNER JOIN employees e ON e.emp_no.emp_no;
```

- SELECT 절 : `SELCT s.emp_no, COUNT(DISTINCT e.first_name) AS cnt`
- FROM 절 : `FROM salaries s INNER JOIN employees e ON e.emp_no.emp_no;`

#### 각 쿼리절 실행 순서

드라이빙 테이블, 드리븐 테이블(WHERE 적용 및 조인 실행) --> GROUP BY --> DISTINCT -> HAVING 조건 적용 --> ORDER BY --> LIMIT

예외적으로 ORDER BY가 조인보다 먼저 실행되는 경우

드라이빙 테이블(WHERE 적용) --> ORDER BY --> 드리븐 테이블(조인 실행) --> LIMIT

WITH 절(CTE, Common Table Expression)은 항상 제일 먼저 실행되어 임시 테이블로 저장된다. 

### 11.4.2 WHERE 절과 GROUP BY 절, ORDER BY 절의 인덱스 사용
#### 11.4.2.1 인덱스를 사용하기 위한 기본 규칙
WHERE 절과 GROUP BY, ORDER BY가 인덱스를 사용하려면 기본적으로 인덱스된 칼럼의 값 자체를 변환하지 않고 그대로 사용해야 한다. 인덱스는 칼럼의 값을 아무런 변환 없이 B-Tree에 정렬해서 저장한다.

WHERE 조건이나 GROUP BY 또는 OBDER BY에서도 원본값을 검색하거나 정렬할 때만 B-Tree에 정렬된 인덱스를 이용한다. 즉, 인덱스는 salary 칼럼으로 만들어져 있는데, 다음 예제의 WHERE 절과 같이 salary 칼럼을 가공한 후 다른 상숫값과 비교한다면 이 쿼리는 인덱스를 적절히 이용하지 못한다.

```sql
SELECT * FROM salaries WHERE salary * 10 > 150000;
```

#### 11.4.2.2 WHERE 절의 인덱스 사용

WHERE 조건이 인덱스를 사용하는 방법은 작업 범위 결정 조건과 체크 조건으로 구분할 수 있다.

작업 범위 결정 조건은 WHERE 절에서 동등 비교 조건이나 IN으로 구성된 조건에 사용된 칼럼들이 인덱스의 칼럼 구성과 좌측에서부터 비교했을 때 얼마나 일치하는가에 따라 달라진다.

WHERE 조건절의 순서에 나열된 조건들의 순서는 실제 인덱스 사용 여부와 무관하다.

COL_1과 COL_2가 동등 비교 조건(COL_1 =?, COL_2 =?)이며 COL_3의 조건이 크다 작다와 같은 범위 비교 조건(COL_3 > ?)이면 뒤 칼럼인 COL_4의 조건은 작업 범위 결정 조건으로 사용되지 못하고 체크 조건으로 사용된다. 

WHERE 조건의 AND 연산이 아닌 OR 연산에서는 처리 방법이 완전히 바뀐다.

```sql
SELECT *
FROM employees
WHERE first_name='Kebin' OR last_name='Poly';
```

OR 연산자로 연결됐기 때문에 last_name='Poly' 조건은 인덱스를 사용할 수 없다. AND 연산자라면 first_name='Kebin'의 인덱스를 이용했을 것이다.

#### 11.4.2.3 GROUP BY 절의 인덱스 사용

GROUP BY 절에 명시된 칼럼의 순서가 인덱스를 구성하는 칼럼의 순서와 같으면 일단 인덱스를 이용할 수 있다.

인덱스를 구성하는 칼럼 중 뒤쪽에 있는 칼럼은 GROUP BY 절에 명시되지 않아도 인덱스를 사용할 수 있지만 인덱스의 앞쪽에 있는 칼럼이 GROUP BY 절에 명시되지 않으면 인덱스를 사용할 수 없다.

WHERE 조건절과는 달리 GROUP BY 절에 명시된 칼럼이 하나라도 인덱스에 없으면 GROUP BY 절은 전혀 인덱스를 이용하지 못한다.  


#### 11.4.2.4 ORDER BY절의 인덱스 사용

ORDER BY 절의 인덱스 사용은 GROUP BY의 요건과 흡사한데 정렬되는 각 칼럼이 오름차순 및 내림차순 옵션이 인덱스와 같거나 정반대인 경우만 사용할 수 있다는 점이 추가된다.

MySQL의 인덱스는 모든 칼럼이 오름차순으로만 정렬돼 있기 때문에 ORDER BY 절의 모든 칼럼이 오름차순이거나 내림차순일 때만 인덱스를 사용할 수 있다.

...
(인덱스 학습이 미진하여 보류)

### 11.4.3 WHERE 절의 비교 조건 사용 시 주의사항

#### 11.4.3.1 NULL 비교

MySQL에서는 NULL을 하나의 값으로 인정해 NULL 값이 포함된 레코드도 인덱스로 관리된다.

NULL의 정의는 SQL 표준상 비교할 수 없는 값이다.  두 값이 모두 NULL이라도 동등 비교는 불가능하다. 연산이나 비교에서 한쪽이라도 NULL이면 그 결과도 NULL인 이유다.

쿼리에서 NULL인지 비교하려면 `IS NULL` 또는 `<=>` 연산자를 사용해야 한다.

```sql
SELECT NULL=NULL;

+-----------+
| NULL=NULL |
+-----------+
|      NULL |
+-----------+

SELECT NULL<=>NULL;

+-------------+
| NULL<=>NULL |
+-------------+
|           1 |
+-------------+
```

#### 11.4.3.2 문자열이나 숫자 비교

#### 11.4.3.3 날짜 비교

날짜만 저장하는 DATE 타입, 날짜와 시간을 저장하는 DATETIME 타입, 시간만 저장하는 TIME 타입이 있다.

##### 11.4.3.3.1 DATE 또는 DATETIME과 문자열 비교

DATE 또는 DATETIME 타입의 값과 문자열을 비교할 때 칼럼의 타입이 문자열을 DATE나 DATETIME으로 변환하지 않아도 MySQL에 내부적으로 변환을 수행하고 인덱스를 효율적으로 이용하므로 성능 문제를 고민하지 않아도 된다.

##### 11.4.3.3.2 DATE와 DATETIME 비교

DATETIME 값에서 시간 부분만 떼어 버리고 비교하려면 `DATE()` 함수를 사용하면 된다.

```sql
SELECT COUNT(*) FROM employees
WHERE hire_date > DATE(NOW());
```

DATETIME 타입의 값을 DATE 타입으로 만들지 않고 그냥 비교하면 MySQL 서버가 DATE 타입의 값을 DATETIME으로 변환해서 같은 타입을 만든 다음 비교를 수행한다.
이를테면 "2011-06-30"을 "2011-06-30 00:00:00"으로 변환하므로 쿼리 결과에 주의가 필요하다.

##### 11.4.3.3.3 DATE와 TIMESTAMP 비교

DATE 또는 DATETIME 타입의 값과 `TIMESTAMP`값을 타입 변환 없이 비교하면 문제가 없는 것 같이 보이지만 사실은 그렇지 않다.

```sql
SELECT COUNT(*) FROM employees WHERE hire_date < '2011-07-23 11:10:12';

+----------+
| COUNT(*) |
+----------+
|   300024 |
+----------+

SELECT COUNT(*) FROM employees WHERE hire_date > UNIX_TIMESTAMP('1986-01-01 00:00:00');

+----------+
| COUNT(*) |
+----------+
|        0 |
+----------+

SHOW WARNINGS;

+---------+------+-------------------------------------------------------------------+
| Level   | Code | Message                                                           |
+---------+------+-------------------------------------------------------------------+
| Warning | 1292 | Incorrect date value: '504889200' for column 'hire_date' at row 1 |
| Warning | 1292 | Incorrect date value: '504889200' for column 'hire_date' at row 1 |
+---------+------+-------------------------------------------------------------------+
```

`UNIX_TIMESTAMP()` 함수 결과값은 MySQL 내부적으로 단순 숫자 값이므로 두 번째 쿼리는 원하는 결과를 얻을 수 없다.

이때는 반드시 비교 값으로 사용되는 상수 리터럴을 비교 대상 칼럼의 타입에 맞게 변환해서 사용해야 한다.

칼럼이 DATETIME 타입이라면 `FROM_UNIXTIME()` 함수를 이용해 TIMESTAMP 값을 DATETIME 타입으로 만들어서 비교해야 한다.

그리고 반대로 칼럼의 타입이 TIMESTAMP라면 `UNIX_TIMESTAMP()` 함수를 이용해 DATETIME을 TIMESTAMP로 변환해서 비교해야 한다. 또는 간단하게 `NOW()` 함수를 시용해도 된다.

```sql
SELECT COUNT(*) FROM employees WHERE hire_date < FROM_UNIXTIME(UNIX_TIMESTAMP());

+----------+
| COUNT(*) |
+----------+
|   300024 |
+----------+

SELECT COUNT(*) FROM employees WHERE hire_date < NOW();

+----------+
| COUNT(*) |
+----------+
|   300024 |
+----------+
```
 
#### 11.4.3.4 Short-Circuit Evaluation

간단한 다음 의사 코드를 이용해 "Short-circuit Evaluation"의 작동방식을 살펴보자.

```
boolean in_transaction;

if ( in_transaction && has_modified() ) {
  commit();
}
```

많은 프로그래밍 언어에서는 빠른 성능을 위해 is_tansaction 불리언 변숫값이 FALE라면 has_modified() 함수를 호출도 하지 않고 다음 코드를 실행한다. 이처럼 여러 개의의 표현식이 AND 또는 OR 논리 연산자로 연결된 경우 선행 표현식의 결과에 따라 후행 표현식 평가 여부를 결정하는 최적화를 ''Short-circuit Evaluation"라고 한다.

MySQL 서버는 쿼리의 WHERE 절에 나열된 조건을 순서대로 "Short-circuit Evalution" 방식으로 평가해서 해당 레코드를 반환해야 할 지 말지를 결정한다. 그런데 WHERE 절의 조건 중에서 인덱스를 사용할 수 있는 조건이 있다면 "Short-circuit Evalution"과는 무관하게 MySQL 서버는 그 조건을 가장 최우선으로 사용한다.

### 11.4.4 DISTINCT

DISTINCT를 남용하는 것은 성능적인 문제도 있지만 쿼리의 결과도 의도한 바와 달라질 수 있다(9.2.5절 참고).

### 11.4.5 LIMIT n

```sql
SELECT * FROM employees
WHERE emp_no BETWEEN 10001 AND 10010
ORDER BY first_name
LIMIT 0, 5;

+--------+------------+------------+-----------+--------+------------+
| emp_no | birth_date | first_name | last_name | gender | hire_date  |
+--------+------------+------------+-----------+--------+------------+
|  10006 | 1953-04-20 | Anneke     | Preusig   | F      | 1989-06-02 |
|  10002 | 1964-06-02 | Bezalel    | Simmel    | F      | 1985-11-21 |
|  10004 | 1954-05-01 | Chirstian  | Koblick   | M      | 1986-12-01 |
|  10010 | 1963-06-01 | Duangkaew  | Piveteau  | F      | 1989-08-24 |
|  10001 | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
+--------+------------+------------+-----------+--------+------------+
```

- 1) employees 테이블에서 WHERE 절의 검색 조건에 일치하는 레코드를 전부 읽어 온다.
- 2) 1번에서 읽어온 레코드를 first_name 칼럼값에 따라 정렬한다.
- 3) 정렬된 결과에서 상위 5건만 사용자에게 반환한다.


MySQL의 LIMIT은 WHERE 조건이 아니기 때문에 항상 쿼리의 가장 마지막에 실행된다.

LIMIT의 중요한 특성은 LIMIT에서 필요한 레코드 건수만 준비되면 즉시 쿼리를 종료한다는 것이다. 즉 위의 쿼리에서 모든 레코드의 정렬이 완료되지 않았다고 하더라도 상위 5건까지만 정렬되면 작업을 멈춘다.

ORDER BY나 GROUP BY 또는 DISTINCT가 인덱스를 이용해 처리될 수 있다면 LIMIT 절은 꼭 필요한 만큼의 레코드만 읽게 만들어 주기 때문에 쿼리의 작업량을 상당히 줄여준다.

LIMIT 절의 인자가 1개인 경우에는 상위 n개의 레코드를 가져온다.

2개의 인자인 경우는 첫 번째 인자에 지정된 위치부터 두 번째 인자에 명시된 개수 만큼의 레코드를 가져온다. 첫 번째 인자(시작 위치, 오프셋)는 0부터 시작한다는 것에 주의하자.

다음 예제의 첫 번째 쿼리는 상위 10개의 레코드만, 두 번째 쿼리는 상위 11번째부터 10개의 레코드를 가져온다.

```sql
SELECT * FROM employees LIMIT 10;
SELECT * FROM employees LIMIT 10, 10;
```

자주 부딪히는 제한 사항으로는 LIMIT의 인자로 표현식이나 서브쿼리를 사용할 수 없다는 점이 있다.

한 가지 더 주의할 점이 있다. 화면에 레코드가 몇 건 출력되느냐보다 MySQL 서버가 어떤 작업을 했는지가 중요하다. 많은 응용 프로그램에서 테이블 데이터를 SELECT할 때 조금씩 잘라서(페이징) 가져가는데 LIMIT을 쓰곤 한다.

```sql
SELECT * FROM salaries ORDER BY salary LIMIT 0, 10;
10 rows in set (0.01 sec)

SELECT * FROM salaries ORDER BY salary LIMIT 20000000, 10;
Empty set (2.52 sec)
```
`LIMIT 20000000, 10`은 먼저 salaries 테이블을 처음부터 읽으면서 20000010건의 레코드를 읽은 후, 20000000건은 버리고 마지막 10건만 사용자에게 반환한다.

LIMIT 조건의 페이징이 처음 몇 개 페이지 조회로 끝나지 않을 가능성이 높다면 WHERE 조건절로 읽어야할 위치를 찾고 그 위치에서 10개만 읽는 형태의 쿼리를 사용하는 것이 좋다.

## Source

* [Real MySQL 8.0 2권](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158392727&orderClick=JGJ)

