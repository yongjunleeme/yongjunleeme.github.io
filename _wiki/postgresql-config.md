---
layout  : wiki
title   : 
summary : 
date    : 2020-07-03 18:00:55 +0900
updated : 2020-07-03 19:37:06 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Config

- 파이썬과 postgresql을 연동하기 위한 라이브러리 psycopg2 설치

```python
$ pip install psycopg2
```

```python
$ psql
```

### [사용자 생성](https://www.postgresql.org/docs/9.6/sql-createuser.html)

#### 현재 사용자 정보 출력

```python
postgres=# select * from pg_user;
```

#### Superuser 권한의 사용자 생성

```python
postgres=# CREATE USER yongjunlee PASSWORD 'password' SUPERUSER;
```

- 옵션
    - `superuser` | `nosupseruser`
        - superuser 여부, 기본값은 nosuperuser
    - `createdb` | `nocreatedb`
        - db생성 권한 부여, 기본값은 없음
    - `createuser` | `nocreateuser`
        - user생성 권한 부여 여부, 기본값은 없음
    - `password`
        - password 설정

#### 유저 목록, 권한 조회

```python
postgres=# \du
```

### [DB 생성](https://www.postgresql.org/docs/9.6/static/sql-createdatabase.html)

#### 이름이 dbname인 DB 생성

```python
postgres=# CREATE DATABASE dbname OWNER yongjunlee;
```

#### db 목록 조회

```python
postgres=# \l

or

postgres=# select * from pg_database;
```

#### 옵션

- OWENR : DB owner. Owner 외에 다른 계정은 역할 제한이 있다.
- TEMPLATE : DB Template에 의해 생성될 때 Template 이름이다. 기본값은 template1이다.
- ENCODING : Data Encoding 방법. 값을 지정할 때 LC_CTYPE, LC_COLLATE value와 연계되기 때문에 주의해야 한다.
- LC_COLLATE : String Data를 기준으로 정렬할 때 정렬 기준. 예를 들면 ko_KR.UTF-8은 기본적으로 한글 기준으로 정렬하되, 한글 외의 문자는 UTF-8에 의해 정렬하라는 의미다. 본 시스템 설치 시 ko_KR.UTF-8이 기본값으로 설정되어 있다. (template1의 기본값)
- LC_CTYPE : 대, 소문자, 숫자 등과 같은 문자 분류를 위한 설정.
- TABLESPACE : Table Space를 임의로 설정할 때 사용.
- ALLOW_CONNECTIONS : 외부에서 접속 가능 여부 설정
- CONNECTION LIMIT : DB 접속 제한 설정.
- IS_TEMPLATE : DB Template 인지 여부 설정

```python
CREATE DATABASE name [ [ WITH ] [ OWNER [=] user_name ]
[ TEMPLATE [=] template ]
[ ENCODING [=] encoding ]
[ LC_COLLATE [=] lc_collate ]
[ LC_CTYPE [=] lc_ctype ]
[ TABLESPACE [=] tablespace_name ]
[ ALLOW_CONNECTIONS [=] allowconn ]
[ CONNECTION LIMIT [=] connlimit ]
[ IS_TEMPLATE [=] istemplate ] ]
```

### [Table 생성](https://www.postgresql.org/docs/9.6/sql-createtable.html)

#### psql command 창에서 DB 접속

```python
postgres=# \c dbname yongjunlee
```

#### table 생성

```python
dbname=# CREATE TABLE star (
id integer NOT NULL,
name character varying(255),
class character varying(32),
age integer,
radius integer,
lum integer,
magnt integer,
CONSTRAINT star_pk PRIMARY KEY (id)
);
CREATE TABLE
```

#### table 조회

```python
dbname=# \dt

or

dbname=# select * from pg_tables where tableowner='yongjunlee';
```

## Link

- [PostgreSQL 사용자 추가 및 DB/ Table 생성](https://browndwarf.tistory.com/3)


