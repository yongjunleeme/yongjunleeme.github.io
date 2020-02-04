---
layout  : wiki
title   : django-mozilla-4 
summary : 
date    : 2020-02-02 21:59:55 +0900
updated : 2020-02-02 22:02:38 +0900
tags    : django
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}


> [Moziila Django Tutorial Part 4: Django admin site](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Admin_site) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 지적과 피드백 대환영입니다


## Overview
모델을 관리자 어플리케이션에 추가하기 위해서 그것들을 등록(register)해야 한다. 모델을 등록한 뒤로는 새로운 "superuser"를 만들어서 사이트에 로그인을 하며, 이후에는 books, 저자, book instances 그리고 장르를 만들 것이다. 이것들은 다음 튜토리얼에서 만들기 시작할 뷰와 템플릿을 테스트할 때 유용할 것.

## Models 등록하기

```python
filename: /locallibrary/catalog/admin.py 

from django.contrib import admin
from catalog.models import Author, Genre, Book, BookInstance

admin.site.register(Book)
admin.site.register(Author)
admin.site.register(Genre)
admin.site.register(BookInstance)
```
이 코드는 모델들을 import하고 이를 등록하기 위해 `admin.site.register`를 호출한다.

## Superuser 만들기

```python
$ python3 manage.py createsuperuser
$ python3 manage.py runserver
```

## 관리자 계정에 로그인하기

(이곳)[https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Admin_site]에서 따라하는 것이 좋을 듯.

## 추가 설정(Advanced configuration)