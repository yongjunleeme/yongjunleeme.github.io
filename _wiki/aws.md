---
layout  : wiki
title   : aws
summary : 
date    : 2020-03-04 14:05:38 +0900
updated : 2020-04-13 18:44:30 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## AWS 서버종류

### EC2

- Elastic Commpute Cloud
- AWS Server, EC2 서버에 API를 배포
- `t2.nano(CPU 1, 0.5 GB memory)`부터 `x1.32xlarge (CPI 128, 1952GB)` 까지 다양하게 제공

### Security Group

- EC2 인스턴스에 대한 네트워크 트래픽 제어하는 가상 방화벽 역할
- 즉 Security group 설정을 해야 EC2 인스턴스에 HTTP와 SSH 접속 가능

### RDS(Relational Database Service)

- AWS의 데이터베이스 서비스
- 사용자가 직접 DB 운영하는 것보다 저렴, RDS를 사용하지 않을 이유가 거의 없다.

### Load Balancer

- HTTP 요청들을 여러 서버에 분산할 때 사용
- 서버 수를 늘리지 않아도 Load Balancer가 여러 서버들에 HTTP 요청들을 분산

### Route 53

- AWS의 DNS 서비스
- API 시스템을 도메인과 연결

### AWS S3

- AWS S3(Simple Storage Service)는 파일을 쉽게 저장할 공간 제공
- 파일 저장뿐만 아니라 고유 주소까지 부여해 S3에 저장한 파일을 웹에서 쉽게 읽을 수 있음
- 주로 웹사이트 이미지들을 저장하고 렌더링하는 데 사용

## 사용방법

### [EC2](https://stackoverflow.com/c/wecode/questions/176)
- 퍼블릭 IP - 전 세계 어디서도 연결 가능
- IAM 역할 - 계정 권한 설정
- 백엔드 22/SSh, 8000/tcp

#### 서버시작

```shell
chmod -R 400 [keyname.pem]
"권한 설정 - 파일소유자 읽기 권한으로"

ssh -i keyname ubuntu@13.124.155.63
"EC2.IP주소 적고 EC2 인스턴스 접속"
```

- 로컬

```ubuntu
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

chmod -R 755 Miniconda3-latest-Linux-x86_64.sh

./Miniconda3-latest-Linux-x86_64.sh
"미니콘다 인스톨"
```

- EC2 인스턴스

### [RDS와 Mysql 연동](https://stackoverflow.com/c/wecode/questions/172)

#### 원격접속

```shell

$ mysql -h "호스트명" -u root -p 
"호스트명 -> 엔드포인트(AWS웹사이트 내 명시)"

"예시"
$ mysql -h temptest.cj5v1k6zfree.ap-northeast-2.rds.amazonaws.com -u root -p
```

```shell
mysql> create database test character set utf8mb4 collate utf8mb4_general_ci;
```

#### AWX RDS DB Export

```shell
$ mysqldump -u root -p wetyle_share > wetyle_share.sql

$ mysql -h wetyle_share-test.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -u root -p
"로컬DB를 아마존으로"
```

#### EC2 인스턴스에 mysqlclient 설치

```shell

$ sudo apt install gcc
$ sudo apt install libmysqlclient-dev

$ pip install -r requirement.txt
```

```shell
$ pip install gunicorn

$ gunicorn --bind 0:8000 wetyle_share wsgi

$ nohup gunicorn --bind 0:8000 wetyle_share.wsgi &
"백그라운드에서 서버동작"

$ ps -ef | grep python
"현재 동작 중 프로세스"

$ kill <프로세스 숫자>
"가상환경 위에 공통 동작되는 프로세스 찾아서"
```

```shell
$ sudo mysql -uroot
"mysql 비번 없이 로그인"
```

```python
DATABASE = {
    'default' : {
        'ENGINE'   : 'django.db.backends.mysql',
        'NAME'     : 'hivibe',
        'USER'     : 'root',
        'PASSWORD' : '',
        'HOST'     : 'RDS 주소',
        'PORT'     : '3306',
        'OPTIONS'  : {
            'init_command'  : "SET sql_mode='STRICT_TRANS_TABLES'",
            'charset'       : 'utf8mb4',
            'use_unicode'   : True,
        },
    }
}

SECRET_KEY = {
    'secret'   : [YOUR_SECRET_KEY],
    'algorithm': [YOUR_ALGORITHM]
}
```

- my_settings.py 설정

#### 장고와 RDS에서 생성된 mysql 연동

- [참고링크](https://lukelee91.github.io/blog/aws-django-mysql-connection)

## Links

- [wave1994님 블로그](https://wave1994.tistory.com/86)
- [욜욜욜님 블로그](https://yorr.tistory.com/18)
- [남쥐님 블로그](https://blog.naver.com/namji117/221760954391)
