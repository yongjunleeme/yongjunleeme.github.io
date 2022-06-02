---
layout  : wiki
title   : 
summary : 
date    : 2022-06-02 20:39:01 +0900
updated : 2022-06-02 20:39:17 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 매뉴얼의 SQL 문법 표기를 읽는 방법  
- 대괄호`[]`는 해당 키워드나 표현식 자체가 선택 사항임을 의미
- 파이프`|`는 앞 뒤의 키워드나 표현식 중에서 단 하나만 선택해서 사용할 수 있음을 의미
- 중괄호`{}`는 괄호 내의 아이템 중에서 반드시 하나를 사용해야 하는 경우
- `...`표기는 앞에 명시된 키워드나 표현식의 조합이 반복될 수 있음을 의미

# MySQL 연산자와 내장 함수

## 11.3.1 리터럴 표기법 문자열
### 문자열
- MySQL은 홑따옴표, 쌍따옴표 혼용해서 사용 가능
- 예약어와 충돌방지하려면 역따옴표 사용

### 숫자
- 자동 형변환 가능하지만 성능 등의 이슈 발생 가능하므로 주의

### 날짜
- 다른 DBMS 처럼 `STR_TO_DATE()` 함수를 사용하지 않아도 정해진 형태의 날짜 포맷으로 표기하면 MySQL서버가 자동으로 DATE나 DATETIME으로 변환

### 불리언
- TINYINT(0, 1)로 저장 

## 11.3.2 MySQL 연산자
### 동등(Equal) 비교(=, <=>)
- 동등 비교: = 
- NULL-Safe 비교 연산자: <=>
	- 동등 비교도 수행
	- 양쪽 비교 대상 모두 NULL이면 TRUE, 한 쪽만 NULL이면 FALSE 반환

### 부정(Not-Equal) 비교(<>, !=)
- 같지 않다: <>, !=

### NOT 연산자(!)
- TRUE나 FALSE 연산 결과와 반대로 만드는 연산자: NOT, !
- 값뿐만 아니라 숫자나 문자열 표현식에도 사용가능

### AND(&&)와 OR(||) 연산자
- AND, OR뿐만 아니라 &&, ||도 가능
- AND가 OR보다 우선순위 높음

### LIKE 연산자
REGEXP는 비교 대상 문자열의 일부에 대해서만 일치해도 TRUE를 반환하는 반면, LIKE는 항상 비교 대상 문자열의 처음부터 끝까지 일치하는 경우에만 TRUE를 반환한다.
- `%` : 0 또는 1개 이상의 문자에 일치(문자의 내용과 관계없이)
- `_`: 정확히 1개의 문자에 일치(문자의 내용과 관계없이)

와일드카드 문자인 `%`나 `_` 문자 자체를 비교한다면 `ESCAPE` 절을 `LIKE` 조건 뒤에 추가해 이스케이프 문자를 설정할 수 있다.

```mysql
mysql> SELECT 'abc' LIKE 'a%';
>> 1

mysql> SELECT 'abc' LIKE 'a%';
>> 1

mysql> SELECT 'abc' LIKE 'a/%' ESCAPE '/';
>> 0

mysql> SELECT 'a%' LIKE 'a/%' ESCAPE '/';
>> 1
```
LIKE 연산자는 와일드카드 문자(`%`, `_`)가 검색어 뒤쪽에 있어야 레인지 스캔으로 사용할 수 있다(앞쪽 불가능).

```mysql
mysql> EXPLAIN
	   SELECT COUNT(*)
	   FROM employees
	   WHERE first_name LIKE 'Christ%';
```
'Christ'로 시작하는 이름을 검색하려면 다음과 같이 인덱스 레인지 스캔을 이용해 검색할 수 있다.

하지만 'rist'로 끝나는 이름을 검색하려면 와일드카드가 검색어 앞쪽에 있게 되는데 이 경우 인덱스의 Left-most 특성으로 인해 레인지 스캔을 사용하지 못하고 인덱스를 처음부터 끝까지 읽는 인덱스 풀 스캔 방식으로 쿼리가 처리된다.

### BETWEEN 연산자

BETWEEN 연산자는 다른 비교 조건과 결합해 하나의 인덱스를 사용할 때 주의해야 할 점이 있다.

