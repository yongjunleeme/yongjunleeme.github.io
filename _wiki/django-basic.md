---
layout  : wiki
title   : django-basic 
summary : 
date    : 2020-04-20 19:50:09 +0900
updated : 2020-05-04 15:26:02 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

{% raw %}

## django-basic

```python
from django.db import models

# Create your models here.

class Fcuser(models.Model):
    username = models.CharField(max_length=32, verbose_name='사용자명') # verbose_name -> 어드민에 보이는 제목
    useremail = models.EmailField(max_length=128, verbose_name='사용자이메일')
    password = models.CharField(max_length=64, verbose_name='비밀번호')
    registered_dttm = models.DateTimeField(auto_now_add=True, verbose_name='등록시간') # -> auto_now_add -> 등록된 시간 기록

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

## 로그인, 회원가입

### templates

- templates 폴더 생성
- 부트스트랩에서 CDN 검색 후 복붙

```python
# base.html
<html>
<head>
  <!-- Required meta tags -->
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

  <!-- <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" /> -->
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
  block contents
  endblock
  </div>
</body>
</html>
```

```html
# register.html

extends "base.html"
block contents
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>회원가입</h1>
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
     || error ||
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    <form method="POST" action="."> # action에는 post 수행할 경로 지정
       csrf_token    # form 태그에는 csrf 명시, 크로스도메인 공격을 막기 위해 키를 숨겨놓고 검증?, 장고에서는 csrf를 안 쓰면 에러
      <div class="form-group">
        <label for="username">사용자 이름</label>
        <input type="text" class="form-control" id="username" placeholder="사용자 이름" name="username" />  # input에 name 값으로 정보 전달
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
endblock
```

```html
# home.html

extends "base.html"

block contents
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>홈페이지!</h1>
  </div>
</div>
<div class="row mt-5">
  if request.session.user
  <div class="col-12">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/logout/'">로그아웃</button>
  </div>
  else
  <div class="col-6">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/login/'">로그인</button>
  </div>
  <div class="col-6">
    <button class="btn btn-primary btn-block" onclick="location.href='/fcuser/register/'">회원가입</button>
  </div>
  endif
</div>
<div class="row mt-1">
  <div class="col-12">
    <button class="btn btn-primary btn-block" onclick="location.href='/board/list/'">게시물보기</button>
  </div>
</div>
endblock

```

```html
# login.html

extends "base.html"

block contents
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>로그인</h1>
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    || error ||
  </div>
</div>
<div class="row mt-5">
  <div class="col-12">
    <form method="POST" action=".">
      csrf_token
      for field in form      # || form.as_p || - p태그, || form.as_table || - table 태그
      <div class="form-group">
        <label for="|| field.id_for_label ||">|| field.label ||</label>  # form.py에서 모델에 지정한 label 값을 가져옴, form 내부에 id_for_label이라는 변수가 있다( 해당 테이블의 id를 물고 있는 다른 필드 값을 가져오는 건가..? )
        <input type="|| field.field.widget.input_type ||" class="form-control" id="|| field.id_for_label }}" # form의 widge 내부에 input_type 이라는 변수가 있다.
          placeholder="|| field.label }}" name="|| field.name ||" />
      </div>
      if field.errors    # error가 발생하면 errors 함수 호출, forms.py에서 error_messages 설정 
      <span style="color: red">|| field.errors ||</span>
      endif
      endfor
      <button type="submit" class="btn btn-primary">로그인</button>
    </form>
  </div>
</div>
endblock
```

### Static 파일 관리

- [CDN](https://docs.microsoft.com/ko-kr/azure/cdn/cdn-overview)
   - CDN은 사용자에게 웹 콘텐츠를 효율적으로 제공할 수 있는 서버의 분산 네트워크입니다. CDN은 최종 사용자와 가까운 POP(point-of-presence) 위치의 Edge 서버에 캐시된 콘텐츠를 저장하여 대기 시간을 최소화합니다.

- [부트스트랩 테마](https://bootswatch.com/)에서 css파일 다운로드 후 static 폴더로 이동

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

### urls.py

```python
# fc_community/urls.py

from django.contrib import admin
from django.urls import path, include
from fcuser.views import home

urlpatterns = [
    path('admin/', admin.site.urls),
    path('fcuser/', include('fcuser.urls')),
    path('board/', include('board.urls')),
    path('', home), # 리다이렉트되는 홈url은 메인 디렉토리 url 내에 작성
]
```

```python
# fc_user/urls.py

