---
layout  : wiki
title   : 
summary : 
date    : 2020-03-18 14:59:57 +0900
updated : 2020-03-22 23:35:29 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Background

- VM Ware는 무겁다.
    - Hypervisor 일정량 할당 그러나 리소스 많이 먹어
- Docker
    - OS 가상화, 경량화, 쉬운 복제
    - 추가한 변동사항이 레이어처럼 누적
        - Client와server
        - 이미지 - 디펜던시 환경(예: 우분투) - 없으면 허브에서 가져올 수 있다.
            - 배포가 빨라지는 이유 
        - registries
            -  도커 이미지를 저장하는 레파지토리 개념
        - Docker Containers
            - 이미지를 실행시키는 가상화 공간
        - Docker Compose And Swarm
            - 스택이나 클러스터를 관리하는 서비스

## Install

- 우분투

```shell
$ sudo apt update
$ sudo apt install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
$ sudo apt update
$ apt-cache policy docker-ce

$ sudo apt install docker-ce

```

- [맥](https://hub.docker.com/editions/community/docker-ce-desktop-mac)


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

### Dockerfile 내용

```shell
#./Dockerfile
FROM python:3 #기반이 될 이미지

WORKDIR /usr/src/app # 작업디렉토리(default)설정

## Install packages
COPY requirements.txt ./ #현재 패키지 설치 정보를 도커 이미지에 복사
$ RUN pip install -r requirements.txt #설치정보를 읽어 들여서 패키지를 설치

## Copy all src files
COPY . . #현재경로에 존재하는 모든 소스파일을 이미지에 복사

## Run the application on the port 8080
EXPOSE 8000   #8000번 포트를 외부에 개방하도록 설정

#CMD ["python", "./setup.py", "runserver", "--host=0.0.0.0", "-p 8080"]
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "example.wsgi:application"] #gunicorn을 사용해서 서버를 실행 

```

- 주석은 모두 지우고 저장

### Doker 파일 이용해 이미지 빌드

```shell
$ docker build -t '도커허브에 가입한 계정명'/'이미지명(프로젝트명 권장)':'버전' .
ex) docker build -t wecode/wecodeproject:0.1.0 .
```

### 빌드된 이미지 실행

```shell
$ docker run --name '컨테이너 명' -d'데몬으로 실행하기 위한 옵션' -p '호스트 포트':'컨테이너 포트' '이미지명'
ex) docker run --name wecode01 -d -p 8000:8000 wecode/wecodeproject:0.1.0
```

- 빌드된 이미지에 이상이 있을 경우, docker ps -a 명령어로 살펴봤을 때, status가 exited 됐을 것이다. 서버가 실행되다 오류가 발생해 문제가 되는 경우가 많다. 그렇다면 다음과 같이 실행해서 문제를 확인해 볼 수 있다.

```shell
$ docker run -it wecode/wecodeproject:0.1.0 /bin/bash
#위의 명령어를 실행하면 이미지를 기반으로 바로 컨테이너를 실행하면서 접속한다.
#process 등 여러가지를 살펴보면서 현재 서버에 어떤 문제가 있는지 알 수 있다. 하지만 수정은 컨테이너에서 하는게 아닌 이미지를 다시 빌드 해야한다.
```

### 기타

- 이미지 전부 삭제

```shell
$ docker rmi $(docker images -q)
```

- 컨테이너 전부 삭제

```shell
$ docker rm $(docker ps -a -q)
```

- 실행중인 docker container에 shell 접속하기
    - i: doccker 컨테이너의 STDIN을 open한다.
    - t: doccker 컨테이너에 psuedo tty를 지정해준다. 위의 두 옵션을 사용하여 실행중인 doccker 컨테이너에 shell 접속을 할 수 있다.

```shell
docker run -i -t ubuntu /bin/bash
```

- Docker ps
    -현재 실행중인 docker container들을 볼 수 있다. (마치 ps 커맨드 처럼) * -a 옵션을 설정하면 실행되었다 멈춘 컨테이너 들도 나열된다. * -l 옵션을 설정하면 가장 최근의 컨테이너 정보가 나열된다 (가장 최근에 시작되었거나 멈추어진 컨테이너). * -q: returns container IDs only

- Daemonized docker containers:
    -d 옵션

```shell
docker run --name daemonized_container -d <docker_image>
```

- Production 환경에서 실행될 docker container는 대부분 daemonized 된 상태로 실행될 것이다.

- Logging
    -logs 커맨드

```shell
docker logs daemonized_container
```

- tail 커맨드 처럼 -f 옵션을 설정해서 tailing이 가능하다.

```shell
docker logs -f demonized_container
```

--tail 옵션으로 log의 부분만 볼 수 있다.

```shell
docker logs --tail 10 daemonized_container
```

- -t: 각 로그에 timestamp가 추가된다. 디버깅에 용이하다.

```shell
docker logs -ft daemonized_container
```

- `--log-driver`
    - Docker 1.6 버젼 부터는 --log-driver 옵션을 설정하여 더 다양한 로그 설정을 할 수 있다.
    - Default는 json-file 로서 docker logs 로 볼수 있는 기본적인 로그다.
    -syslog 로 설정하면 docker logs 커맨드는 disable되고 모든 로그는 Syslog로 redirect된다.
    
```shell
docker run --log-driver="syslog" --name daemonized_container -d 
<docker_image>
```

- none: 로깅을 disable 한다.
- 그 외에도 여러 로깅 옵션이 있다.

- Docker Process Inspection
    - docker top

```shell
docker top daemonized_container
``````

    - docker stats

```shell
docker stats daemonized_container
```

- Docker Container Inspection
    - docker inspect command
    - -f or --format option to selectively query the inspect results

```shell
docker inspect --format='{{.NetworkSettings.IPAddress }}' daemonized_container
```

- To run additional process inside a running container
    -docker exec

```shell
docker exec -d daemonized_container touch /etc/new_config_file
```

- To stop & kill docker container
    - docker stop: sends a SIGTERM signal.
    - docker kill: sends a SIGKILL signal.

- To delete a container
    - docker rm
    - To delete all docker container:

```shell
docker rm -f `sudo docker ps -a -q`
```

- the -f flag forces to delete any running containers. If you want to protect running containers, omit the flag.


- Automatic restart
    - --restart flag 설정으로 docker container가 멈추었을때 다시 자동으로 시작하게 할 수 있다.
    - no: do not automatically restart the container. (the default)
    - on-failure: restart the container if it exits due to an error, which manifests as a non-zero exit code.
        - This flag also accepts an optional restart count: --restart=on-failure:5
    - unless-stopped: restart the container unless it is explicitly stopped or Docker itself is stopped or restarted.
always: always restart the container if it stops.

## Link

- [Wecode](https://stackoverflow.com/c/wecode/questions/275)