```mysql
SELECT dept_no BETWEEN 'd003' AND 'd005' AND emp_no = 10001;
```

위 쿼리는 d003보다 크거나 같고 d005보다 작거나 같은 모든 인덱스 범위를 검색해야만 한다. emp_no=10001 조건은 비교 범위를 줄이는 역할을 하지 못한다.

```mysql
SELECT * FROM dept_emp
WHERE dept_no IN ('d003', 'd004', 'd005') 
AND emp_no = 10001;
```

위 쿼리로 바꾸면 emp_no = 10001 조건도 작업 범위를 줄이는 용도로 인덱스를 이용할 수 있다.

MySQL 8.0 버전부터는 `IN (subquery)` 형태로 작성하면 옵티마이저가 세미 조인 최적화를 이용해 더 빠른 쿼리로 변환해서 실행한다.

```mysql
SELECT *
FROM dept_emp USE INDEX(PRIMARY)
WHERE dept_no IN (
	SELECT dept_no
	FROM departments
	WHERE dept_no BETWEEN 'd003' AND 'd005')
AND emp_no = 10001;	
```

### IN 연산자

IN은 여러 개 값에 대해 동등 비교 연산을 수행하는 연산자다. 여러 개의 값이 비교되지만 범위로 검색하는 것이 아니라 여러 번 동등 비교로 실행하기 때문에 일반적으로 빠르게 처리된다.

- 상수가 사용된 경우 - IN ( ?, ?, ?)
- 서브쿼리가 사용된 경우 - IN ( SELECT .. FROM ..)

```mysql
mysql> SELECT *
	   FROM dept_emp
	   WHERE (dept_no, emp_no) IN (('d001', 10017), ('d002', 10144));
```

위 쿼리는 IN절의 상숫값이 단순 스칼라값이 아닌 튜플로 사용됐다.

NOT IN의 실행 계획은 인덱스 풀 스캔으로 표시되는데 동등이 아닌 부정형 비교여서 인덱스를 이용해 처리 범위를 줄이는 조건으로는 사용할 수 없기 때문이다.

## 11.3.3 MySQL 내장 함수  

함수의 이름이나 사용법은 표준이 없으므로 DBMS별로 거의 호환되지 않는다.

### 11.3.3.1 NULL 값 비교 및 대체(IFNULL, ISNULL)
`IFNULL()`은 칼럼이나 표현식의 값이 NULL인지 비교하고, NULL이면 다른 값으로 대체하는 용도로 사용할 수 있는 함수다.

`IFNULL()` 함수에는 두 개의 인자를 전달한다. 첫 번째 인자는 NULL인지 아닌지 비교하려는 칼럼이나 표현식을 설정한다. 두 번째 인자는 NULL일 경우 대체할 값이나 칼럼을 설정한다.

`IFNULL()` 함수의 반환 값은 첫 번째 인자가 NULL이 아니면 첫 번째 인자의 값을, 첫 번째 인자의 값이 NULL이면 두 번째 인자의 값을 반환한다.

```mysql
mysql> SELECT IFNULL(NULL, 1)
>> 1

mysql> SELECT IFNULL(0, 1)
>> 0

mysql> SELECT IFNULL(0)
>> 0

mysql> SELECT IFNULL(1/0)
>> 1
```

### 11.3.3.2 현재 시각 조회(NOW, SYSDATE)

두 함수 모두 현재 시간을 반환하는 함수다. 하지만 하나의 SQL에서 `NOW()` 함수는 같은 값을 가지지만 `SYSDATE()` 함수는 호출되는 시점에 따라 결괏값이 달라진다.

```mysql
mysql> SELECT NOW(), SLEEP(2), NOW();

+---------------------+----------+---------------------+
| NOW()               | SLEEP(2) | NOW()               |
+---------------------+----------+---------------------+
| 2022-05-27 21:15:39 |        0 | 2022-05-27 21:15:39 |
+---------------------+----------+---------------------+

mysql> SELECT SYSDATE(), SLEEP(2), SYSDATE();

+---------------------+----------+---------------------+
| SYSDATE()           | SLEEP(2) | SYSDATE()           |
+---------------------+----------+---------------------+
| 2022-05-27 21:17:24 |        0 | 2022-05-27 21:17:26 |
+---------------------+----------+---------------------+
```

