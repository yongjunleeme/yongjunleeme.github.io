---
layout  : wiki
title   : 
summary : 
date    : 2020-07-05 21:28:27 +0900
updated : 2020-07-06 12:30:34 +0900
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

## Docker

### Mysql

- ec2에서 설치

```python
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

- 실행

```python
$ docker run -it --rm --name mysql-db -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password -e MYSQL_PASSWORD=password mysql
```

- 확인

```python
$ docker ps
```

- `settings.py` 변경

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '127.0.0.1',
        'NAME': 'mysql-db',
        'USER': 'root',
        'PASSWORD': 'password',
        'PORT': '3306',
        'OPTIONS': {'charset': 'utf8mb4'},
    }
}
```

### Redis

```python
$ docker run --rm --name some-redis -d -p 6379:6379 redis
```

- `settinsg.py`

```python
CACHES = {
        "default": {
        "BACKEND" : "django_redis.cache.RedisCache",
        "LOCATION" : "redis://127.0.0.1",
        "OPTIONS": {
            "CLIENT_CLASS" : "django_redis.client.DefaultClient",
        }
    }
}
```

### Django

- 도커서버 폴더생성, 프로젝트 이동

```python
$ mkdir docker-server
$ mv projectname/ docker-server/
```

- Dockerfile 생성

```python
$ cd docker-server
$ vim Dockerfile
```

```python
# /docker-server/server_dev/Dockerfile
FROM python:3

ENV PYTHONUNBUFFERED 1

RUN apt-get -y update
RUN apt-get -y install vim

# docker 안에서 vi 설치 안해도됨
RUN mkdir /srv/docker-server # docker안에 srv/docker-server 폴더 생성
ADD . /srv/docker-server # 현재 디렉토리를 srv/docker-server 폴더에 복사

WORKDIR /srv/docker-server # 작업 디렉토리 설정

RUN pip install --upgrade pip
RUN pip install -r ./projectname/requirements.txt

EXPOSE 8000 CMD ["python", "./projectname/manage.py", "runserver", "0.0.0.0:8000"]
```

- `requirements.txt` -> `pip freeze > requirements.txt`로 생성
    - 버전 호환 안 되는 라이브러리 있으면 수동 점검해야
- Docker 빌드
    - -t는 이름 붙이는 옵션 - projectname/django
    - .은 이미지를 만들 경로

```python
$ docker build -t projectname/django .
```

- 이미지 확인

```python
$ docker image list
```

- 도커 실행
    - -p 옵션으로 서버 포트(호스트 포트)와 도커 포트를 맞춰준다.

```python
$ docker run -p 8000:8000 projectname/django
```

### Nginx

```python
# ~/docker-server/nginx/nginx.conf
user root;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    # multi_accept on;
}

http {

    ##
    # Basic Settings
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##

    # include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

```python
# ~/docker-server/nginx/nginx-app.conf
upstream uwsgi {
    server unix:/srv/docker-server/apps.sock;
}

server {
    listen 80;
    server_name localhost;
    charset utf-8;
    client_max_body_size 128M;

    location / {
        uwsgi_pass       uwsgi;
        include          uwsgi_params;
    }

    location /media/ {
        alias /srv/docker-server/.media/;
    }

    location /static/ {
        alias /srv/docker-server/.static/;
    }
}
```

```python
# ~/docker_server/nginx/Dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-app.conf /etc/nginx/sites-available/

RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- 빌드

```python
$ docker build -t projectname/nginx .
```

- 실행

```python
$ docker run -p 80:80 projectname/nginx
```

### Docker-compose

- 여러 개의 이미지를 관리하는 툴, 두 개의 이미지를 연결
- nginx docker + django docker
- 설치

```python
$ sudo curl -L https://github.com/docker/compose/releases/download/1.25.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

- `docker-compose` 명령으로 실행 확인
- Dokerfile처럼 docker-compose.yml을 사용해 설정

```python
# ~/docker-server/docker-compose.yml

version: '3'
services:

    nginx:
        container_name: nginx
        build: ./nginx
        image: projectname/nginx
        restart: always
        ports:
          - "80:80"
        volumes:
          - ./projectname:/srv/docker-server
          - ./log:/var/log/nginx
        depends_on:
          - django

    django:
        container_name: django
        build: ./projectname
        image: docker-server/django
        restart: always
        command: uwsgi --ini uwsgi.ini
        volumes:
          - ./server_dev:/srv/docker-server
          - ./log:/var/log/uwsgi
```

- Dockerfile 설정 
  
```python
# ~/docker_server/nginx/Dockerfile
FROM nginx:latest

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-app.conf /etc/nginx/sites-available/

RUN mkdir -p /etc/nginx/sites-enabled/\
    && ln -s /etc/nginx/sites-available/nginx-app.conf /etc/nginx/sites-enabled/

#EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- uwsgi.ini 파일 생성

```python
# ~/docker-server/server_dev/uwsgi.ini
[uwsgi]
socket = /srv/docker-server/apps.sock
master = true

processes = 1
threads = 2

chdir = /srv/docker-server
module = projectname.wsgi

logto = /var/log/uwsgi/uwsgi.log
log-reopen = true

vacuum = true
```

- 빌드
    - -d는 데몬
    - run이 아닌 up/down

```python
$ docker-compoase up -d --build
```

- 확인

```python
$ docker-compose ps
```


## Link

- [Nginx와 uWSGI 설치해서 연결하기 + docker로 mysql 띄우기](https://cholol.tistory.com/485?category=651593)
- [django, nginx 도커로 구동하기](https://cholol.tistory.com/489)
- [docker로 mysql, redis 실행. Django와 연결](https://cholol.tistory.com/476)

