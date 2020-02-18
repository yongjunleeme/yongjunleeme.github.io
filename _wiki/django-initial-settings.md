---
layout  : wiki
title   : django-initial-settings 
summary : 
date    : 2020-02-18 18:57:43 +0900
updated : 2020-02-18 19:11:43 +0900
tags    : 
toc     : true
public  : true
parent  : django
latex   : false
---
* TOC
{:toc}

## gitignore

- `.gitigore' 파일 생성 후 [여기](https://www.gitignore.io/) 에서 검색해서 키워드 선

## corsheaders

- 2세대 서버 인증은 [CSRF](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0) 를 대비
- 3세대 서버에는 페이지가 없다? 하지만 보안은 중요하다. [CORS 설정과 API 연동](https://blog.thereis.xyz/41)

```shell
$ pip install django-cors-headers
```

```python
## filename: settings.py

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

- `cors-headers`설치와 `settings.py` 수정

```python
## filename: settings.py


##CORS
CORS_ORIGIN_ALLOW_ALL=True
CORS_ALLOW_CREDENTIALS = True

CORS_ALLOW_METHODS = (
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
)

CORS_ALLOW_HEADERS = (
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

- `settings.py`에 추가

## 보안설정

- `my_settings.py` 파일 생성

```python
DATABASES = {
        'default' : {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'DATABASE 명',
            'USER': 'DB접속 계정명',
            'PASSWORD': 'DB접속용 비밀번호',
            'HOST': '실제 DB 주소',
            'PORT': '포트번호',
        }
    }
    
    SECRET = {
            'secret':'시크릿키',
    }

```

```python
## filename: settings.py

import my_settings
DATABASE = my_settings.DATABASES
```

- `settings.py`에서 설정 적용
- 추가적으로 외부 API(SNS로그인, AWS접속 정보 등)도 기록 가능

## Links

[위키백과](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)    
[데어이즈](https://blog.thereis.xyz/41)    
[gitignore](https://blog.thereis.xyz/41)

