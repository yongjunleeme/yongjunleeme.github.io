---
layout  : wiki
title   : 
summary : 
date    : 2020-04-20 19:50:09 +0900
updated : 2020-04-24 19:16:16 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

##

```python
from django.db import models

# Create your models here.

class Fcuser(models.Model):
    username = models.CharField(max_length=32,  # verbose_name -> 어드민에 보이는 제목
                                verbose_name='사용자명')
    useremail = models.EmailField(max_length=128,
                                  verbose_name='사용자이메일')
    password = models.CharField(max_length=64,
                                verbose_name='비밀번호')
    registered_dttm = models.DateTimeField(auto_now_add=True, # -> auto_now_add -> 등록된 시간 기록
                                           verbose_name='등록시간') # dttm -> datetime

    def __str__(self):
        return self.username

    class Meta:
        db_table = 'fastcampus_fcuser'
        verbose_name = '패스트캠퍼스 사용자'
        verbose_name_plural = '패스트캠퍼스 사용자'
```

### sqlite3

```python
$ sqlite3.db.sqlite3

$ .tables

$ .schema [테이블명]

```

### 회원가입

- templates 폴더 생성
- 부트스트랩에서 CDN 검색 후 복붙

```python
# base.html

<html>

<head>
  <!-- Required meta tags -->
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

  <!-- <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" /> -->
  <link rel="stylesheet" href="/static/bootstrap.min.css" />
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
    integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous">
  </script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>
</head>

<body>
  <div class="container">
    {% block contents %}
    {% endblock %}
  </div>
</body>

</html>
```

```python
# registre.html

{% extends "base.html" %}
{% block contents %}
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>회원가입</h1>
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    {{ error }}
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    <form method="POST" action="."> # action에는 post 수행할 경로 지정
      {% csrf_token %}   # form 태그에는 csrf 명시, 크로스도메인 공격을 막기 위해 키를 숨겨놓고 검증?, 장고에서는 csrf를 안 쓰면 에
      <div class="form-group">
        <label for="username">사용자 이름</label>
        <input type="text" class="form-control" id="username" placeholder="사용자 이름" name="username" />  # input에 name 값으로 정보 전달.
      </div>
      <div class="form-group">
        <label for="useremail">사용자 이메일</label>
        <input type="email" class="form-control" id="useremail" placeholder="사용자 이메일" name="useremail" />
      </div>
      <div class="form-group">
        <label for="password">비밀번호</label>
        <input type="password" class="form-control" id="password" placeholder="비밀번호" name="password" />
      </div>
      <div class="form-group">
        <label for="re-password">비밀번호 확인</label>
        <input type="password" class="form-control" id="re-password" placeholder="비밀번호 확인" name="re-password" />
      </div>
      <button type="submit" class="btn btn-primary">등록</button>
    </form>
  </div>
</div>
{% endblock %}

```

- input에 name 변수의 값(username)을 view에서 받아서 처리

```python
# home.html

{% extends "base.html" %}

{% block contents %}
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>홈페이지!</h1>
  </div>
</div>
<div class="row mt-5">
  {% if request.session.user %}
  <div class="col-12">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/logout/'">로그아웃</button>
  </div>
  {% else %}
  <div class="col-6">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/login/'">로그인</button>
  </div>
  <div class="col-6">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/register/'">회원가입</button>
  </div>
  {% endif %}
</div>
<div class="row mt-1">
  <div class="col-12">
    <button class="btn btn-primary btn-block" onclick="location.href='/board/list/'">게시물보기</button>
  </div>
</div>
{% endblock %}

```

```python
# login.html

{% extends "base.html" %}

{% block contents %}
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>로그인</h1>
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    {{ error }}
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    <form method="POST" action=".">
      {% csrf_token %}
      {% for field in form %}
      <div class="form-group">
        <label for="{{ field.id_for_label }}">{{ field.label }}</label>
        <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
          placeholder="{{ field.label }}" name="{{ field.name }}" />
      </div>
      {% if field.errors %}
      <span style="color: red">{{ field.errors }}</span>
      {% endif %}
      {% endfor %}
      <button type="submit" class="btn btn-primary">로그인</button>
    </form>
  </div>
</div>
{% endblock %}


```


