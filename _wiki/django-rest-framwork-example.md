---
layout  : wiki
title   : 
summary : 
date    : 2020-05-15 16:37:54 +0900
updated : 2020-05-15 17:08:15 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## [django rest framework api guige](https://www.django-rest-framework.org/)

- serializer
    - 데이터 검증, 모델 데이터 전달(forms와 비슷)

### ProducList, ProductDetail

#### settinsg.py

```shell
$ pip install djangorestframework
```


```python
INSTALLED_APPS = [
    'rest_framework'
]
```

#### serializsers.py

```python
from rest_framework import serializers
from .models import Produc

class ProductSerializer(serializers.ModelSerializer):

    class Meta:
        model = Product
        fields = '__all__'
```

#### views.py

```python
from rest_framework import generics
from rest_framework import mixins

class ProductListAPI(generics.GenericAPIView, mixins.ListModelMixin):
    serializer_class = ProductSerializer # 유효성 검사

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
```

#### urls.py

```python
from product.views import (
    ProductListAPI, ProductDetailAPI
)

urlpatterns = [
    path('api/product/', ProductListAPI.as_view()),
    path('api/product/<int:pk>/', ProductDetailAPI.as_view())
]
```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)



