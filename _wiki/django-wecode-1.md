---
layout  : wiki
title   : django-wecode-1
summary : 
date    : 2020-02-08 13:38:23 +0900
updated : 2020-02-10 15:37:54 +0900
tags    : 
toc     : true
public  : true
parent  : python 
latex   : false
---
* TOC
{:toc}

## Background

[위코드](https://wecode.co.kr/) 에서 배운 장고강의를 기록으로 남깁니다.

## Why

제로베이스에서 Model, View, url을 만들어 기초적인 Http통신이 이뤄지는 로그인 앱을 만들어보자. `$ django-admin startproject 프로젝트이름`과 `$ django-admin startapp 앱이름`을 통해 초기설정을 마친 후 아래 과정을 따라하면 된다.

## asgi.py, wsgi.py

알아서 프로젝트 폴더에 깔리는 두 파일
- **asgi.py** - 비동기 통신에 사용한다(예: 실시간 채팅, 웹소켓).
- **wsgi.py** - Web Server gateway Interface

## Settings.py

```python
INSTALLED_APPS = [
#    'django.contrib.admin',
#    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'user',
    'comment'
]
``

- admin과 auth 기능을 사용하지 않을 예정이라 주석처리

```python
ROOT_URLCONF = 'westa.urls'
```

- 서버가 받을 기준 경로(보통 바꾸지 않는다)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
    }
```

- 데이터베이스 - 여러 개 연결 가능

```python
AUTH_PASSWORD_VALIDATORS 
```

- auth 기능을 쓸 때 비밀번호 복잡도 등의 자릿수 검증


- 그외 설정
    - **import os** - 기준 로를 잡는다.
    - **DEBUG** - 디버깅 가능여부 설정, False로 해놓으면 404 Not Found
    - **allowed_host** - 외부접속을 허용하는 IP주소

추가 설정
```python
##Stop Warning about '/' 슬래시 경고를 안 보게
APPEND_SLASH=False
```

## models.py

```python
class User(models.Model):
    email = models.CharField(max_length=200)
    password = models.CharField(max_length=500) # 암호화를 고려한 500자
    created_at = models.DataTimeField(auto_now_add =True) # 최초 생성시만 시간 기록
    updated_at = models.DateTimeField(auto_now = True) # 변경될 때마다 기록
    
    class Meta:
        db_tables = 'users' # 복수로 사용
```

## ORM

```
$ python manage.py shell
```

```
>>> from user.models import User
>>> User.objects.create(email='abcd@abcd.net', password='1234')

>>> User(email='bcda@aaa.net', password='1234').save()  # save를 해야 
>>> User.object.all()                                   # 인스턴스화된 객체를 한 번에 담아오려다 보니 쿼리셋이라는 단위를 사용한다.
```

```
>>> a = User.objects.all() 
>>> b = User.objects.values()       # 복수로 쓰는 모양
>>> c = User.objects.filter(id=1)
```

위에는 모두 쿼리셋이고 아래는 모두 쿼리셋에 담긴 객체라고 한다.
정확한지 모르겠다. 어떻게 가늠하는 것인지도 모르겠다.

```
>>> d = User.objects.get(id=1)
>>> e = User.objects.filter(id=1).values()
```

> 문서가 아닌 멘토님이 자세히 알려주니 시간절약, 이해도상승.. 그러나 언제 어떻게 강의할지 안내가 없어서 이후 강의를 놓쳤다. 1멘토 20수강생이라 어쩔 수 없는 상황인 것 같다.