from django.urls import path
from . import views

urlpatterns = [
    path('register/', views.register),
    path('login/', views.login),
    path('logout/', views.logout)
]

```

### views.py

```python
from django.http import HttpResponse
from django.shortcuts import render, redirect
from django.contrib.auth.hashers import make_password, check_password
from .models import Fcuser
from .forms import LoginForm

def home(request): # 리다이렉트되는 홈 설정
    return render(request, 'home.html')

def logout(request):
    if request.session.get('user'):
        del(request.session['user'])

    return redirect('/')

def login(request):
    if request.method == 'POST':
        form = LoginForm(request.POST)
        if form.is_valid(): # form에 빌트인 메소드 is_valid - 값 존재여부만 판단
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

        return render(request, 'register.html', res_data) # res_data -> 데이터 전달, res_data['error']의 error 키는 templates폴더 내 html 파일에서 || error ||로 연결하면 그 자리에 자동 매핑

```
### form.py

```python
# forms.py
from django import forms
from .models import Fcuser
from django.contrib.auth.hashers import check_password

class LoginForm(forms.Form):
    username = forms.CharField(
        error_messages={
            'required': '아이디를 입력해주세요.'  # required에 원하는 메시지 삽입 가능
        },
        max_length=32, label="사용자 이름")
    password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요.'
        },
        widget = forms.PasswordInput, label="비밀번호") # PasswordInput - 패스워트 ** 타입으로 변경

    def clean(self):
        cleaned_data = super().clean()  # form의 빌트인 메소드를 상속하므로 super()를 쓴다.
        username = cleaned_data.get('username')
        password = cleaned_data.get('password')

        if username and password:
            try:
                fcuser = Fcuser.objects.get(username=username)  # 앞 username은 모델의 필드명, 뒤 username은 변수 username의 post 통해 얻은 값
            except Fcuser.DoesNotExist:
                self.add_error('username', '아이디가 없습니다')
                return

            if not check_password(password, fcuser.password): # check_password 1번쨰 인자는 변수 password통해 입력받은 값, 2번째 인자는 모델에 입력된 패스워드 값
                self.add_error('password', '비밀번호를 틀렸습니다')
            else:
                self.user_id = fcuser.id
```


## 게시판

### templates

#### base.html

```python
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

#### border_detail.html

```python
{% extends "base.html" %}

{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <div class="form-group">
      <label for="title">제목</label>
      <input type="text" class="form-control" id="title" value="{{ board.title }}" readonly />
      <label for="contents">내용</label>
      <textarea class="form-control" readonly>{{ board.contents }}</textarea>
      <label for="tags">태그</label>
      <span id="tags" class="form-control">
        {{ board.tags.all|join:", " }}
      </span>
    </div>
    <button class="btn btn-primary" onclick="location.href='/board/list/'">돌아가기</button>
  </div>
</div>
{% endblock %}
```

#### border_list.html

```python
{% extends "base.html" %}

{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <table class="table table-light">
      <thead class="thead-light">
        <tr>
          <th>#</th>
          <th>제목</th>
          <th>아이디</th>
          <th>일시</th>
        </tr>
      </thead>
      <tbody class="text-dark">
        {% for board in boards %}
        <tr onclick="location.href='/board/detail/{{ board.id }}/'">
          <th>{{ board.id }}</th>
          <td>{{ board.title }}</td>
          <td>{{ board.writer }}</td>
          <td>{{ board.registered_dttm }}</td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>
</div>
<div class="row mt-2">
  <div class="col-12">
    <nav>
      <ul class="pagination justify-content-center">
        {% if boards.has_previous %}
        <li class="page-item">
          <a class="page-link" href="?p={{ boards.previous_page_number }}">이전으로</a>
        </li>
        {% else %}
        <li class="page-item disabled">
          <a class="page-link" href="#">이전으로</a>
        </li>
        {% endif %}
        <li class="page-item active">
          <a class="page-link" href="#">{{ boards.number }} / {{ boards.paginator.num_pages }}</a>
        </li>
        {% if boards.has_next %}
        <li class="page-item">
          <a class="page-link" href="?p={{ boards.next_page_number }}">다음으로</a>
        </li>
        {% else %}
        <li class="page-item disabled">
          <a class="page-link disabled" href="#">다음으로</a>
        </li>
        {% endif %}
      </ul>
    </nav>
  </div>
</div>
<div class="row">
  <div class="col-12">
    <button class="btn btn-primary" onclick="location.href='/board/write/'">글쓰기</button>
  </div>
</div>
{% endblock %}
```

