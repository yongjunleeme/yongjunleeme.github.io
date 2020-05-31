---
layout  : wiki
title   : 
summary : 
date    : 2020-03-18 14:59:57 +0900
updated : 2020-05-31 18:37:06 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Background

- VM Ware
    - Host OS 위에 Hyperviser가 깔리고 그 위에 가상머신 생성
    - 예를 들어 x86 하드웨어를 가상화
- Docker
    - VM 처럼 하드웨어를 가상화하는 것이 아니라 Guest OS(컨테이너)를 가상화
    - 컨테이너에 커널이 없고 커널이 Host OS를 사용하면서 컨테이너 OS와 다른 부분만 컨테이너 내에 패키징
    - OS 가상화, 경량화, 쉬운 복제
    - 추가한 변동사항이 레이어처럼 누적
        - registries
            -  도커 이미지를 저장하는 레파지토리 개념
        - Docker Compose And Swarm
            - 스택이나 클러스터를 관리하는 서비스

## 기본 명령어

```shell
$ docker

$ docker ps
#(실행중인 컨테이너를 보여주는 커맨드) 

$ docker ps -a
#(실행이 종료된 것을 포함해서 모든 컨테이너를 보는 커맨드 및 옵션)

$ docker images
#(생성된 혹은 다운로드 된 이미지를 보여주는 커맨드)

$ docker images -a
#(모든 이미지를 보여주는 커맨드 및 옵션)
```

### 컨테이너/이미지 삭제

```python
$ docker rm -f container  # 컨테이너 삭제
$ docker rmi image  # 이미지 삭제
```

### 컨테이너 실행/중단

```python
$ docker start 컨테이너명
$ docker stop 컨테이너명
```

### 기타

```python
$ docker history
$ docker inspect
$ docker cp [path]
$ docker commt
$ docker push
```

## 도커 사용방법

- 장고 프로젝트를 로컬에서 도커 이미지로 빌드하고 AWS EC2에 배포하기

### 도커 빌드 in 로컬

#### requirements.txt에 gunicorn 추가

```python
$ pip install gunicorn
$ pip freeze > requirements.txt
```

#### my_settings.py 수정

```python
DATABASES = {
    'default' : {
        'ENGINE': 'django.db.backends.mysql',    
        'NAME': 'hivibe',                  
        'USER': 'root',                          
        'PASSWORD': 'password',                  
        'HOST': 'localhost',                     
        'PORT': '3306',                          
    }
}
```

#### Dokerfile 생성

- 확장자 없이 파일명이 `Dockerfile`

```python
## filename: Dockerfile

#./Dockerfile 
FROM python:3
WORKDIR /usr/src/app # 도커 이미지 내 경로

## Install packages 
COPY requirements.txt ./
RUN pip install -r requirements.txt 

## Copy all src files 
COPY . . # 도커 경로에 복사

## Run the application on the port 8080 
EXPOSE 8000 

#CMD ["python", "./setup.py", "runserver", "--host=0.0.0.0", "-p 8080"] 
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "example.wsgi:application"] # example에는 내 프로젝트의 wsgi 가 위치한 디렉토리를 명시
```

#### 이미지 빌드

```python
$ docker login
```

```python
# docker build -t 도커 계정명/이미지명:버전 도커파일사용위치
$ docker build -t yongjunleeme/hivibe:0.1 . 
```

```python
# 이미지 확인
$ docker images
```

#### 이미지 실행

```python
# -d 데몬 지속실행, -p 포트포워딩 호스트포트:컨테이너포트
$ docker run -d -p 8000:8000 yongjunleeme/hivibe:0.1
```

- 컨테이너명을 쓰려면

```python
# 이미지 실행
sudo docker run --name '컨테이너명' -d -p 8000:8000 yongjunleeme/hivibe:0.1 
```


```python
# 실행 중 컨테이너 확인
$ docker ps
```

#### 이미지 푸시

```python
$ docker push yongjunleeme/hivibe:0.1
```

### AWS EC2 배포

- 우분투

```python
$ sudo apt update
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
$ sudo apt update
$ apt-cache policy docker-ce
$ sudo apt install docker-ce
```

- [맥](https://hub.docker.com/editions/community/docker-ce-desktop-mac)

#### 이미지 pull

```python
$ sudo docker login
```

```python
$ sudo docker pull yongjunleeme/hivibe:0.1
```

```python
# 확인
$ sudo docker images -a
```

```python
# 이미지 실행 컨테이너명 있을 때, 없을 때
$ sudo docker run --name '컨테이너명' -d -p 8000:8000 yongjunleeme/hivibe:0.1 
$ sudo docker run -d -p 8000:8000 yongjunleeme/hivibe:0.1
```

## Link

- [Docker로 프로젝트 배포하기](https://velog.io/@devmin/Docker-deployment)
- [Wecode](https://stackoverflow.com/c/wecode/questions/275)
- [Docker 소개](https://bcho.tistory.com/805)
