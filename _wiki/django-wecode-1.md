---
layout  : wiki
title   : django-wecode-1
summary : 
date    : 2020-02-08 13:38:23 +0900
updated : 2020-02-10 18:32:20 +0900
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

알아서 프로젝트 깔리는 두 파일
- **asgi.py** - 비동기 통신에 사용한다(예: 실시간 채팅, 웹소켓).
- **wsgi.py** - Web Server gateway Interface 파이썬 스크립트와 웹서버 통신에 쓰이는 프로토콜

> [django-channels](https://github.com/django/channels) 로 실시간 비동기 채팅을 구현할 때 웹소켓을 쓴다는 말을 듣기는 했는데 그게 `asgi.py`인가 싶다.


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

- 데이터베이스 - 한 개가 아닌 여러 개도 연결 가능

```python
AUTH_PASSWORD_VALIDATORS 
```

- auth 기능을 쓸 때 비밀번호 복잡도 등의 자릿수 검증


- 그외 설정
    - **DEBUG** - 디버깅 가능여부 설정, False로 해놓으면 404 Not Found
    - **allowed_host** - 외부접속을 허용하는 IP주소

추가 설정
```python
##Stop Warning about '/' 슬래시 경고를 안 보게
APPEND_SLASH=False
```

## models.py (user)

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

> ORM이 django에서 핵심이라고 한다. 아직은 아는 게 거의 없어서 무슨 개념이든 섣불리 말하기가 겁난다..  [여기](https://django-orm-cookbook-ko.readthedocs.io/en/latest/) 에 ORM 정리가 잘 되어 있으니 참고하면 좋을 것 같다.


## views.py (user)
```python
import json
from .models import User 

from django.views import View
from django.http import JsonResponse, HttpResponse # import 순서 1 외부 2 내코드 3 장고 내부 클래스

class UserView(View):
    def post(self, request):
        data = json.loads(request.body)
        
        if User.objects.filter(email = data['email']).exists(): 
            return JsonResponse({"message":"USER_ALREADY_EXIST"}, status=400) # 이미 존재하면 알림

        User(
            name     = data['name'],
            email    = data['email'],
            password = data['password']
        ).save()
       
            return HttpResponse(status=200) # 그렇지 않으면 성공
    
    def get(self, request):
        user_data = User.objects.values()

        return JsonResponse({'user':list(user_data)},status = 200)


class LoginView(View):
    def post(self, request):
        user_data = json.loads(request.body)
        try:
            if User.objects.filter(email = data['email']).exits():
                user = User.objects.get(email = data['email'])
                if user.password == data['password']:
                    return HttpResponse(status = 200)
                return HttpResponse(status = 401) 					# 401 Unauthorized
            return HttpResponse(status = 400)						# 400 Bad Request
        except KeyError:											# 유저 정보가 틀리거나 존재하지 않는 조건 이외에 다른 예외조건에도 리턴을 달아주어야 하는 모양
            return JsonResponse({"message":"INVALID_KEYS"}, status = 400)
```
## urls.py (user)

```python
from django.urls import path
from .views import UserView, LoginView 

urlpatterns = [
    path('', UserView.as_view()),
    path('login', LoginView.as_view()),
    ]
```
- `as_view()` 메소드는 Http 메소드가 GET인지 POST인지 DELETE인지 UPDATE인지 판별해서 그에 맞는 함수를 실행시켜 준다.

## models.py (comment)

```python
from django.db import models
 
class Comments(models.Model):
    author    = models.CharField(max_length=200)
    reply     = models.CharField(max_length=1000)
    class Meta:
         db_table='comments'
```

## views.py (comment)

```python
import json

from .models import Comments

from django.http import JsonResponse
from django.views import View

 
class CommentView(View):
    def post(self, request):
        data=json.loads(request.body)
        Comment(
            author  = data['author'],
            reply   = data['reply'],
        ).save()
        return JsonResponse({'message':'SUCCESS'}, status = 200)
    
    def get(self, request):
        comment_data = Comment.objects.values()

        return JsonResponse({'author':list(comment_data)}, status = 200)
```

## urls.py

```python
from django.urls import path
from .views import CommentView 

urlpatterns = [
        path('', CommentView.as_view()),
]
```

## [httpie](https://httpie.org/)

CLI에서 간단하게 통신 테스트를 해볼 수 있는 툴

```
# Ubuntu
    sudo apt install httpie
    
# Mac
    brew install httpie
```

```
$ http -v 'url' name='테스트용이름' email='테스트용이메일' password='비밀번호'
```
