---
layout  : wiki
title   : 
summary : 
date    : 2020-05-18 20:47:28 +0900
updated : 2020-06-01 18:21:53 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Backgroud

1991년 스웨덴계 핀란드인 리누스 토발즈가 개발
미닉스 운영체제를 쓰면서 마음에 안 드는 점을 추가해 학습적인 용도로 개발

## 명령어

### cron

```python
$ crontab -e

# m h dom(일) mon(월) dow(요일) command

*/1 * * * * date >> date.log

*/1 (1분에 1번)

콘트롤 z
$ date > date.log
$ cat date.log
$ date >> date.log
$ fg

$ crontab -l
$ cd ~
$ ls
$ tail -f date.log (감시하다가 파일 뒷쪽 텍스트 추가되면 자동출력)
```

### daeman

```python
$ sudo apt-get install apache2  
/etc/init.d - demon 위치한 폴더

$ sudo service apache2 start
$ ps aux | grep apache2
$ sudo service apache2 stop


/etc/rc3.d/ -> 부팅 시 자동실행
$ ls -l
S02apache2 링크형태로
```

### `whereis`

```python
$ man whereis
-> $PATH 환경변수에 담긴 데이터 중에서 찾는 것

$ echo $PATH
```

### `file`

```python
찾기 (디렉토리가 아닌 데이터베이스에서 - mlocate)
$ locate *.log

찾기 (디렉토리에서 - 위보다 느림)
$ find 
$ find --help | head
$ find / -name *.log (루트부터, 이름으로)
$ find ~ -name *.log (홈부터, 이름으로)

$ find . -type f -name "tecmint.txt" -exec rm -f {} \; (디렉토리가 아닌 파일 타입, -exec -> 실행)
```

### file structure

```python
검색 -> linux file sturcture
/bin - User Binaries
/sbin - System Binaries
/etc - Configuration Files

$ cd / (루트로 한방에)

주기억장치에 있는 명령어 실행 상태 - 프로세스
$ ps aux | grep apache   (apache 프로세스 출력)

$ sudo top (프로세스 리스트)
$ sudo htop (없으면 설치)

load average : cpu 점유율 ( 1분, 5분, 15분 평균치 )
```

### shell script

```python
$ mkdir script
$ cd script
$ touch a.log b.lob c.log
$ mkdir bak
$ cp *.log bak

$ ls /bin
$ nano backup

#!/bin/bash   (bash를 쓰겠다는 선언)
if ! [ -d bak ]; then    (-d 디렉토리)
				mkdir bak
fi      (if 끝)
cp *.log bak

콘트롤 x

$ ls -l
$ ./backup
$ chmod +x backup (권한 부여)
$ ls -l
파일 리스트 끝에 x가 붙으면 실행가능하다는 뜻

$ rm -rf bak
$ ./backup
```

### 메일 보내기

```python
$ mail chesidyong@gmail.com << hahaha (메일 보내기)
> hi
> hahaha (끝났다는 의미)

$ ls -al > /dev/null (삭제)
```

### IO Redirection

```python
$ ls- l > result.txt (ls -l의 출력을 result에 저장)
$ cat result.txt

프로세스의 아웃풋의 방향을 바꿔서(redirection: standard output만) result에

output
1 standard output
2 standard error

$ rm rename2.txt 2> eroor.log ( 2번 아웃풋을 사용, 에러메시지가 파일에 저장)

$ cat < hello.txt (1번 아웃풋을 사용)
$ cat hello.txt (hello.txt는 인자로 사용)

$ head -n1 linux.txt (파일 앞쪽 출력, -n1은 한 줄만 출력)
```

### grep(검색)

```python
$ nano linux.txt
$ cat linux.txt  (출력)
$ grep <검색단어> linux.txt

$ ls --a | grep sort (sort가 포함된 설명만 검색해서 출력)
$ ls --a | grep sort | grep file

$ ps aux (실행중 프로그램 출력)
$ ps aux | grep apache
```

### wget(이미지 다운로드)

```python
$ wget <이미지 url>

$ mv download hello.jpg

위 2과정을 한번에 ->
$ wget -O <이미지 이름> <이미지 url>
```

### ssh public private key

```python
$ ip addr

$ ssh 이름@ip주소

$ ssh-keygen

패스워드 노입력
비공개키 , 공개키 만들기 
is_rsa -> private key  vs   id_rsa.pub -> public key

$ cd .ssh

$ ssh-cpy-id 이름@ip주소

$ cat authorized_keys
```
