---
layout  : wiki
title   : encryption-wecode
summary :
date    : 2020-02-13 18:06:11 +0900
updated : 2020-02-13 19:33:55 +0900
tags    : encryption 
toc     : true
public  : true
parent  : study 
latex   : false
---
* TOC
{:toc}

> [wecode](https://wecode.co.kr/) 에서 배운 인증, 인가를 위한 암호화 기초 정리 문서입니다.

## Why

암호화를 하는 이유는 직관적으로 해킹 방지를 위함이 있지만 그에 앞서 정보통신보호법에 의거해 사용자의 정보를 보호해야 할 의무가 있기 때문이라고 한다. 
웹개발에서는 로그인 시 패스워드 등에 암호화를 적용한다.

## What

종류는 단방향과 양방향으로 나뉜다. 기준은 복호화의 가능여부인데 실제적으로 단방향의 복호화가 불가능하지는 않고 비교적 어려운 편이라고 한다. 양방향은 복호화를 통해 식별할 수 있는 성격의 정보에 사용하며 대표적으로 토근이 이에 해당한다.

인증(Authentication), 인가(Authorization)
* 인증은 유저를 식별하는 절차, 인가는 유저의 권을 확인하는 절
* 로그인으로 가정하면 인증에서 암호화된 비밀번호를 식별하고 `Access token`을 생성한다. 이후 인가를 통해 적절한 권한에 따른 자원 접근을 허용한다.

비밀번호 암호화
* 유저의 비밀번호는 암호화해서 저장하며 보통 단방향 해시함수를 쓴다.
* 해시는 암호화된 메시지인 다이제스트(Digest)를 생성
* 해시는 역설적으로 보안에 취약한 부분이 있다.  본래 패스워드용이 아닌 데이터 검색용이라 공격자가 매우 빠른 속도로 임의 문자열과 비밀번호를 비교할 수 있는 것이다. 예를 들어 `MD5`는  초당 56억개의 다이제스트를 대입 가능하다. 패스워드가 길지 않거나 복잡하지 않은 경우 단시간 내에 공격을 당한다.
* 이에 `Bcrypt`는 비밀번호에 데이터를 추가하는 `Salting`이라는 방법을 쓰고 이는 디폴트로 12번 수행된다고 한다. 또한 비밀번호를  1초에 약 5번만 비교하도록 `Key Stretching`을 적용한다.

JWT(JSON Web Tokens)
`Access token`을 생성하는 기술로 유저 데이터를 암호화해서 보관.

## Bcrypt, Pyjwt

- 설치

```shell
$ pip install bcrypt
$ pip install pyjwt
```

-  파이썬에서 간단한 테스트

```shell
$ python

>>> import bcrypt
>>> password = "pass1234"
>>> bcrypt.hashpw(password, bcrypt.gensalt())	"암호화"
```

- 디코드하는 방법 2가지

```shell
>>> password.decode('utf-8')		"1번"
>>> a = bytes(password, 'utf-8')	"2번"
>>> a.decode('utf-8')
```

- 복잡하게 접근도 가능..

```shell
>>> bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())		"1번"
>>> b = bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())	"2번"
>>> b.decode('utf-8')
```

- 패스워드 체크

```shell
>>> c = "1111"
>>> bcrypt.checkpw(c.encode('utf-8'), b)
```

- JWT

```shell
>>> import jwt
>>> jwt.encode({'user_id': 1}, 'secret-key', algorithm = 'HS256')

>>> token = jwt.encode({'user_id':1}, 'secret-key', algorithm = 'HS256')
>>> jwt.decode(token, 'secret-key', algorithm = 'HS256')

>>> type(token)				"바이트"
>>> token.decode('utf-8')	"넘겨줄 때는 디코드해서"
```

## Django 회원가입 적용 (bcrypt)

```python
## filename: views.py

import bycrypt

input_data = {
	'email'		: 'aa@bb.com',
	'password'	: 'pass1234',
}

byted_password	= input_data['password'].encode('utf-8')
hashed_password = bcrypt.hashpw(byted_password, bcrypt.gensalt())

Users(
		email		= input_data['email'],
		password	= hashed_password.decode('utf-8'),
		).save()
```

`[[hashpw]]()` 메소드는 인자로 Unicode가 아닌 Byte string을 받으므로 해싱 전 인코딩이 필요하다.

1. encode - unicode     -> byte string
2. hash	  - byte string -> hashed 
3. decode - hashed		-> unicode


- 로그인뷰

```python
if User.objects.filter(email = input_data['email']).exists():
	# DB에서 유저 정보 받기
	user_in_db = User.objects.get(email = input_data['email'])
	
	# 패스워드 확인
	if bcrypt.checkpw(input_data['password'].encode('utf-8'), user_in_db.password.encode('utf-8')):
		return JsonResponse({'message': f'hi, {user_in_db.name}'}, status = 200)
	else:
		return JsonResponse({'message': 'Name or password does not match', status = 401}
```


