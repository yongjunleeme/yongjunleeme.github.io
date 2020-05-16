---
layout  : wiki
title   : 
summary : 
date    : 2020-05-11 15:54:23 +0900
updated : 2020-05-15 16:04:01 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

{% raw %}

## fc_django

### 외부 

- [부트스트랩 4 summernote](https://summernote.org/getting-started/#for-bootstrap-4)

### wsgi.py

```python
import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'fc_django.settings')

application = get_wsgi_application()
```

### settings.py

```python
INSTALLED_APPS = [
    'django.contrib.humanize'  # 템플릿에서 필터 사용, built-in template tagsand filters로 검색
]
```

### urls.py

```python
from django.contrib import admin
from django.urls import path
from fcuser.views import index, logout, RegisterView, LoginView
from product.views import (
    ProductList, ProductCreate, ProductDetail,
    ProductListAPI, ProductDetailAPI
)
from order.views import OrderCreate, OrderList

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', index),
    path('logout/', logout),
    path('register/', RegisterView.as_view()),
    path('login/', LoginView.as_view()),
    path('product/', ProductList.as_view()),
    path('product/<int:pk>/', ProductDetail.as_view()),
    path('product/create/', ProductCreate.as_view()),
    path('order/', OrderList.as_view()),
    path('order/create/', OrderCreate.as_view()),

    path('api/product/', ProductListAPI.as_view()),
    path('api/product/<int:pk>/', ProductDetailAPI.as_view())
]
```

## fcuser

### models.py

```python
from django.db import models

# Create your models here.

class Fcuser(models.Model):
    email = models.EmailField(verbose_name='이메일')
    password = models.CharField(max_length=128, verbose_name='비밀번호')
    level = models.CharField(max_length=8, verbose_name='등급',
        choices=(
            ('admin', 'admin'),
            ('user', 'user')
        ))
    register_date = models.DateTimeField(auto_now_add=True, verbose_name='등록날짜')

    def __str__(self):
        return self.email

    class Meta:
        db_table = 'fastcampus_fcuser'
        verbose_name = '사용자'
        verbose_name_plural = '사용자'
```

### decorator.py

```python
from django.shortcuts import redirect
from .models import Fcuser

def login_required(function):
    def wrap(request, *args, **kwargs): #원래 데코레이터를 받는 dispatch 함수의 인자가 dispatch(request, *args, **kwargs)이므로 맞춰줘야 함
        user = request.session.get('user')
        if user is None or not user: # user가 None이거나 없거나
            return redirect('/login')
        return function(request, *args, **kwargs)

    return wrap


def admin_required(function):
    def wrap(request, *args, **kwargs):
        user = request.session.get('user')
        if user is None or not user:
            return redirect('/login')
        
        user = Fcuser.objects.get(email=user)
        if user.level != 'admin':
            return redirect('/')

        return function(request, *args, **kwargs)

    return wrap
```

### forms.py

```python
from django import forms
from django.contrib.auth.hashers import check_password
from .models import Fcuser

class RegisterForm(forms.Form):
    email = forms.EmailField(
        error_messages={
            'required': '이메일을 입력해주세요.'
        },
        max_length=64, label='이메일'
    )
    password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요.'
        },
        widget=forms.PasswordInput, label='비밀번호'
    )
    re_password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요.'
        },
        widget=forms.PasswordInput, label='비밀번호 확인'
    )

    def clean(self):
        cleaned_data = super().clean()
        email = cleaned_data.get('email')
        password = cleaned_data.get('password')
        re_password = cleaned_data.get('re_password')

        if password and re_password:
            if password != re_password:
                self.add_error('password', '비밀번호가 서로 다릅니다.')
                self.add_error('re_password', '비밀번호가 서로 다릅니다.')


class LoginForm(forms.Form):
    email = forms.EmailField(
        error_messages={
            'required': '이메일을 입력해주세요.'
        },
        max_length=64, label='이메일'
    )
    password = forms.CharField(
        error_messages={
            'required': '비밀번호를 입력해주세요.'
        },
        widget=forms.Textarea, label='비밀번호'
    )

    def clean(self):
        cleaned_data = super().clean()
        email = cleaned_data.get('email')
        password = cleaned_data.get('password')

        if email and password:
            try:
                fcuser = Fcuser.objects.get(email=email)
            except Fcuser.DoesNotExist:
                self.add_error('email', '아이디가 없습니다')
                return

            if not check_password(password, fcuser.password):
                self.add_error('password', '비밀번호를 틀렸습니다')
```

### views.py

```python
from django.shortcuts import render, redirect
from django.views.generic import DetailView
from django.views.generic.edit import FormView
from django.contrib.auth.hashers import make_password
from .forms import RegisterForm, LoginForm
from .models import Fcuser


# Create your views here.

def index(request):
    return render(request, 'index.html', { 'email': request.session.get('user') })


class RegisterView(FormView):
    template_name = 'register.html'
    form_class = RegisterForm
    success_url = '/' # 성공하면 자동 이동

    def form_valid(self, form):
        fcuser = Fcuser(
            email=form.data.get('email'),
            password=make_password(form.data.get('password')),
            level='user'
        )
        fcuser.save()

        return super().form_valid(form) # 기존의 form_valid()를 상속?


class LoginView(FormView):
    template_name = 'login.html'
    form_class = LoginForm
    success_url = '/'

    def form_valid(self, form):
        self.request.session['user'] = form.data.get('email')

        return super().form_valid(form)

def logout(request):
    if 'user' in request.session:
        del(request.session['user'])

    return redirect('/')
```

### admin.py

```python
from django.contrib import admin
from .models import Fcuser

# Register your models here.

class FcuserAdmin(admin.ModelAdmin):
    list_display = ('email', )

admin.site.register(Fcuser, FcuserAdmin)
```

### templates

#### base.html

```html
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
  </script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
    integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous">
  </script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
    integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous">
  </script>
  {% block header %}
  {% endblock %}
</head>

<body>
  <div class="container">
    {% block contents %}
    {% endblock %}
  </div>
</body>

</html>
```

#### index.html

```html
{% extends "base.html" %}
{% block contents %}
Hello world!
{{ email }}
{% endblock %}
```

#### login.html

```html
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

#### register.html

```html
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
      <button type="submit" class="btn btn-primary">회원가입</button>
    </form>
  </div>
</div>
{% endblock %}
```

## product

### models.py

```python
from django.db import models

# Create your models here.

class Product(models.Model):
    name = models.CharField(max_length=256, verbose_name='상품명')
    price = models.IntegerField(verbose_name='상품가격')
    description = models.TextField(verbose_name='상품설명')
    stock = models.IntegerField(verbose_name='재고')
    register_date = models.DateTimeField(auto_now_add=True, verbose_name='등록날짜')

    def __str__(self):
        return self.name

    class Meta:
        db_table = 'fastcampus_product'
        verbose_name = '상품'
        verbose_name_plural = '상품'
```

### serializers.py

```python
from rest_framework import serializers
from .models import Product


class ProductSerializer(serializers.ModelSerializer):

    class Meta:
        model = Product
        fields = '__all__'
```

### forms.py

```python
from django import forms
from .models import Product


class RegisterForm(forms.Form):
    name = forms.CharField(
        error_messages={
            'required': '상품명을 입력해주세요.'
        },
        max_length=64, label='상품명'
    )
    price = forms.IntegerField(
        error_messages={
            'required': '상품가격을 입력해주세요.'
        }, label='상품가격'
    )
    description = forms.CharField(
        error_messages={
            'required': '상품설명을 입력해주세요.'
        }, label='상품설명'
    )
    stock = forms.IntegerField(
        error_messages={
            'required': '재고를 입력해주세요.'
        }, label='재고'
    )

    def clean(self):
        cleaned_data = super().clean()
        name = cleaned_data.get('name')
        price = cleaned_data.get('price')
        description = cleaned_data.get('description')
        stock = cleaned_data.get('stock')

        if not (name and price and description and stock):
            self.add_error('name', '값이 없습니다')
            self.add_error('price', '값이 없습니다')
```

### views.py

```python
from django.shortcuts import render
from django.views.generic import ListView, DetailView
from django.views.generic.edit import FormView
from django.utils.decorators import method_decorator
from rest_framework import generics
from rest_framework import mixins

from fcuser.decorators import admin_required
from .models import Product
from .forms import RegisterForm
from .serializers import ProductSerializer
from order.forms import RegisterForm as OrderForm

# Create your views here.

class ProductListAPI(generics.GenericAPIView, mixins.ListModelMixin):
    serializer_class = ProductSerializer

    def get_queryset(self):
        return Product.objects.all().order_by('id')

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)