꼭 필요한 때가 아니라면 `SYSDATE()` 함수를 사용하지 않는 편이 좋겠지만 이미 사용중이라면 설정 파일(my.cnf나 my.ini 파일)에 `sysdate-is-now` 시스템 변수를 넣어서 `SYSDATE()` 함수도 `NOW()` 함수처럼 호출 시점에 관계없이 같을 값을 갖게 할 수 있다.

### 11.3.3.3 날짜와 시간 포맷(DATE_FORMAT, STR_TO_DATE)

`DATETIME` 타입으로 변환할 때는 `DATE_FORMAT()` 함수를 이용하면 된다.

`%i` 는 2자시 숫자 표시의 분(00~59)

대소문자를 구분해서 사용해야 한다.

```mysql
mysql> SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s') AS current_dttm;

+---------------------+
| current_dttm        |
+---------------------+
| 2022-05-27 21:54:57 |
+---------------------+
```

SQL에서 표준 형태(년-월-일 시:분:초)로 입력된 문자열은 필요한 경우 자동으로 DATETIME 타입으로 변환되어 처리된다.

### 11.3.3.4 날짜와 시간의 연산(DATE_ADD, DATE_SUB)

사실 `DATE_ADD()` 로 더하거나 빼는 처리를 모두 할 수 있기 때문에 `DATE_SUB()`는 크게 필요하지 않다.

첫 번째 인자는 연산을 수행할 날짜다. 두 번째 인자는 더하거나 뺄 월의 수나 일자의 수등이다.

두 번째 인자의 형태는 `INTERVAL n [YEAR, MONTH, DAY...]`다

```mysql
mysql> SELECT DATE_ADD(NOW(), INTERVAL -1 DAY) AS yesterday;

+---------------------+
| yesterday           |
+---------------------+
| 2022-05-26 21:59:22 |
+---------------------+
```

### 11.3.3.5 타임스탬프 연산(UNIX_TIMESTAMP, FROM_UNIXTIME)

`UNIX_TIMESTAMP()` 함수는 `1970-01-01 00:00:00`으로부터 경과된 초의 수를 반환하는 함수다.

인자가 없으면 현재 날짜와 시간의 타임스탬프 값을 전달한다. 인자에 특정 날짜를 전달하면 그 날짜의 시간의 타임스탬프를 반환한다.

`FROM_UNIXTIME()` 함수는 `UNIX_TIMESTAMP()`와 반대로 인자로 전달한 타임 스탬프 값을 DATETIME 타입으로 변환하는 함수다.

```mysql
mysql> SELECT UNIX_TIMESTAMP();

+------------------+
| UNIX_TIMESTAMP() |
+------------------+
|       1653657246 |
+------------------+

mysql> SELECT UNIX_TIMESTAMP('2020-08-23 15:06:45');

+---------------------------------------+
| UNIX_TIMESTAMP('2020-08-23 15:06:45') |
+---------------------------------------+
|                            1598162805 |
+---------------------------------------+

mysql> SELECT FROM_UNIXTIME(UNIX_TIMESTAMP('2020-08-23 15:06:45'));

+------------------------------------------------------+
| FROM_UNIXTIME(UNIX_TIMESTAMP('2020-08-23 15:06:45')) |
+------------------------------------------------------+
| 2020-08-23 15:06:45                                  |
+------------------------------------------------------+
```

### 11.3.3.6 문자열 처리(RPAD, LPAD / RTRIM, LTRIM, TRIM)

```mysql
mysql> SELECT RPAD('Cloee', 10, '_');

+------------------------+
| RPAD('Cloee', 10, '_') |
+------------------------+
| Cloee_____             |
+------------------------+

mysql> SELECT LPAD('123', 10, '0');

+----------------------+
| LPAD('123', 10, '0') |
+----------------------+
| 0000000123           |
+----------------------+

mysql> SELECT RTRIM('Cloee  ') AS name;

+-------+
| name  |
+-------+
| Cloee |
+-------+

mysql> SELECT LTRIM('     Cloee') AS name;

+-------+
| name  |
+-------+
| Cloee |
+-------+

mysql> SELECT TRIM('     Cloee    ') AS name;

+-------+
| name  |
+-------+
| Cloee |
+-------+
```

### 11.3.3.7 문자열 결합(CONCAT)

