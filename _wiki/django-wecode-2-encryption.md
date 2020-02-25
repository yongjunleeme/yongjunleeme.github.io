---
layout  : wiki
title   : django-wecode-2-encryption 
summary :
date    : 2020-02-13 18:06:11 +0900
updated : 2020-02-23 09:29:06 +0900
tags    : encryption 
toc     : true
public  : true
parent  : django 
latex   : false
---
* TOC
{:toc}

> [wecode](https://wecode.co.kr/) 에서 배운 인증, 인가를 위한 암호화 기초 정리입니다.

## Why

암호화를 하는 이유는 직관적으로 해킹 방지를 위함이지만 그에 앞서 서비스 제공자게에는 정보통신보호법에 의거한 사용자의 정보를 보호해야 할 의무가 있기 때문이라고 한다. 

## What

종류는 단방향과 양방향으로 나뉘는데 그 기준은 복호화의 가능여부다. 단방향은 복호화가 안 되고 양방향은 복호화가 된다는 것이다. 하지만 실제적으로 단방향의 복호화가 불가능하지는 않고 비교적 어려운 편이라고 한다. 양방향은 복호화를 통해 식별할 수 있는 성격의 정보에 사용하며 대표적으로 토근이 이에 해당한다.

* 인증(Authentication), 인가(Authorization)
	* 인증은 유저를 식별하는 절차, 인가는 유저의 권한을 확인하는 절차
	* 로그인으로 가정하면 인증에서 암호화된 비밀번호를 식별하고 `Access token`을 생성한다. 이후 인가를 통해 적절한 권한에 따른 자원 접근을 허용한다.

* 비밀번호 암호화
	* 유저의 비밀번호는 암호화해서 저장하며 보통 단방향 해시함수를 쓴다.
	* 해시는 암호화된 메시지인 다이제스트(Digest)를 생성
	* 보통 암호화에 사용하는 기술은 `bcrypt`다. 이는 역설적으로 보안에 취약한 부분이 있다.  본래 패스워드용이 아닌 데이터 검색용이라 공격자가 매우 빠른 속도로 임의 문자열과 비밀번호를 비교할 수 있는 것이다. 예를 들어 `MD5`는 초당 56억개의 다이제스트를 대입 가능하다. 패스워드가 길지 않거나 복잡하지 않은 경우 단시간 내에 공격을 당한다.
	* 이에 `bcrypt`는 비밀번호에 데이터를 추가하는 `Salting`이라는 방법을 쓰고 이는 비밀번호를 계속 바꿔주는(?) 것이며  디폴트로 12번 수행된다고 한다. 또한 비밀번호를 1초에 약 5번만 비교할 수 있도록 `Key Stretching`을 적용한다.

> 적어놓고 보니 내가 봐도 무슨 말인지 잘 모르겠다.. [네이버 기술블로그](https://d2.naver.com/helloworld/318732) 에 보다 정확하고 상세하게 설명돼 있다.
    
* JWT(JSON Web Tokens)     
	* `Access token`을 생성하는 기술
	* Http의 두 가지 중요한 요소(1.Stateless 2.Request and Response)를 토대로 Http는 통신 시 상태를 저장하지 않는다. 이에 요청을 할 때 필요한 정보를 패킷에 포함해서 보내야 한다. 로그인을 예로 들면 헤더에 토큰값을 넣어서 요청하는 것이다. 
	* Json 기반 웹토큰은 크게 3부분으로 나뉜다. 1.토큰 2.페이로드 3.시그니처다. 이 중 시그니처만 암호화돼 있어서 서버만 복호화가 가능하고 나머지는 단순 문자 변경으로만 이뤄져 있다.

## Bcrypt, Pyjwt

- 설치

```shell
$ pip install bcrypt
$ pip install pyjwt
```

### bcrypt 암호화

- 파이썬에서 간단한 테스트

```shell
$ python

>>> import bcrypt
>>> password = "pass1234"
>>> bcrypt.hashpw(password, bcrypt.gensalt())	
```

- `TypeError("Unicode-objects must be encoded before hashing")`가 나온다.
- `hashpw()` 메소드는 인자로 Unicode가 아닌 Byte string을 받으므로 해싱 전 인코딩이 필요하다.

- 순서

```
1. encode - unicode -> byte string 암호화 기계에 넣기 전 인코딩
2. hash	  - byte string -> hashed  암호화 기계 작동
3. decode - hashed -> unicode 암호화 기계에서 꺼내 쓸 때는 디코딩
```

```shell
>>> pw = bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())
```

- 인코딩한 다음 pw를 출력해보면 아래와 같이 출력된다.
- `b$2b$12$WbDONrDW4TATkxwYVstr0.Ok.nRqvbIXK7nRLqDLioYdLAQsXim4a`

> 보다 정확하고 상세한 설명은 [ruanbekker 블로그](https://blog.ruanbekker.com/blog/2018/07/04/salt-and-hash-example-using-python-with-bcrypt-on-alpine/) 에 나와 있다.

### jwt 토큰생성

```shell
>>> import jwt
>>> token = jwt.encode({'user_id':1}, 'secret-key', algorithm = 'HS256')
```

- `b'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2l0IjoxfQ.wMdl_bMTySIUArv0BUiASktsa1HAl8Al4A_3jHJ2e5w'` 를 반환
- 인자순서는 유저데이터, 키 이름, 알고리즘 종류

```shell
>>> jwt.decode(token, 'secret-key', algorithm = 'HS256') 
```

- 프론트로 넘겨줄 때는 다시 디코딩해서

## Django 적용 

### Secret Key 파일 분리

```python
## filename : mysite/secret.json
{
    "FILENAME"       : "secret.json",
    "SECRET_KEY"     : "secret key",
    "DATABASE_HOST"  : "127.0.0.1",
    "PORT"           : "8000"
}
```

- `secret.json` 파일로 생성해서 따로 관리
- 암호화 인증에 사용되는 키이므로 외부나 깃에 노출되지 않도록 주의

```python
## filename: settings.py

import  json
from    django.core.exceptions import ImproperlyConfigured

with open("mysite/secret.json") as sk:
    secrets = json.loads(sk.read())

def get_secret(setting, secrets=secrets):
    try:
        return secrets[setting]
    except KeyError:
        error_msg = f"Set the {setting} enviroment variable"
        raise ImproperlyConfigured(error_msg)

SECRET_KEY = get_secret("SECRET_KEY")
```

- `settings.py`에 위와 같은 함수를 추가하면 `mysite/seret.json`에 저장한 `secret.json` 파일을 불러와 보안 걱정 없이 사용 가능

> [김광훈님](https://wave1994.tistory.com/64) 블로그를 참고했다.


### SignupView 회원가입 (bcrypt)

```python
## filename: views.py

import bcrypt
import jwt
import json

from   insta.settings      import SECRET_KEY
from   .models             import User

from   django.views        import View
from   django.http         import JsonResponse, HttpResponse

class SignupView(View):
    def post(self, request):
        user_data = json_loads(request.body)
        try:
            if User.objects.filter(email = user_data['email']).exists():
                return HttpResponse(status = 400)

            byted_password = user_data['password'].encode('utf-8')
            hashed_password = bcrypt.hashpw(byted_password, bcrypt.gensalt())
            password = hashed_password.decode('utf-8')
            
            User(
		        email = user_data['email'],
		        password = password 
		    ).save()
            
            return HttpRespone(status = 200)
        
        except KeyError:
            return JsonResponse({"message": "INVALID_KEY"}, status = 400)
```

### SigninView 로그인 (bcrypt.check)

- `bcrypt.check` 메소드로 유저가 입력한 비밀번호를 암호화하고 이를 암호화된 비밀번호와 일치하는지 체크한다. 유저의 비밀번호가 암호화되지 않은 상태로 저장되거나 비교되는 것이 아니다.

```python
class SignInView(View):
    def post(self, request):
        user_data = json.loads(request.body)
        
        try:
            if User.objects.filter(email = user_data['email']).exists():
                user = User.objects.get(email = user_data['email'])
                
                if bcrypt.checkpw(user_data['password'].encode('utf-8'), user.password.encode('utf-8')):
                
                    token = jwt.encode({'user': user.id}, SECRET_KEY, algorithm = 'HS256')
                    token = token.decode('utf-8')
                
                    return JsonResponse({"token" : token}, status = 200)
                
                else:
                    return HttpResponse(status = 401)
            return HttpResponse(status = 400)
        
        except KeyError:
            return JsonResponse({"message": "INVALID_KEY"}, status = 400)
```

### TokenView 토큰확인 (jwt)

```python
class TokenCheckView(View):
    def post(self,request):
        user_data = json.loads(request.body)

        user_token_info = jwt.decode(user_data['token'], SECRET_KEY, algorithm = 'HS256') 

        if User.objects.filter(email=user_token_info['user']).exists():
            return HttpResponse(status=200)

        return HttpResponse(status=403) # 권한 없어 거절
```

> [이종민님 블로그](https://velog.io/@devmin/Django-login-crypt-token-basic-v1.2) 를 참고했다. 이곳에 더 상세하고 깔끔한 설명이 나와 있다.

### 인증 데코레이터

- 발행한 토큰을 헤더에 넣어서 요청하면 로그인 인증, 유지

```python
## filename: core/utils.py (보통은 account 앱 폴더 내 위치 )

import jwt
import json

from django.http import JsonResponse
from django.core.exceptions	import ObjectDoesNotExist

from insta.settings import SECRET_KEY
from user.models import User

def login_required(func):

	def wrapper(self, request, *args, **kwargs):
	    access_token = request.headers.get('Authorization', None)	
        
        if access_token:
            try:
                payload = jwt.decode(access_token, SECRET_KEY, algorithms = ['HS256'])
                user_id = decode.get('user', None)
                user = User.objects.get(id = user_id)
                request.user = user 
            except jwt.DecodeError:
                return JsonResponse({'message': 'INVALID_TOKEN'}, status = 403) 
            except User.DoesNotExist:
                return JsonResponse({'message': 'INVALID_USER'}, status = 401)
            
            return func(self, request, *args, **kwargs)
        
        return JsonResponse(status = 401)
	
	return wrapper
```		

- 데코레이터 파일을 따로 생성. 다른 앱에서 import할 가능성이 있기 때문
- `ObjectDoesNotExist` 는 에러처리 용도
- `try`
	- `access_token` - 값이 있으면 Authorization에서 가져오고? 없으면 None
	- [payload](https://ko.wikipedia.org/wiki/%ED%8E%98%EC%9D%B4%EB%A1%9C%EB%93%9C_(%EC%BB%B4%ED%93%A8%ED%8C%85)) - 유저 인증을 위해 사용할 토큰을 payload에 담는다
	- `request.user` - 요청에 유저 인증를 담아서 활용
- `DecodeError` - 토큰 불일치인데 DecodeError?
- `DoesNotExist` - import를 `ObjectDoesNotExist`로 했지만 사용할 때는 이렇게 해야 한다고 함.

### CommentView (데코레이터)

```python
import json

from   .models		import Comment
from   core.utils	import login_required

from   django.views	import View
from   django,http	import JsonResponse, HttpResponse

class CommentView(View):
	@login_required
	def get(self, request):
		comment_data = Comment.objects.values()
		return JsonResponse({'comment_list': list(comment_data)})
		
	@login_required
	def post(self, request):
		comment_data = json.loads(request.body)
		
		Comment(
			email	= request.user.email,
			comment = comment_data['comment']
		).save()
		
		return HttpResponse(status = 200)
```

- `def post`
	- `Comment(email = request.user.email)` - `utils.py`에서 `request.user`에 담은 것과 같이 여기서도 이렇게 사용하나 봄
	 
## 데이터베이스 연결 (mysql)

```python
## filename: my_settings.py

DATABASES = {
	'default' : {
		'ENGINE'   : 'django.db.backends.mysql',
		'NAME'	   : 'test',
		'USER'	   : 'root',
		'PASSWORD' : '1111',
	}
}
```

```python
filename: settings.py

# 기존 데이터베이스 지우고
DATABASES = my_settings.DATABASES
```

# Links 

[김광훈님 블로그](https://wave1994.tistory.com/64)     

[이종민님 블로그](https://velog.io/@devmin/bcrypt-jwt-basic)    

[네이버 기술 블로그](https://d2.naver.com/helloworld/318732)


