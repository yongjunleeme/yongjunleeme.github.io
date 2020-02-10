---
layout  : wiki
title   : django-mozilla-5
summary : 
date    : 2020-02-05 13:07:21 +0900
updated : 2020-02-05 15:40:26 +0900
tags    : django
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

> [Django Tutorial Part 5: Creating our home page](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Home_page) 를 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 지적과 피드백 대환영입니다.
 

## Overview

이제 사용자에게 정보를 제공하기 위한 코드를 작성할 때다. URL들을 정의하는 것이다. 다음으로 페이지를 나타내기 위한 URL 매퍼, views, 그리고 템플릿을 생성할 것이다.
- URL 매퍼들 : 적절한 view 함수들을 위해 지원되는 URL들(그리고 URL들 안에 인코딩된 어떤 정보라도)을 포워딩하기 위해.
- View 함수들: 요청된 데이터를 모델들에게서 가져오고, 데이터를 표시하는 HTML 페이지를 생성하고 그리고 브라우저 안의 view로 페이지들을 사용자에게 반환하기 위해.
- 탬플릿들: 데이터를 뷰들 안에 렌더링할 때 사용하기 위해.

## resource URLs 정의하기

- `catalog/` — 홈/색인(index) 페이지
- `catalog/books/` — 모든 책들의 목록
- `catalog/authors/` — 모든 저자들의 목록
- `catalog/book/<id>` — <id> 라는 이름의(기본값) 프라이머리 키(primary key) 필드를 가지는 특정한 책을 위한 세부 사항 뷰(detail view). 예를 들어, 목록에 추가된 세 번째 책은 `/catalog/book/3`이 될 것
- `catalog/author/<id>` —  <id>라는 이름의 프라이머리 키(primary key) 필드를 가지는 특정한 저자를 위한 세부 사항 뷰(detail view). 예를 들어, 목록에 추가된 11번째 저자는 `/catalog/author/11`이 될 것
URL 매퍼는 인코딩된 정보를 추출해 view로 전달한다. 그리고 view는 데이터베이스에서 무슨 정보를 가져올지 동적으로 결정한다. 장고 문서는 더 나은 URL설계를 위해 URL 본문(body)에 정보를 인코딩하는 것을 권장한다.


## index page 만들기

### URL 매핑

```python
urlpatterns = [
    path('', views.index, name='index'),
]
```

이름을 반전시킬 수도 있다. 즉, 매퍼가 처리하도록 설계된 리소스를 향하는 URL을 동적으로 생성하기 위해 예를 들어 아래 템플릿을 추가해서 이름 매개변수로 사용하는 것이다. 그러면 다른 모든 페이지에서 홈페이지로 링크를 걸 수 있다.

[예제를 넣으면 liqid error가 나온다](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Home_page)

### View (함수-기반의)

```python
## filename: catalog/views.py 

from django.shortcuts import render

# Create your views here.

from catalog.models import Book, Author, BookInstance, Genre

def index(request):
    """View function for home page of site."""

    # Generate counts of some of the main objects
    num_books = Book.objects.all().count()
    num_instances = BookInstance.objects.all().count()
    
    # Available books (status = 'a')
    num_instances_available = BookInstance.objects.filter(status__exact='a').count()
    
    # The 'all()' is implied by default.    
    num_authors = Author.objects.count()
    
    context = {
        'num_books': num_books,
        'num_instances': num_instances,
        'num_instances_available': num_instances_available,
        'num_authors': num_authors,
    }

    # Render the HTML template index.html with the data in the context variable
    return render(request, 'index.html', context=context)
```

뷰는 HTTP 요청을 처리하고 데이터베이스에서 요청된 데이터를 가져오고 HTML 템플릿을 이용해 테이터를 HTML 페이지에 렌떠링하고 그러고 나서 생성된 HTML을 HTML 응답으로 반환하여 사용자들에게 페이지를 보여주는 함수다.

