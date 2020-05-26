---
layout  : wiki
title   : 
summary : 
date    : 2020-05-22 21:23:57 +0900
updated : 2020-05-26 20:17:04 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Introduction to DRF

### 환경설정

```python
$ git clone https://github.com/nomadcoders/airbnb-api --branch blueprint --single-branch awesome-api-course
$ git remote -v
$ rm -rf .git

# 내 레파지토리 생성
$ git remote add origin <repo 주소>
```

```python
$ pipenv shell
$ pipenv install
$ python manage.py createsueruser
$ python manage.py mega_seed

++++
mega_seed에서 오류날 경우
django_seed/__init.__.py 35번째 줄
cls.fakers[code].seed(random.randint(1, 10000)) 를
cls.fakers[code].seed_instance(random.randint(1, 10000)) 로 수정
++++
```

### [Serializers](https://www.django-rest-framework.org/)

- 쿼리셋과 같은 복잡한 데이터를 JSON, XML로 쉽게 변환, 그 반대도 가
- 응답을 제어하는 폼과 같은 역할


many = True -> 리스트 가능

