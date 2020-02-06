---
layout  : wiki
title   : django-mozilla-4 
summary : 
date    : 2020-02-02 21:59:55 +0900
updated : 2020-02-05 13:05:57 +0900
tags    : django
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}


> [Moziila Django Tutorial Part 4: Django admin site](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Admin_site) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 지적과 피드백 대환영입니다.


## Overview
모델을 관리자 어플리케이션에 추가하기 위해서 등록(register)을 해야 한다. 모델을 등록한 뒤로는 새로운 "superuser"를 만들어서 사이트에 로그인을 하며, 이후에는 books, 저자, book instances 그리고 장르를 만들 것이다. 이것들은 다음 튜토리얼에서 만들기 시작할 뷰와 템플릿을 테스트할 때 유용할 것이다.

## Models 등록하기

```python
## filename: /locallibrary/catalog/admin.py 

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

> [이곳](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Admin_site) 에서 따라하는 것이 좋을 듯합니다.

> TMI지만 명령어 설정을 alias로 설정해두면 시간 절약이 됩니다. 예를 들어 `.zshrc` 파일(각자 개발환경에 따라 다르겠지만)에 아래와 같이 추가해 놓는 식입니다.

```
alias dr='python manage.py runserver'
alias dmm='python manage.py makemigrations'
alias dm='python manage.py makemigrate'
alias js='bundle exec jekyll serve'
```

## 추가 설정(Advanced configuration)

- 각 모델은 `__str__()` 메소드로 생성된 문자열로 식별되고 편집을 위한 세부정보(detail views)와 양식(forms)에 링크된 개별 레코드들의 목록을 가지고 있다. 기본적으로 이 화면은 레코드에 대한 대량삭제 작업을 수행할 수 있는 액션 메뉴를 상단에 가지고 있다.
- 레코드의 편집과 추가를 위한 모델 디테일 레코드 양식들은 모델 안의 모든 필드를 포함하고 있고 그것들의 선언 순서에 따라 수직으로 배치돼 있다.
    
사용 편의를 위해 인터페이스를 추가적으로 사용자화할 수 있다.
* 목록 뷰(List views)
    * 레코드에 표시되는 필드/정보 추가
    * 날짜나 다른 선택 값에 기초해 어떤 레코드들을 목록으로 만들지 선택하는 필터 추가
    * 목록 뷰 안의 동작 메뉴(actions menu)에 옵션을 추가하고 이 메뉴 위치 선택
* 세부 뷰(Detail views)
    * 표시할(혹은 제외할) 필드, 따를 순서, 그루핑, 편집가능 여부, 위젯, 방향 등 선택
    * 인라인 편집 기능을 넣기 위해 관련된 필드들을 레코드에 추가

[관리자 사이트 사용자화 레퍼런스](https://docs.djangoproject.com/en/2.0/ref/contrib/admin/)

## ModelAdmin 클래스 등록하기

관리자 인터페이스에서 보이는 방식을 바꾸고 싶다면 [ModelAdmin](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#modeladmin-objects) 클래스를 정의한 후 모델 안에 등록


```python
## filename: /locallibrary/catalog/admin.py

# admin.site.register(Author)

# Define the admin class
class AuthorAdmin(admin.ModelAdmin):
    pass

# Register the admin class with the associated model
admin.site.register(Author, AuthorAdmin)

```

원본 등록(registrations) 주석 처리

```python
# admin.site.register(Book)
# admin.site.register(BookInstance)
```

`@register` 데코레이터(decorator)를 사용(이것은 `admin.site.register()` 구문과 정확히 똑같다.

```python
# Register the Admin classes for Book using the decorator
@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass

# Register the Admin classes for BookInstance using the decorator
@admin.register(BookInstance) 
class BookInstanceAdmin(admin.ModelAdmin):
    pass
```

이제 모델 고유의 관리자 행동을 정의하기 위해 이들을 확장할 수 있다.

## 목록 뷰(list view)들을 설정하기

```python
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
```

목록에 선언될 이름들은 필요한 순서대로 튜플(tuple)로 선언

```python
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')
```

위 버전으로 BookAdmin 클래스를 대체한다. author는 Foreignkey 필드 관계(일-대-다)이기에, 관련된 레코드의 `__str__()`값에 의해 나타난다.
genre 필드는 ManyToManyField이기 때문에 list_display에 직접적으로 특정할 수 없다(거대한 데이터베이스 접근에 비용이 발생할 수 있어 장고는 이것을 방지). 대신 정보를 문자열로 받기 위해 `display_genre` 함수를 정의(이는 위해서 호출한 함수, 아래에서 정의)

```python
## filename: models.py

def display_genre(self):
    """Create a string for the Genre. This is required to display genre in Admin."""
    return ', '.join(genre.name for genre in self.genre.all()[:3])

display_genre.short_description = 'Genre'
```

genre 필드의 세 가지 값들의 문자열을 생성한다. 그리고 이 메소드를 위해 관리자사이트에서 사용할 수 있는 `short_description`을 만든다.

## 목록 필터 추가하기

목록 안에 항목들이 많다면 필터를 추가하는 것이 유용하다. `list_filter` 속성 안에서 필드들을 목록짓는다.

```python
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')
```
## 세뷰 보기 레이아웃(detail view layout) 조직하기

### 어떤 필드들이 보이고 배치될 지 제어하기

```python
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
    fields = ['first_name', 'last_name', ('date_of_birth', 'date_of_death')]
```

필드들은 기본적으로 수직적으로 표시되지만 튜플 안에 추가적으로 그룹짓는다면 수평적으로 표시된다.
(양식에서 제외할 속성을 선언하려면 exclue를 사용)

### 세부 뷰 구역 나누기(Sectioning the detail view)

[fieldsets](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets) 속성을 사용해 모델 정보를 그룹화하기 위한 "sections"를 추가할 수 있다.

```python
@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')
    
    fieldsets = (
        (None, {
            'fields': ('book', 'imprint', 'id')
        }),
        ('Availability', {
            'fields': ('status', 'due_back')
        }),
    )
```

이제 웹사이트 안의 book instance view를 보자.

## 연관 레코드들의 인라인 편집(Inline editing of associated records)

```python
class BooksInstanceInline(admin.TabularInline):
    model = BookInstance

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')
    inlines = [BooksInstanceInline]
```

연관된 레코드들은 동시에 추가할 수도 있다. [inlines](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.inlines), [TabularInline](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.TabularInline) - 수평적 레이아웃, [StackedInline](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.StackedInline) - 기본모델 레이아웃이 선택 가능하다.

기본값으로 예비 책 인스턴스를 위한 플레이스를 홀더를 가지지 않고 새로운 북 인스턴스마다 새로운 북 인스턴스 링크 하나씩 추가하는 것이 좋다. 또는 BookInstance를 여기서는 읽기 불가(non-readable) 링크로 목록화하는 것도 좋고요. 전자는 BookInstanceInline 모델 안의 extra 속성을 0으로 설정하여 완료할 수 있다. 직접 해보세요.

## 도전 과제

이 섹션에서 많은 것을 배웠기 때문에, 이젠 직접 몇 가지를 도전해 볼 차례입니다.

BookInstance 목록 뷰에 책, 상태, 만기 날짜, 그리고 id(book, status, due back date, id)를 표시하기 위한 코드를 추가해 보세요(기본 `__str__()` 텍스트 가 아닌).
Book/BookInstance에서 했던 것고 같은 접근법을 사용해서 Author 세부 사항 뷰에 Book 항목들의 인라인 목록(Inline listing)을 추가해 보세요.

## Links

[Django Tutorial Part 4: Django admin site](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Admin_site)