### Static 파일 관리

- [CDN](https://docs.microsoft.com/ko-kr/azure/cdn/cdn-overview)
   - CDN(콘텐츠 배달 네트워크)은 사용자에게 웹 콘텐츠를 효율적으로 제공할 수 있는 서버의 분산 네트워크입니다. CDN은 최종 사용자와 가까운 POP(point-of-presence) 위치의 Edge 서버에 캐시된 콘텐츠를 저장하여 대기 시간을 최소화합니다.

```python
# 장고 메인에 `/static` 폴더 생성 후 스테틱 파일(예제에서는 부트스트랩 CSS) 복사
# settings.py에 경로 지정

STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]
STATIC_ROOT = os.path.join(BASE_DIR, 'static')

# registre.html 파일에 추가
<link rel="stylesheet" href="/static/bootstrapp.min.css" />
```

## 로그인, 회원가입

```python
from django.http import HttpResponse
from django.shortcuts import render, redirect
from django.contrib.auth.hashers import make_password, check_password
from .models import Fcuser
from .forms import LoginForm

def home(request): 
    return render(request, 'home.html')

def logout(request):
    if request.session.get('user'):
        del(request.session['user'])

    return redirect('/')

def login(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid():
            request.session['user'] = form.user_id
            return redirect('/')
    else:
        form = LoginForm()

    return render(request, 'login.html', {'form': form})

def register(request): # request를 url과 연결하면 요청정보가 request 변수에 담긴다
    if request.method == 'GET':
        return render(request, 'register.html') # request를 같이 전달, 반환하고 싶은 html 파일 표시(templates 폴더에서 찾으므로 따로 폴더 표시 안 해도 됨)
    elif request.method == 'POST':
        username = request.POST.get('username', None) # templates 내 파일의 input에 담긴 name의 값인 username
        useremail = request.POST.get('useremail', None) # get은 'useremail'을 키로 값을 가져오는데 값이 없으면 None을 담는다, request.POST['username']에서 None처리만 추가
        password = request.POST.get('password', None)
        re_password = request.POST.get('re-password', None)

        res_data = {} # 딕셔너리로 선언

        if not (username and useremail and password and re_password):
            res_data['error'] = '모든 값을 입력해야합니다.' # 딕셔너리 키값 통해 할당
        elif password != re_password:
            res_data['error'] = '비밀번호가 다릅니다.'
        else:
            fcuser = Fcuser(  # fcuser = Fcuser() < - 모델의 클래스를 가져와서 실행
                username=username,
                useremail=useremail,
                password=make_password(password) # 장고에서 가져온 make_password
            )

            fcuser.save()

        return render(request, 'register.html', res_data) # res_data -> 데이터 전달, res_data['error']의 error 키는 templates폴더 내 html 파일에서 {{error}} 로 연결하면 그 자리에 자동 매핑

```

```python
# forms.py
from django import forms
from .models import Fcuser
from django.contrib.auth.hashers import check_password

class LoginForm(forms.Form):
    username = forms.CharField(
        error_messages={
            'required': '아이디를 입력해주세요.'
        },
        max_length=32, label="사용자 이름")
    password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요.'
        },
        widget=forms.PasswordInput, label="비밀번호")

    def clean(self):
        cleaned_data = super().clean()
        username = cleaned_data.get('username')
        password = cleaned_data.get('password')

        if username and password:
            try:
                fcuser = Fcuser.objects.get(username=username)
            except Fcuser.DoesNotExist:
                self.add_error('username', '아이디가 없습니다')
                return

            if not check_password(password, fcuser.password):
                self.add_error('password', '비밀번호를 틀렸습니다')
            else:
                self.user_id = fcuser.id
```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
