---
layout  : category
title   : 프로그래밍 언어
summary :
date    : 
updated : 
tag     : python django
toc     : true
public  : true
parent  : index
latex   : false
---
* TOC
{:toc}

> [Moziila djangdo tutorial](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 코린이라 틀린 내용이 다수 포함될 가능성이 다분합니다. 넓은 아량을 베풀어 틀린 부분 지적해주시면 큰 도움이 되겠습니다.

[모질라 장고 튜토리얼2](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/skeleton_website)

# 설명

1. 프로젝트 폴더, basic file templates, 그리고 프로젝트 관리 스크립트 (**manage.py**)를 만들기 위해서 `django-admin` 을 사용합니다.
2. 하나 또는 그 이상의 애플리케이션을 만들기 위해서 **manage.py** 를 사용합니다.
3. 프로젝트에 포함시키기 위해 새 어플리케이션들을 등록(register)합니다.
4. 각 어플리케이션에 대해 url/mapper를 연결(hook up)합니다.

# catalog application 만들기
```
    $ pipenv --three
    
    $ pipenv shell
    
    $ pipenv install Django
    
    $ django-admin startproject localllibrary
    
    $ cd locallibrary
    
    $ python3 manage.py startapp catalog
```    

* init.py — 장고/파이썬이 폴더를 Python Package 로 인식하게 할 빈 파일입니다. 또한 프로젝트의 다른 부분에서 객체(object)를 사용할 수 있게 합니다.

# catalog application 등록하기
```
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        **'catalog.apps.CatalogConfig',** 
    ]
```
새로운 행은 어플리케이션 구성 객체(application configuration object) (CatalogConfig)를 지정하게 됩니다. 이것은 어플리케이션을 생성할 때 /locallibrary/catalog/apps.py 안에 생성됩니다.

## Database 설정하기

[공식문서](https://docs.djangoproject.com/en/3.0/ref/databases/)

## 프로젝트의 다른 설정
```
    TIME_ZONE = 'Asia/Seoul'
```
지금은 바꾸지 않지만 알아둬야 할 두 가지 설정이 있습니다:

- `SECRET_KEY`. 이것은 장고의 웹사이트 보안 전략의 일부로 사용되는 비밀 키 입니다. 만약 이 코드를 개발 과정에서 보호하지 않는다면 제품화(production) 과정에서 다른 코드(아마 환경 변수나 파일에서 읽어오는)를 사용해야 할 것입니다.

- `DEBUG`. 이것은 에러가 발생했을 때 HTTP 상태 코드 응답 대신 디버깅 로그가 표시되게 합니다. 디버깅 정보는 공격자에게 유용하기 때문에 제품화된(production) 환경에서는 `False` 로 설정해야 합니다. 하지만, 지금은 `True`로 설정합시다.

## URL 매퍼 연결하기

**locallibrary/urls.py**
```
    from django.contrib import admin
    from django.urls import path
    from django.conf.urls import include
    from django.views.generic import RedirectView
    from django.conf import settings
    from django.conf.urls.static import static
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('catalog/', include('catalog.urls')),
        path('', RedirectView.as_view(url='/catalog/', permanent=True)),
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```
이곳 설명 이해가 잘 안 된다..

Use include() to add paths from the catalog application

Add URL maps to redirect the base URL to our application

Use static() to add url mapping to serve static files during development (only)

catalog 폴더 안에 [urls.py](http://urls.py) 파일 생성
```
    from django.urls import path
    from catalog import views
    
    
    urlpatterns = [
    
    ]
```
## Website framework 테스트 하기
```
    $ pythons manage.py makemigrations
    $ pythons manage.py migrate
    $ pythons manage.py runserver
```
## Link

[장고 튜토리얼 2]([https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/skeleton_website](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/skeleton_website))

## Question

URL 매퍼 연결하기 이해가 안 되므로 이따 다시 읽어보고 이해가 안 되는 부분 적어보자.