class ProductDetailAPI(generics.GenericAPIView, mixins.RetrieveModelMixin):
    serializer_class = ProductSerializer

    def get_queryset(self):
        return Product.objects.all().order_by('id')

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)


class ProductList(ListView):
    model = Product
    template_name = 'product.html'
    context_object_name = 'product_list'

@method_decorator(admin_required, name='dispatch')
class ProductCreate(FormView):
    template_name = 'register_product.html'
    form_class = RegisterForm
    success_url = '/product/'

    def form_valid(self, form):
        product = Product(
            name=form.data.get('name'),
            price=form.data.get('price'),
            description=form.data.get('description'),
            stock=form.data.get('stock')
        )
        product.save()
        return super().form_valid(form)

class ProductDetail(DetailView):
    template_name = 'product_detail.html'
    queryset = Product.objects.all()
    context_object_name = 'product'

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['form'] = OrderForm(self.request)
        return context
```

### admin.py

```python
from django.contrib import admin
from .models import Product

# Register your models here.

class ProductAdmin(admin.ModelAdmin):
    list_display = ('name', 'price')

admin.site.register(Product, ProductAdmin)
```

### templates

#### product.html

```html
{% extends "base.html" %}
{% load humanize %} # | 필터 사용 가능하게 만듦
{% block header %}
<script>
  function product_detail(id) {
    $.ajax({
      url: "/api/product/" + id,
      success: function (result) {
        $("#product-" + id).popover({
          html: true,
          content: result.name + "<br/>" + result.price
        }).popover('show');
      }
    });
  }

  function product_leave(id) {
    $("#product-" + id).popover('hide');
  }
  $(document).ready(function () {});
