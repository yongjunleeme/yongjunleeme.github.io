---
layout  : wiki
title   : 
summary : 
date    : 2020-07-05 21:28:27 +0900
updated : 2020-07-06 00:07:37 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Background

- client - nginx - socket - uwsgi - django 연결

## AWS 서버 생성

- ec2 ubuntu 생성
- python, 가상환경 생성

```python
$ top
$ ls -rlt

$ sudo apt update
$ sudo apt install python3-pip
$ sudo apt install virtualenv
$ virtualenv venv --python=python3
$ source venv/bin/activate
```

- ec2 인바운드 보안설정에 TCP 8000포트, 위치무관 추가
- 장고 설치
    - 'ALLOWED_HOSTS' 변경
    - 런서버

```python
filename : settings.py

ALLWED_HOSTS = ['*']
```

```python
$ python manage.py runserver 127.0.0.1:8000 # 로컬만 되니 이거 말고 
$ python manage.py runserver 0.0.0.0:8000 # 이것으로
```

- AWS DNS로 접속 확인
- 장고 런서버는 디버깅을 위한 서버 - 성능이 안 좋다, 이에 아래 구조로 구현
- 웹 클라이언트 - 웹 서버 - 소켓 - WSGI(Web server Gateway Interface) - Django - Python function
    - wsgi - 서버와 프레임워크 통신
    - 소켓을 쓰는 이유는 통신을 하면 헤더를 써서 과정이 하나 더 생기는데 소켓을 쓰면 그러지 않아도 되기 때문

## uwsgi

```python
$ pip install uwsgi
$ uwsgi --http :8000 --module projectname.wsgi
```

- uwsgi 설정파일 만들기

```python
$ cd /etc
$ sudo mkdir uwsgi
$ cd uwsgi
$ sudo mkdir sites
$ cd sites
$ vim projectname.ini // 아래 내용 추가
```

```python
[uwsgi]
base = /home/ubuntu/projectname

home = /home/ubuntu/venv  # 가상환경 경로
chdir = %(base) # 프로젝트 경로
module = projectname.wsgi:application

socket = /tmp/django.sock # 장고 뒤에 이름은 임의로 가능
chmod-socket = 666 # 소켓 권한 설정, 666은 누구나 쓸 수 있다는 의미

master = true # 마스터 프로세스는 워커 프로세스들을 감시한다
enable-threads = true # 멀티 쓰레드 옵션
pidfile = /tmp/django.pid # 프로세스 kill할 때 하나씩 pid 찾아서 해야 하는 번거로움을 덜기 위해 한 개로 묶어주는 옵션

vaccum = true # uwsgi가 내려가면 자동으로 pid, socket 등을 지워주는 옵션
logger = file:/tmp/uwsgi.log # 로그 남길 경로 설정
```

- 실행

```python
# 실행
$ uwsgi -i /etc/uwsgi/sites/projectname.ini --http :8000

# 실행확인
$ ps -ef | grep uwsgi

# 파일추적
# 명령 실행한 상태로 웹접속하면 곧장 로그가 뜬다
$ tail -f /tmp/uwsgi.log
```

## nginx

- 설치

```python
$ sudo apt-get install nginx
```

- 설정 폴더 자동으로 생김 `/etc/nginx/`
- 설정 파일 확인

```python
$ vim /etc/nginx/nginx.conf
```

- 설정 파일 변수 내용 하나하나 숙지 중요
    - 여기를 참고 - [Nginx와 uWSGI 설치해서 연결하기 + docker로 mysql 띄우기](https://cholol.tistory.com/485?category=651593)
    - `tcp_nopush on;` - tcp 패킷전송 시 개별이 아니라 모아서 전송하는 혼잡제어 가능하게 만듦
    - `tcp_nodelay_on;` - 작은 데이터도 기다리지 않고 바로 보내도록 만듦
        - nginx는 상반되는 두 옵션을 상황에 맞게 대응 가능

```python
# 서버설정
$ sudo vim /etc/nginx/sites-enabled/projectname # 아래 내용 추가
```

```python
upstream djnago {
    server unix:///tmp/django.sock;
} # 서버그룹을 정의, 서버가 여러 개면 여러 개 작성 가능

server {
    listen 80;
    server_name localhost;
    charset utf-8;

    client_max_body_size 75M; # 클라이언트 요청 최대 사이즈

    location / {
        uwsgi_pass django; # uwsgi 요청이 오면 바로 장고로
        include /etc/nginx/uwsgi_params;
    }
} # 서버 정의
```

- aws 보안그룹 인바운드
    - HTTP 80 포트
    - TCP 8080 포트 위치무관 설정 추가
- nginx를 구동하면 /etc/nginx/nginx.conf에서 /etc/nginx/sites-enables/projectname을 읽어서
- 80(http) 포트로 오는 요청을 uwsgi로 보냄
- 이에 nginx listen 포트는 80이 아닌 8080으로 변경

```python
$ sudo vim /etc/nginx/sites-enables/default 
```

```python
# 8080으로 변경
server {
   listen 8080 default_server;
	 listen [::]:8080 defulat_server;
}
```

- 실행

```python
# 실행
$ uwsgi -i /etc/uwsgi/sites/projectname.ini # uwsgi 먼저 실행
$ sudo systemctl start nginx # nginx start

# 확인
$ ps -ef | grep nginx
```

- 로그

```python
$ vim /etc/nginx/nginx.conf # log 경로 찾을 수 있다
$ cd /var/log/nginx
$ tail -f /var/log/nginx/access.log
```

## Docker, Mysql

- ec2에서 설치

```python
$ mkdir docker-server

# docker 설치
$ curl -fsSL https://get.docker.com/ | sudo sh

# 현재 접속 유저 권한 설정
$ sudo usermod -aG docker $USER

# 특정 유저 권한 설정
$ sudo uwermod -aG docker username
```

- MySQL docker pull
    - [Docker Hub](https://hub.docker.com/_/mysql)

```python
$ docker pull mysql
```


## Link

- [Nginx와 uWSGI 설치해서 연결하기 + docker로 mysql 띄우기](https://cholol.tistory.com/485?category=651593)
- [django, nginx 도커로 구동하기]([https://cholol.tistory.com/489?category=651593](https://cholol.tistory.com/489?category=651593))
- [docker로 mysql, redis 실행. Django와 연결]([https://cholol.tistory.com/476?category=651593](https://cholol.tistory.com/476?category=651593))