`object.all()` 속성을 사용하는 레코드들의 개수를 가져온다. view함수의 마지막 부분에서는 HTML 페이지를 생성하고 이를 응답으로 반환하기 위해 `render()`함수를 호출한다. 이 바로가기(shorcut) 함수는 일반적으로 사용되는 경우들을 간단히 하기 위해 여러 다른 함수들ㅇ르 포함한다. `render()` 함수는 아래 매개변수들을 허용한다.

- HttpRequest인 원본 request 객체
- 데이터에 대한 플레이스홀더(placeholder)들을 갖고 있는 HTML 템플릿
- 파이썬 딕셔너리(dictionary)인 플레이스홀더에 삽입할 데이터를 갖고 있는 context 변수

### 탬플릿(Template)

장고는 settings 파일에 기초해 여러 장소에서 템플릿을 찾는다. 기본으로 설치된 애플리케이션 'template' 경로에서 자동으로 찾는다. 장고가 어떤 템플릿을 찾는지 어떤 양식을 지원하는기 [여기](https://docs.djangoproject.com/en/2.0/topics/templates/) 에서 볼 수 있다.

[예제를 넣으면 liqid error가 나온다](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Home_page)

## LocalLibrary 기본 탬플릿



위 코드를 locallibray 베이스 템플릿으로 사용할 것이다. title, sidebar 그리고 content 블럭을 정의한다. 미래에 쉽게 변경하기 위해 블럭들 안에 묶여 있다.



## 색인(index) 탬플릿


동적 콘텐츠 섹션에서 포함하고 싶은 view 정보를 플레이스홀더(템플릿 변수)로 선언한다. 이 변수들은 이중 중괄호로 묶인다(그러나 이중 중괄호를 넣는 기능을 몰라서 못 쓰고 있음). 태그들은 퍼센트와 단일 중괄호로 감싸여 있다.
주의해야 할 점은 변수들의 이름은 키로 정해진다는 사실이다. 이 키들은 viewdml `render()` 함수 안의 context 딕셔너리로 전달하는 키다. 템플릿이 렌더링될 떄 그것들과 연관된 값들로 대체될 것이다.


```python
context = {
    'num_books': num_books,
    'num_instances': num_instances,
    'num_instances_available': num_instances_available,
    'num_authors': num_authors,
}

return render(request, 'index.html', context=context)
```

## Templates 에 정적 파일 참조하기(referencing)

자바스크립트, CSS 그리고 이미지를 포함하는 정적 리소스를 사용할 때 위치를 알 수 없거나 바뀌기 때문에 장고는 `STATIC_URL` 전역 설정을 기준으로 템플릿에서 위치를 특정한다.



이와 같이 'static'을 지정하는 load 템플릿 태그를 호출한다. 그러고 나서 static 템플릿 태그를 사용할 수 있고 관련 UR을 ㅅ요구되는 파일에 지정할 수 있다.



정적 파일들로 작업하는 정보는 [여기](https://docs.djangoproject.com/en/2.0/howto/static-files/) 를 참고하면 된다.

## URL 링크하기(Linking to URLs)



이 태그는 `urls.py`에서 호출된 `path()` 함수의 이름과 연관된 view가 그 함수에서 수신받을 모든 인자들을 위한 값을 허용하고 리소스를 링크하는 데 사용할 URL을 반환한다.

## 탬플릿을 찾을 수 있는 곳 설정하기

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, 'templates'),
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

폴더 안에서 템플릿을 찾아볼 수 있도록 장고에게 위치를 알려줘야 한다. 이를 위해 `settings.py` 파일을 수정해 TEMPLATES 긱체에 templates 경로(dir)를 추가한다.

## 어떻게 보일까요?

All books와 All authors 링크들에 대한 경로, 뷰 그리고 탬플릿들이 정의되지 않았기 때문에 그 링크들은 작동하지 않을 것이다. 아직은 단지 `base_generic.html` 템플릿 안에 그 링크들을 위한 플레이스홀더(placeholder)들을 삽입했을 뿐이다.

