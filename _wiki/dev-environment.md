---
layout  : wiki
title   : 
summary : 
date    : 2020-07-18 17:07:16 +0900
updated : 2020-07-19 14:29:31 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## VS-code Extension

- Live Server - 하단의 go live버튼 클릭하면 html 파일 서버로 연결
- Material Theme
    - 배경 색상 설정
- settings > shell > OSx
     - /bin/zhs로 변경
- REST Client
    - `.html` 파일 생성
    - `GET https://google.com` - 코드 위에 마우스 놓고 실행
    - post 등도 가능
    - `POST https://google.com`
    - `Authorizatioj: JWT 토큰`
    - `Content-Type: "application/json"`
    - `{
    -       "pk" : "1"
    - }`
- Waka time
    - 작업한 언어, 시간 등을 트래킹해서 대시보드로 제공
- polacode
    - 코드를 상자 안에 넣어 놓고 깔끔한 이미지로 제공
- visual studio live share
    - URL 복사해서 다른 사람에게 주면 내 VSC에 접속 가능

## 파일 저장 클라우드

- Google Deploy Drive File Stream

## CLI

- [Iterm2](https://www.iterm2.com/)
    - [fish shell](https://fishshell.com/) - 자동완성
    - [설치방법](https://lobster1234.github.io/2017/04/08/setting-up-fish-and-iterm2/)

## 가상환경

- [pyenv](https://github.com/pyenv/pyenv)
    - 파이썬 버전별 관리
- [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

```python
# 파이썬 설치
pienv install 3.7.7

# 설치 가능한 파이썬 버전 검색
$ pyenv install --list

# 가상환경
$ pyenv virtualenv 3.7.7 가상환경이름

# 가상환경 작동 
$ pyenv activate 가상환경이름
```

## Link

- [가장 보통의 파이썬 개발 환경(pyenv + pipenv + black + flit)](https://jonnung.dev/python/2019/11/23/ordinary-python-development-environment/)
- [새 맥북으로 이사가기, 설정하기](https://johngrib.github.io/wiki/migrate-to-new-macbook/)



