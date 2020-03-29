---
layout  : wiki
title   : django-initial-settings 
summary : 
date    : 2020-02-18 18:57:43 +0900
updated : 2020-03-26 18:43:14 +0900
tags    : 
toc     : true
public  : true
parent  : django
latex   : false
---
* TOC
{:toc}

## initial-settings

## gitignore

- `.gitigore` 파일 생성 후 [gitignore](https://www.gitignore.io/) 에서 검색해서 키워드 선택( 예: `vim`,  `macOS`, `django`, `python`) 
     - 프로젝트 시작하면 먼저 해놓아야, 나중에 하면 인식 못할 수도

```python
$ echo "db.sqlite3" >> .gitignore
```

## requirements

```shell
$ pip freeze > requirements.txt
```

- 개발환경 공유방법이라는데 가상환경이 아니라 로컬환경이 모두 포함되는 듯..

```shell
pip install -r requirements.txt
```

- 클론받아서 타인의 가상환경을 설치해야 할 때 쓰는 명령어

## Django cookiecutter

```shell
$ pip install cookiecutter

$ cookiecutter https://github.com/pydanny/cookiecutter-django

$ pip install -r requirements/local.txt

```

```python
## filename: config/settings/base.py

DATABASES = {
    'default': {
       'ENGINE': 'django.db.backends.sqlite3',
       'NAME': os.path.join(ROOT_DIR, 'db.sqlite3'),
  }
}
```


## django 로컬파일 공유방법

### 타인과 서버 공유 시 절차 (장고)
    1. IP체크 후 공유
    2. `settings.py` 내 코드변경
    3. ALLOWD_HOST = ['*']
    4. INSTALLED_APPS`, `MIDDLEWARE`에 Cors 추가
    5. `$ python manage.py runserver 0:8000` 지정해야 외부접근 가능 (이것도 가능 - 0.0.0.0:8000)

### 내 IP주소 확인

```shell
$ ifconfig

"en0: inet에 IP주소 표기"
```

### corsheaders

- 다른 서버와 데이터 쉐어하는 표준
- 2세대 서버 인증은 [CSRF](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0) 를 대비
- 3세대 서버에는 페이지가 없다? 하지만 보안은 중요하다. [CORS 설정과 API 연동](https://blog.thereis.xyz/41)

> 무슨 말..?

- 설치

```shell
$ pip install django-cors-headers
```

### django 설정

```python
## filename: settings.py

ALLOWED_HOST = ['*']

INSTALLED_APPS = [
...
	'django.contrib.staticfiles',
	'corsheaders'
]

MIDDLEWARE = [
...
	'corsheaders.middleware.CorsMiddleware',
...	
]
```

```python
## filename: settings.py

## CORS
CORS_ORIGIN_ALLOW_ALL   = True  # 어떤 서버든 허용
CORS_ALLOW_CREDENTIALS  = True  

CORS_ALLOW_METHODS = ( # 허용 메소드
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
)

CORS_ALLOW_HEADERS = ( # 헤더스에 들어간 키 목록(예: 카카오토큰 사용 시 여기 넣어줘야)
    'accept',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
)
```

## 보안설정

- `my_settings.py` 파일 생성

```python
## filename: my_settings.py
DATABASES = {
        'default' : {
            'ENGINE'    : 'django.db.backends.mysql',
            'NAME'      : 'DATABASE 명',
            'USER'      : 'DB접속 계정명',
            'PASSWORD'  : 'DB접속용 비밀번호',
            'HOST'      : '실제 DB 주소',
            'PORT'      : '포트번호',
        }
    }
    
SECRET = {
            'secret'    : '시크릿키',
}
# 객체가 아니면 변수로 선언
```

```python
## filename: settings.py

import my_settings
DATABASE = my_settings.DATABASES
```

- `settings.py`에서 설정 적용
- 추가적으로 외부 API(SNS로그인, AWS접속 정보 등)도 기록 가능


## `debug_toolbar`, `django-extensions`, `silk`

```python
##filename: settings.py

INSTALLED_APPS = [
    'debug_toolbar',
    'django_extensions',
    'silk'
]


MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
    'silk.middleware.SilkyMiddleware'
]

DEBUG_TOOLBAR_PANELS = [
    'debug_toolbar.panels.versions.VersionsPanel',
    'debug_toolbar.panels.timer.TimerPanel',
    'debug_toolbar.panels.settings.SettingsPanel',
    'debug_toolbar.panels.headers.HeadersPanel',
    'debug_toolbar.panels.request.RequestPanel',
    'debug_toolbar.panels.sql.SQLPanel',
    'debug_toolbar.panels.staticfiles.StaticFilesPanel',
    'debug_toolbar.panels.templates.TemplatesPanel',
    'debug_toolbar.panels.cache.CachePanel',
    'debug_toolbar.panels.signals.SignalsPanel',
    'debug_toolbar.panels.logging.LoggingPanel',
    'debug_toolbar.panels.redirects.RedirectsPanel',
    'debug_toolbar.panels.profiling.ProfilingPanel'
]
```

```python
## urls.py

from django.urls import path, include

urlpatterns = [
        path('hanteo', include('rank.urls')),
        path('silk', include('silk.urls', namespace='silk'))
]

from django.conf import settings
from django.conf.urls import include, url  # For django versions before 2.0

if settings.DEBUG:
    import debug_toolbar
    urlpatterns = [
        path('__debug__/', include(debug_toolbar.urls)),
    ] + urlpatterns

```


## Links

* [위키백과](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)
* [데어이즈](https://blog.thereis.xyz/41)
* [gitignore](https://blog.thereis.xyz/41)

