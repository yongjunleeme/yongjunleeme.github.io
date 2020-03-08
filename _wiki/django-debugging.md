---
layout  : wiki
title   : 
summary : 
date    : 2020-03-08 18:14:06 +0900
updated : 2020-03-08 18:50:55 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## django-extensions

```shell
$ pip install django_extensions
$ pip install Werkzeug
```

```python
## filename: settings.py

if DEBUG:
    INSTALLED_APPS += [
        'django_extensions',
]
```

```shell
$ python magage.py
"추가된 명령어 확인"
```

```shell
$ python manage.py runservier_plus
$ python manage.py shell_plus
$ python manage.py shell_plus --notebook
```

- runserver_plus - 오류 지점을 클릭하면 파이썬 콘솔이 뜬다.
    - `locals()` - 로컬 디비 출     - `1/0` - 원하는 위치 오류
- shell_plus - app들을 기본 임포

## django-debud-toolbar

```python
$ pip install django_debug_toolbar
```

```python
filename: settings.py

INSTALLED_APPS = [
    'debug_toolbar'
]
```

```python
MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]
```

- 우측에 페이지로드하기 위해 사용한 `sql` 열람 가능
    - `explained` 실행계획
    - `duplicated 4 times` - 같은 쿼리 4번이나 실행됐으니 개선 필요 알림


## ngrok

- 포트 포워딩할 수 없는 환경에 퍼블릭 접근 가능한 도메인 부여
- https도 가능

```shell
$ brew cas install ngrok
```

```shell
$ ngrok http 8000
```

- `localhost:8000/inscpect/http`
    - 터널을 통해 접근한 요청을 모두 볼 수 있다

## Links

- [django debugging 할 때 유용하게 쓸 수 있는 도구들 - 김슬](https://www.youtube.com/watch?v=VUGNYr_GxEY&t=265s)