#### border_writhe.html

```python
{% extends "base.html" %}

{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <form method="POST" action=".">
      {% csrf_token %}
      {% for field in form %}
      <div class="form-group">
        <label for="{{ field.id_for_label }}">{{ field.label }}</label>
        {% ifequal field.name 'contents' %}
        <textarea class="form-control" name="{{ field.name }}" placeholder="{{ field.label }}"></textarea>
        {% else %}
        <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
          placeholder="{{ field.label }}" name="{{ field.name }}" />
        {% endifequal %}
      </div>
      {% if field.errors %}
      <span style="color: red">{{ field.errors }}</span>
      {% endif %}
      {% endfor %}
      <button type="submit" class="btn btn-primary">글쓰기</button>
      <button type="button" class="btn btn-primary" onclick="location.href='/board/list/'">돌아가기</button>
    </form>
  </div>
</div>
{% endblock %}
```

### model.py

```python
from django.db import models

# Create your models here.


class Board(models.Model):
    title = models.CharField(max_length=128,
                             verbose_name='제목')
    contents = models.TextField(verbose_name='내용')
    writer = models.ForeignKey('fcuser.Fcuser', on_delete=models.CASCADE,
                               verbose_name='작성자')
    tags = models.ManyToManyField('tag.Tag', verbose_name='태그')
    registered_dttm = models.DateTimeField(auto_now_add=True,
                                           verbose_name='등록시간')

    def __str__(self):
        return self.title

    class Meta:
        db_table = 'fastcampus_board'
        verbose_name = '패스트캠퍼스 게시글'
        verbose_name_plural = '패스트캠퍼스 게시글'
```

### views.py

```python
from django.shortcuts import render, redirect
from django.core.paginator import Paginator
from django.http import Http404
from fcuser.models import Fcuser
from tag.models import Tag
from .models import Board
from .forms import BoardForm

# Create your views here.


def board_detail(request, pk):
    try:
        board = Board.objects.get(pk=pk)
    except Board.DoesNotExist:
        raise Http404('게시글을 찾을 수 없습니다')

    return render(request, 'board_detail.html', {'board': board})


def board_write(request):
    if not request.session.get('user'):
        return redirect('/fcuser/login/')

    if request.method == 'POST':
        form = BoardForm(request.POST)
        if form.is_valid():
            user_id = request.session.get('user')
            fcuser = Fcuser.objects.get(pk=user_id)

            tags = form.cleaned_data['tags'].split(',')

            board = Board()
            board.title = form.cleaned_data['title']
            board.contents = form.cleaned_data['contents']
            board.writer = fcuser
            board.save()

            for tag in tags:
                if not tag:
                    continue

                _tag, _ = Tag.objects.get_or_create(name=tag)
                board.tags.add(_tag)

            return redirect('/board/list/')
    else:
        form = BoardForm()

    return render(request, 'board_write.html', {'form': form})


def board_list(request):
    all_boards = Board.objects.all().order_by('-id') # 최신순으로 배열한다는 의미
    page = int(request.GET.get('p', 1))
    paginator = Paginator(all_boards, 3)

    boards = paginator.get_page(page)
    return render(request, 'board_list.html', {'boards': boards})
```

#### forms.py

```python
from django import forms


class BoardForm(forms.Form):
    title = forms.CharField(
        error_messages={
            'required': '제목을 입력해주세요.'
        },
        max_length=128, label="제목")
    contents = forms.CharField(
        error_messages={
            'required': '내용을 입력해주세요.'
        },
        widget=forms.Textarea, label="내용")
    tags = forms.CharField(
        required=False, label="태그")

```

#### urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('detail/<int:pk>/', views.board_detail),
    path('list/', views.board_list),
    path('write/', views.board_write)
]

```

#### admins.py

```python
from django.contrib import admin
from .models import Board

# Register your models here.


class BoardAdmin(admin.ModelAdmin):
    list_display = ('title',)


admin.site.register(Board, BoardAdmin)

```

#### apps.py

```python
from django.apps import AppConfig


class BoardConfig(AppConfig):
    name = 'board'

```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)

{% endraw %}