여러 개의 문자열을 하나의 문자열로 반환하는 함수로, 인자의 개수는 제한이 없다.

숫자 값을 인자로 전달하면 문자열 타입으로 자동 변환한 후 연결한다.

의도된 결과가 아닌 경우에는 명시적으로 `CAST()` 함수를 이용해 타입을 문자열로 변환하는 편이 안전하다.

```mysql
mysql> SELECT CONCAT('Georgi', 'Christian', CAST(2 AS CHAR)) AS name;

+------------------+
| name             |
+------------------+
| GeorgiChristian2 |
+------------------+
```
비슷한 함수로 `CONCAT_WS()`는 각 문자열을 연결할 때 구분자를 넣어준다. With Separator의 약자.

```mysql
mysql> SELECT CONCAT_WS(',', 'Georgi', 'Christian') AS name;

+------------------+
| name             |
+------------------+
| Georgi,Christian |
+------------------+
```

### 11.3.3.8 GROUP BY 문자열 결합(GROUP_CONCAT)

`GROUP_CONCAT()` 함수는 값들을 먼저 정렬한 후 연결한다. 각 값의 구분자 설정도 가능하다. 여러 값 중에서 중복을 제거하고 연결할 수도 있다.

```mysql
mysql> SELECT GROUP_CONCAT(dept_no) FROM departments;

+----------------------------------------------+
| GROUP_CONCAT(dept_no)                        |
+----------------------------------------------+
| d009,d005,d002,d003,d001,d004,d006,d008,d007 |
+----------------------------------------------+

mysql> SELECT GROUP_CONCAT(dept_no SEPARATOR '|') FROM departments;

+----------------------------------------------+
| GROUP_CONCAT(dept_no SEPARATOR '|')          |
+----------------------------------------------+
| d009|d005|d002|d003|d001|d004|d006|d008|d007 |
+----------------------------------------------+

mysql> SELECT GROUP_CONCAT(dept_no ORDER BY emp_no DESC)
		FROM dept_emp
		WHERE emp_no BETWEEN 100001 and 100003;

+--------------------------------------------+
| GROUP_CONCAT(dept_no ORDER BY emp_no DESC) |
+--------------------------------------------+
| d005,d008,d008,d005                        |
+--------------------------------------------+

mysql> SELECT GROUP_CONCAT(DISTINCT dept_no ORDER BY emp_no DESC)
		FROM dept_emp
		WHERE emp_no BETWEEN 100001 and 100003;

+-----------------------------------------------------+
| GROUP_CONCAT(DISTINCT dept_no ORDER BY emp_no DESC) |
+-----------------------------------------------------+
| d008,d005                                           |
+-----------------------------------------------------+
```

- 세 번째 예제는 dept_emp 테이블에서 emp_no 칼럼의 역순으로 정렬해서 dept_no 칼럼의 값을 연결해서 가져오는 쿼리다. 이 예제에서 GROUP_CONCAT() 함수 내에서 정의된 ORDER BY는 쿼리 전체적으로 설정된 ORDER BY와 무관하다.

`GROUP_CONCAT()`은 제한적인 메모리 버퍼 공간(기본 설정 1KB)을 사용한다. 자주 사용한다면 `group_concat_max_len` 시스템 변수로 크기를 조정할 수 있다.

### 11.3.3.9 값의 비교와 대체(CASE WHEN... THEN ... END)

SWITCH 구문과 같은 역할이다. CASE로 시작하고 END로 끝나야 하며, WHEN ... THEN ... 은 필요한 만큼 반복해서 사용할 수 있다.

