---
layout  : wiki
title   : aws
summary : 
date    : 2020-03-04 14:05:38 +0900
updated : 2020-04-27 18:25:33 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 사용방법

### [EC2](https://stackoverflow.com/c/wecode/questions/176) 초기세팅

#### 서버접속

```shell
$ chmod -R 400 [keyname.pem]
"권한 설정 - 파일소유자 읽기 권한으로"

$ ls -al "변경사항 확인"

$ ssh -i keyname ubuntu@13.124.155.63 "EC2.IP주소 적고 EC2 인스턴스 접속"
```

#### 가상환경 콘다 설치

```shell
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

chmod -R 755 Miniconda3-latest-Linux-x86_64.sh "실행 가능하도록 권한 변경"

./Miniconda3-latest-Linux-x86_64.sh "파일실행 - yes 계속"

"콘다 환경변수 변경"
cd miniconda3/bin
./conda init bash
cd
source .bashrc
```

#### sql 설치

```shell
sudo apt-get update
sudo apt-get upgrade
sudo api install gcc
sudo apt-get install libmysqlclient-dev
```

### [RDS와 Mysql 연동](https://stackoverflow.com/c/wecode/questions/172)

#### 디비 생성

```shell
mysql> create database hivibe character set utf8mb4 collate utf8mb4_general_ci;
```

#### `my_settinsg.py` 수정해 RDS의 mysql을 연결

```python
DATABASES = {
    'default' : {
        'ENGINE'   : 'django.db.backends.mysql',
        'NAME'     : 'hivibe', # 데이터베이스명, 생성 먼저 해야 함
        'USER'     : 'root',
        'PASSWORD' : 'password',
        'HOST'     : '',  # RDS 엔드포인트
        'PORT'     : '3306',
        'OPTIONS'  : {
            'init_command'  : "SET sql_mode='STRICT_TRANS_TABLES'",
            'charset'       : 'utf8mb4',
            'use_unicode'   : True,
        },
    }
}
```

#### `settings.py` 수정해 허용 IP 변경

```python
ALLOWED_HOSTS = ['*', '00.000.00.000', '00.000.00.000:8000']   # EC2 IPv4 퍼블릭 IP
```

#### `gunicorn` 으로 종료상관없이 서버구동

```python
$ pip install gunicorn

$ nohup gunicorn --bind 0:8000 vibe.wsgi &   "서버 On"

"서버 Off"
$ ps -ef | grep python
$ kill 0000   "공통된 숫자 찾아서 표기"
```

#### 원격접속

```shell
$ mysql -h "호스트명" -u root -p 
"호스트명 -> 엔드포인트(AWS웹사이트 내 명시)"

"예시"
$ mysql -h temptest.cj5v1k6zfree.ap-northeast-2.rds.amazonaws.com -u root -p
```

### 로컬 DB RDS로 이관


#### 데이터베이스 백업

```python
$ mysqldump -u root -p --databases hivibe > hivibe.sql
```

#### 로컬DB RDS로 이관

```shell
$ mysql -h wetyle_share-test.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -u root -p hivibe < hivibe.sql
"로컬DB를 아마존으로"
```

```shell
$ mysql -h RDS주소 -u 사용자명 -p
"RDS 접속 확인"
```

- [장고와 RDS에서 생성된 mysql 연동 참고링크](https://lukelee91.github.io/blog/aws-django-mysql-connection)

## Links

- [wave1994님 블로그](https://wave1994.tistory.com/86)
- [욜욜욜님 블로그](https://yorr.tistory.com/18)
- [남쥐님 블로그](https://blog.naver.com/namji117/221760954391)