</script>
{% endblock %}
{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <table class="table table-light">
      <thead class="thead-light">
        <tr>
          <th scope="col">#</th>
          <th scope="col">상품명</th>
          <th scope="col">가격</th>
          <th scope="col">등록날짜</th>
        </tr>
      </thead>
      <tbody class="text-dark">
        {% for product in product_list %}
        <tr>
          <th scope="row">{{ product.id }}</th>
          <th><a id="product-{{ product.id }}" onmouseenter="product_detail({{ product.id }});"
              onmouseleave="product_leave({{ product.id }});" href="/product/{{ product.id }}">{{ product.name }}</a>
          </th>
          <th>{{ product.price|intcomma }} 원</th>
          <th>{{ product.register_date|date:'Y-m-d H:i' }}</th>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>
</div>

{% endblock %}
```

#### product_detail.html

```html
{% extends "base.html" %}
{% load humanize %}
{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <div class="card" style="width: 100%;">
      <div class="card-body">
        <h5 class="card-title">{{ product.name }}</h5>
      </div>
      <ul class="list-group list-group-flush">
        <li class="list-group-item">
          <form method="POST" action="/order/create/">
            {% csrf_token %}
            {% for field in form %}
            <div class="form-group">
              {% ifnotequal field.name 'product' %}
              <label for="{{ field.id_for_label }}">{{ field.label }}</label>
              {% endifnotequal %}
              <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
                placeholder="{{ field.label }}" name="{{ field.name }}"
                value="{% ifequal field.name 'product' %}{{product.id}}{% endifequal %}" />
            </div>
            {% if field.errors %}
            <span style="color: red">{{ field.errors }}</span>
            {% endif %}
            {% endfor %}
            <button type="submit" class="btn btn-primary">주문하기</button>
          </form>
        </li>
        <li class="list-group-item">가격: {{ product.price|intcomma }} 원</li>
        <li class="list-group-item">등록날짜: {{ product.register_date|date:'Y-m-d H:i' }}</li>
        <li class="list-group-item">재고: {{ product.stock|intcomma }} 개</li>
        <li class="list-group-item">{{ product.description|safe }}</li>
      </ul>
    </div>
  </div>
  <div class="row">
    <div class="col-12">
      <a href="/product/">목록보기</a>
    </div>
  </div>

  {% endblock %}
```

#### register_product.html

```html
{% extends "base.html" %}
{% block header %}
<link href="https://cdnjs.cloudflare.com/ajax/libs/summernote/0.8.12/summernote-bs4.css" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/summernote/0.8.12/summernote-bs4.js"></script>
<script>
  $(document).ready(function () {
    $('#id_description').summernote({ # 위즈윅 에디터 등록, id의 description 필드
      height: 300
    });
  });
</script>
{% endblock %}
{% block contents %}
<div class="row mt-5">
  <div class="col-12 text-center">
    <h1>상품 생성하기</h1>
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
        {% ifequal field.name 'description' %}
        <textarea class="form-control" name="{{ field.name }}" id="{{ field.id_for_label }}"></textarea>
        {% else %}
        <input type="{{ field.field.widget.input_type }}" class="form-control" id="{{ field.id_for_label }}"
          placeholder="{{ field.label }}" name="{{ field.name }}" />
        {% endifequal %}
      </div>
      {% if field.errors %}
      <span style="color: red">{{ field.errors }}</span>
      {% endif %}
      {% endfor %}
      <button type="submit" class="btn btn-primary">생성</button>
    </form>
  </div>
</div>
{% endblock %}
```

## order

### models.py

```python
from django.db import models

# Create your models here.

class Order(models.Model):
    fcuser = models.ForeignKey('fcuser.Fcuser', on_delete=models.CASCADE, verbose_name='사용자')
    product = models.ForeignKey('product.Product', on_delete=models.CASCADE, verbose_name='상품')
    quantity = models.IntegerField(verbose_name='수량')
    register_date = models.DateTimeField(auto_now_add=True, verbose_name='등록날짜')

    def __str__(self):
        return str(self.fcuser) + ' ' + str(self.product)

    class Meta:
        db_table = 'fastcampus_order'
        verbose_name = '주문'
        verbose_name_plural = '주문'
```

### forms.py

```python
from django import forms
from .models import Order
from product.models import Product
from fcuser.models import Fcuser
from django.db import transaction


class RegisterForm(forms.Form):
    def __init__(self, request, *args, **kwargs): # request를 만들어주는 방법
        super().__init__(*args, **kwargs)
        self.request = request

    quantity = forms.IntegerField(
        error_messages={
            'required': '수량을 입력해주세요.'
        }, label='수량'
    )
    product = forms.IntegerField(
        error_messages={
            'required': '상품설명을 입력해주세요.'
        }, label='상품설명', widget=forms.HiddenInput
    )

    def clean(self):
        cleaned_data = super().clean()
        quantity = cleaned_data.get('quantity')
        product = cleaned_data.get('product')

        if not (quantity and product):
            self.add_error('quantity', '값이 없습니다')
            self.add_error('product', '값이 없습니다')
```

### views.py

```python
from django.shortcuts import render, redirect
from django.views.generic.edit import FormView
from django.views.generic import ListView
from django.utils.decorators import method_decorator
from fcuser.decorators import login_required
from django.db import transaction
from .forms import RegisterForm
from .models import Order
from product.models import Product
from fcuser.models import Fcuser

# Create your views here.

@method_decorator(login_required, name='dispatch') # decorator 원래는 class 내 dispatch 함수에 써야 하지만 장고에서 decorator 직접 쓰도록 지원
class OrderCreate(FormView):
    form_class = RegisterForm
    success_url = '/product/'

    def form_valid(self, form):
        with transaction.atomic():
            prod = Product.objects.get(pk=form.data.get('product'))
            order = Order(
                quantity=form.data.get('quantity'),
                product=prod,
                fcuser=Fcuser.objects.get(email=self.request.session.get('user'))
            )
            order.save()
            prod.stock -= int(form.data.get('quantity'))
            prod.save()

        return super().form_valid(form)

    def form_invalid(self, form):
        return redirect('/product/' + str(form.data.get('product')))

    def get_form_kwargs(self, **kwargs):
        kw = super().get_form_kwargs(**kwargs)
        kw.update({
            'request': self.request
        })
        return kw

@method_decorator(login_required, name='dispatch')
class OrderList(ListView):
    template_name = 'order.html'
    context_object_name = 'order_list'

    def get_queryset(self, **kwargs): # 쿼리셋 오버라이딩해서 쓰는 방법
        queryset = Order.objects.filter(fcuser__email=self.request.session.get('user'))
        return queryset
```

### admin.py

```python
from django.contrib import admin
from .models import Order

# Register your models here.

class OrderAdmin(admin.ModelAdmin):
    list_display = ('fcuser', 'product')

admin.site.register(Order, OrderAdmin)
```

### templates

#### order.html

```html
{% extends "base.html" %}
{% load humanize %} 
{% block contents %}
<div class="row mt-5">
  <div class="col-12">
    <table class="table table-light">
      <thead class="thead-light">
        <tr>
          <th scope="col">#</th>
          <th scope="col">상품명</th>
          <th scope="col">수량</th>
          <th scope="col">주문날짜</th>
        </tr>
      </thead>
      <tbody class="text-dark">
        {% for order in order_list %}
        <tr>
          <th scope="row">{{ order.id }}</th>
          <th>{{ order.product }}</th>
          <th>{{ order.quantity|intcomma }} 개</th>
          <th>{{ order.register_date|date:'Y-m-d H:i' }}</th>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>
</div>

{% endblock %}
```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)

{% endraw %}