```mysql
SELECT emp_no, first_name,
	CASE gender WHEN 'M' THEN 'Man'
				WHEN 'F' THEN 'Woman'
				ELSE 'Unknown' END AS gender
	FROM employees
	LIMIT 10;

+--------+------------+--------+
| emp_no | first_name | gender |
+--------+------------+--------+
|  10001 | Georgi     | Man    |
|  10002 | Bezalel    | Woman  |
|  10003 | Parto      | Man    |
|  10004 | Chirstian  | Man    |
|  10005 | Kyoichi    | Man    |
|  10006 | Anneke     | Woman  |
|  10007 | Tzvetan    | Woman  |
|  10008 | Saniya     | Man    |
|  10009 | Sumant     | Woman  |
|  10010 | Duangkaew  | Woman  |
+--------+------------+--------+

SELECT emp_no, first_name,
	CASE WHEN hire_date < '1995-01-01' THEN 'Old'
		 ELSE 'New' END AS employee_type
	FROM employees
	LIMIT 10;

+--------+------------+---------------+
| emp_no | first_name | employee_type |
+--------+------------+---------------+
|  10001 | Georgi     | Old           |
|  10002 | Bezalel    | Old           |
|  10003 | Parto      | Old           |
|  10004 | Chirstian  | Old           |
|  10005 | Kyoichi    | Old           |
|  10006 | Anneke     | Old           |
|  10007 | Tzvetan    | Old           |
|  10008 | Saniya     | Old           |
|  10009 | Sumant     | Old           |
|  10010 | Duangkaew  | Old           |
+--------+------------+---------------+
```

### 11.3.3.10 타입의 변환(CAST, CONVERT)

프리페어 스테이먼트를 제외하면 SQL은 텍스트(문자열) 기반으로 작동하기 때문에 타입의 변환이 필요하면 `CAST()` 함수를 사용한다. 첫 번째 부분과 두 번째 부분을 구분하기 위해 AS를 사용한다.

변환 가능 데이터 타입: DATE, TIME, DATETIME, BINARY, CHAR, DECIMAL, SIGNED INTEGER, UNSIGNED INTEGER

```mysql
SELECT CAST('1234' AS SIGNED INTEGER) AS converted_integer;

+-------------------+
| converted_integer |
+-------------------+
|              1234 |
+-------------------+

SELECT CAST('2000-01-01' AS DATE) AS converted_date;

+----------------+
| converted_date |
+----------------+
| 2000-01-01     |
+----------------+
```

`CONVERT()` 함수는 `CAST()` 함수와 같이 타입을 변환하는 요도와 문자열의 문자 집합을 변환하는 용도라는 두 가지로 사용할 수 있다.

```mysql
SELECT CONVERT(1-2, UNSIGNED);

+------------------------+
| CONVERT(1-2, UNSIGNED) |
+------------------------+
|   18446744073709551615 |
+------------------------+

SELECT CONVERT('ABC' USING 'utf8mb4');

+--------------------------------+
| CONVERT('ABC' USING 'utf8mb4') |
+--------------------------------+
| ABC                            |
+--------------------------------+
```

### 11.3.3.12 암호화 및 해시 함수(MD5, SHA, SHA2)

```mysql
SELECT MD5('abc');

+----------------------------------+
| MD5('abc')                       |
+----------------------------------+
| 900150983cd24fb0d6963f7d28e17f72 |
+----------------------------------+

SELECT SHA('abc');

+------------------------------------------+
| SHA('abc')                               |
+------------------------------------------+
| a9993e364706816aba3e25717850c26c9cd0d89d |
+------------------------------------------+

SELECT SHA2('abc', 256);

+------------------------------------------------------------------+
| SHA2('abc', 256)                                                 |
+------------------------------------------------------------------+
| ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad |
+------------------------------------------------------------------+
```

### 11.3.3.14 벤치마크(BENCHMARK)

첫 번째 인자는 반복해서 수행할 횟수이며, 두 번째 인자는 반복해서 실행할 표현식을 입력한다. 두 번째 인자는 반드시 스칼라값(하나의 칼럼을 가진 하나의 레코드)을 반환하는 표현식이어야 한다.

```mysql
SELECT BENCHMARK(1000000, MD5('abc'));

+--------------------------------+
| BENCHMARK(1000000, MD5('abc')) |
+--------------------------------+
|                              0 |
+--------------------------------+
1 row in set (0.16 sec)

SELECT BENCHMARK(1000000, (SELECT COUNT(*) FROM salaries));

+-----------------------------------------------------+
| BENCHMARK(1000000, (SELECT COUNT(*) FROM salaries)) |
+-----------------------------------------------------+
|                                                   0 |
+-----------------------------------------------------+
1 row in set (0.09 sec)
```

벤치마크와 직접 실행에는 차이가 있다. 클라이언트 도구로 직접 실행 시 쿼리 파싱, 최적화, 테이블 잠금, 네트워크 비용 등이 소요되므로 고려해야 한다.

# Source

[Real MySQL 8.0 2권](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158392727&orderClick=JGJ)

