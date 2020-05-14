---
layout  : wiki
title   : my-sql-ide 
summary : 
date    : 2020-02-13 19:34:52 +0900
updated : 2020-05-14 20:20:10 +0900
tags    : 
toc     : true
public  : true
parent  : toll
latex   : false
---
* TOC
{:toc}

## 설치 방법 in mac

```python
$ brew install mysql
```

* mysql 시작

```python
$ mysql server start
```

* 기본 설정

```python
$ mysql_secure_installation
```

이제 여러 질문들이 출력됩니다. 출력에 대한 답은 참고만 부탁을 드립니다.

1. 비밀번호 복잡도 검사 과정 (n)
2. 비밀번호 입력 & 확인
3. 익명 사용자 삭제 (y)
4. 원격 접속 허용 (y)
5. test DB 삭제 (y)
6. previlege 테이블을 다시 로드할 것인지 (y or n)

아래는 위에 나타낸 과정의 자세한 내용입니다.

`Securing the MySQL server deployment.
Connecting to MySQL using a blank password.
VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?
Press y|Y for Yes, any other key for No:`

위의 과정은 복잡한 비밀번호 설정을 위한 과정을 거치겠냐고 묻는 과정이며,**No**로 스킵하였습니다.

`Please set the password for root here.
New password:
Re-enter new password:`

위의 과정은 루트 비밀번호를 입력하는 과정입니다.비밀번호와 비밀번호 확인란을 입력하게 됩니다.

`By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y`

익명 사용자를 삭제할 것인지 묻습니다.**y**를 입력하였습니다.

`Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y`

원격 접속을 허용할 것인지 묻습니다,로컬에서만 개발 예정이기에 **y**를 입력했습니다.

`By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.
Remove test database and access to it? (Press y|Y for Yes, any other key for No) :`

test 데이터베이스 삭제를 묻고 있습니다.**No**를 입력하였습니다.

`Reload privilege tables now? (Press y|Y for Yes, any other key for No) :`

previlege 테이블을 다시 로드할 것인지 묻습니다.

**yes**를 입력, 과정을 마칩니다.

`All done!`

위의 메세지와 함께 설정이 종료됩니다.

추가적으로 mysql server가 재부팅과 상관없이 켜져있을 수 있도록 brew services를 이용하여 서버를 켜두겠습니다.

- 서버 on

```python
$ brew services start mysql`
```

## 장고 세팅

### `my_settings.py`

```python
DATABASES = {
    'default' : {
        'ENGINE': 'django.db.backends.mysql',    
        'NAME': 'django_insta',                  
        'USER': 'root',                          
        'PASSWORD': 'password',                  
        'HOST': 'localhost',                    
        'PORT': '3306',                        
    }
}
```

### `settings.py`

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

## 유용한 명령어

### --help

```python
$ mysql --help
```

### mysql db 장고 삽입

```python
"my_settings.py 만들고 데이터베이스 생성 후에
$ mysql -u root -p hanteo < hanteo.sql
```

### migration, db clean

```python
find . -path "*/migrations/*.py"
find . -path "*/migrations/*.py" -not -name "__init__.py" -delete

python manage.py makemigrations
python manage.py migrate

```

```python
drop database wetyle_share;
create database wetyle_share character set utf8mb4 collate utf8mb4_general_ci;
```

### 비번 없이 로그인

```python
$ sudo mysql -uroot
```
