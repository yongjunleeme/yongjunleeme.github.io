---
layout  : wiki
title   : django-initial-settings 
summary : 
date    : 2020-02-18 18:57:43 +0900
updated : 2020-02-20 16:19:04 +0900
tags    : 
toc     : true
public  : true
parent  : django
latex   : false
---
* TOC
{:toc}

## gitignore

- `.gitigore' 파일 생성 후 [여기](https://www.gitignore.io/) 에서 검색해서 키워드 선택
     
예: `vim`,  `macOS`, `django`, `python` 
     
     - 프로젝트 시작하면 먼저 해놓아야, 나중에 하면 인식 못할 수도 

## corsheaders

- 다른 서버와 데이터 쉐어하는 표준
- 2세대 서버 인증은 [CSRF](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0) 를 대비
- 3세대 서버에는 페이지가 없다? 하지만 보안은 중요하다. [CORS 설정과 API 연동](https://blog.thereis.xyz/41)

> 무슨 말..?

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
### 설치

```shell
$ pip install django-cors-headers
```

### 설정

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

CORS_ALLOW_METHODS = (          # 허용 메소드
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
)

CORS_ALLOW_HEADERS = (          # 헤더스에 들어간 키 목록(예: 카카오토큰 사용 시 여기 넣어줘야)
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

### 프로젝트 IDE 공유방법

```shell
$ pip freeze

$ pip freeze > requirements.txt
"git에 업로드"

$ pip install -r  requirements.txt
```



## 보안설정

- `my_settings.py` 파일 생성

```python
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

```

```python
## filename: settings.py

import my_settings
DATABASE = my_settings.DATABASES
```

- `settings.py`에서 설정 적용
- 추가적으로 외부 API(SNS로그인, AWS접속 정보 등)도 기록 가능


## Links

* [위키백과](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)
* [데어이즈](https://blog.thereis.xyz/41)
* [gitignore](https://blog.thereis.xyz/41)

