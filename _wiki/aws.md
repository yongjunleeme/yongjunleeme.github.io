---
layout  : wiki
title   : 
summary : 
date    : 2020-03-04 14:05:38 +0900
updated : 2020-03-06 11:07:57 +0900
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
- 't2.nano(CPU 1, 0.5 GB memory)'부터 'x1.32xlarge (CPI 128, 1952GB)' 까지 다양하게 제공

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
- 서버접속

```shell
chmod -R 400 키이

ssh -i keyname ubuntu@13.124.155.63
"퍼블릭 아이피 주소 아마존 웹사이트에서 복붙"

```

### [RDS와 Mysql 연동](https://stackoverflow.com/c/wecode/questions/172)

- 원격접속

```shell

$ mysql -h "호스트명" -u root -p 
"호스트명 -> 엔드포인트(AWS웹사이트 내 명시)"

"예시"
$ mysql -h temptest.cj5v1k6zfree.ap-northeast-2.rds.amazonaws.com -u root -p
```

- AWX RDS DB Export

```shell
$ mysqldump -u root -p -h <host> -v <dbName> <dbTable> > <exportFile.sql>
```


```shell
mysqldump -u root -p 스키마명 > 스키마명.sql wetyle_share < wetyle_share.sql

mysql -h 호스트명 -u root -p wetyle_share < wetyle_share.sql

ec2 서버 접






#### RDS mysql 설치

```shell

$ sudo apt install gcc
$ sudo apt install libmysqlclient-dev

```

- mysql 비번없이 로그인

```shell
$ sudo mysql -uroot
```


####
지유니콘 --demon

nohup gunicorn --bind 0:8000 wetyle_share.wsgi &

nohup ignoring input and appending output to 'nohup.out'

ps -ef | grep python 

kill 3222

## Links

- [ ](https://yorr.tistory.com/18)

