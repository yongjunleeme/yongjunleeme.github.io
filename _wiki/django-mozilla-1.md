---
layout  : wiki
title   : django-mozilla-1
summary : 
date    : 2020-02-02 21:48:25 +0900
updated : 2020-02-03 14:46:36 +0900
tags    : django
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

> [Moziila djangdo tutorial](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 지적과 피드백 대환영입니다.   
개발 중에 진행이 어렵다면, 여기 [Github](https://github.com/mdn/django-locallibrary-tutorial)에 완전히 개발된 버전의 웹사이트 소스코드를 참고할 수도 있다.
# Why
    * url, view, model, template이 무엇인지, 어떻게 작동하는지 조금이라도 알자. 

# 설명
![basic-django-1](https://user-images.githubusercontent.com/48748376/73620295-92b97600-4674-11ea-9dd0-b0efee518fab.png)

- **URLs:** URL mapper는 HTTP 요청을 적절한 view로 보내주기 위해 사용된다. 또한 URL에 나타나는 특정한 문자열이나 숫자의 패턴을 일치시켜 데이터로서 뷰 함수에 전달할 수 있다.   
- **View:** view는 HTTP 요청을 수신하고 HTTP 응답을 반환하는 요청 처리 함수다. View는 Model을 통해 요청을 충족시키는 데 필요한 데이터에 접근한다. 그리고 탬플릿에게 응답의 서식 설정을 맡긴다.   
- **Models:** Model은 application의 데이터 구조를 정의하고 데이터베이스의 기록을 관리(추가, 수정, 삭제)하고 query하는 방법을 제공하는 파이썬 객체다.   
- **Templates:** 탬플릿은 파일의 구조나 레이아웃을 정의하고(예: HTML 페이지), 실제 내용을 보여주는 데 사용되는 플레이스홀더를 가진 텍스트 파일이다. 템플릿으로 모든 파일의 구조를 정의할 수 있다.템플릿이 꼭 HTML 타입일 필요는 없다.   
  
> 언뜻 보기에 회사 같다. URL이 대표, View가 팀장, Model이 백엔드, Templates가 프론트엔드..

**Note**: 장고는 이 구조를 모델 뷰 템플릿(Model View Template)(MVT) 아키텍처라고 부른다. 익숙한 Model View Controller 아키텍처와 많은 유사점을 가지고 있다.

## 요청을 알맞은 view에 보내기 (urls.py)

```python
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book/<int:id>/', views.book_detail, name='book_detail'),
        path('catalog/', include('catalog.urls')),
        re_path(r'^([0-9]+)/$', views.best),
    ]
```
- **urlpatterns 객체** - <code>path()</code> 또는 <code>re_path()</code> 함수의 리스트(list)
- **path() 메소드** - 꺾쇠 괄호()를 사용해서 인수를 정의
- **re_path() 함수** - 정규식이라는 유연한 패턴 매칭 접근
- 두 메소드(path, re_path)의 첫 번째 인수는 1 경로, 두 번째 인수는 패턴이 일치될 떄 호출되는 2 다른 함수다. <code>views.book.detail </code>은 이 함수가 <code>book_detail()</code>이며 view 모듈 안(views.py 파일 안)에서 찾을 수 있다는 뜻

## 요청 처리하기 (views.py)

- views
    - HTTP 요청을 수신하고 HTTP 응답을 되돌려준다
    - 데이터베이스에 접근하고 템플릿을 렌더링하기 위해 프레임워크의 다른 자원들을 정리

```python
    ## filename: views.py (Django view functions)
    
    from django.http import HttpResponse
    
    def index(request):
        # Get an HttpRequest - the request parameter
        # perform operations using information from the request.
        # Return HttpResponse
        return HttpResponse('Hello from Django!')
```

- HttpRequest 객체를 (request)의 인자로 받고 HttpResponse 객체를 반환

## 데이터 모델 정의하기 (models.py)

- models라는 파이썬 객체를 통해 데이터를 관리하고 쿼리
- 모델의 정의는 기본 데이터베이스와는 별개. 그저 모델 구조와 코드를 작성하면 나머지는 장고가 다 알아서 처리..

```python
    ## filename: models.py
    
    from django.db import models 
    
    class Team(models.Model): 
        team_name = models.CharField(max_length=40) 
    
        TEAM_LEVELS = (
            ('U09', 'Under 09s'),
            ('U10', 'Under 10s'),
            ('U11', 'Under 11s'),
            ...  #list other team levels
        )
        team_level = models.CharField(max_length=3,choices=TEAM_LEVELS,default='U11')
```

- Team 객체는 장고 클래스 <code>models.Model</code>에서 파생
- 이 객체는 팀 이름과 팀 레벨을 캐릭터 필드로 정의하고 각각의 기록에 저장될 최대 캐릭터 숫자를 정함
- team_level 은 랜덤으로 값이 선정되기 때문에,이를 choice 필드로 정의

## 데이터 쿼리하기 (views.py)

- 장고 모델은 데이터베이스를 간단히 탐색하기 위한 쿼리 API를 제공
- 예 : 정확하게 일치(exact), 대소문자 구분없이(case-insensitive), 해당 숫자보다 큰(greater than) 등

```python
    ## filename: views.py
    
    from django.shortcuts import render
    from .models import Team 
    
    def index(request):
        list_teams = Team.objects.filter(team_level__exact="U09") # 이 줄이 모델 쿼리 사용하는 예제
        context = {'youngest_teams': list_teams}
        return render(request, '/best/index.html', context)
```

- 위 함수는 render() 함수를 사용해서 브라우저로 보내는 HttpResponse를 생성
- 특정 HTML 템플릿을 결합하거나 템플릿 안에 일부 데이터를 삽입하는 HTML 파일 생성 ("context")

> [context](https://stackoverflow.com/questions/20957388/what-is-a-context-in-django)는 딕셔너리.<br> 장고 템플릿을 사용할 때 변수를 <code> myvar1 </code>를 컬리브레이스 2개로 감싼 형태로 나타낸다. 예를 들어 <code> myvar1: 101, myvar2: 102 </code>(이것도 컬리브레이스 2개로 감싼 것임) 템플릿을 render 메소드에게 전달한다. 그러면 <code>myvar1</code>는 101로 바뀌고 <code>myvar2</code>는 102로 바뀌는 것이다. 그러나 이는 비단 단순한 예이고 ContextProcessor처럼 진보된 콘셉트도 있다.    
settings.py 파일 내에 Context Processor가 HttpRequest 오브젝트를 가지고 있고 이는 딕셔너리를 리턴한다.(위의 context와 비슷함) Context Processor에 의해 리턴된 딕셔너리는 장고에 의해 context로 전달돼 통합되는 것이다. Context Processor를 쓰면 직접 코딩하지 않고 각 뷰를 삽입할 수 있다. 이는 settings.py 내 <code>TEMPLATE_CONTEXT_PROCESSORS</code>에 추가한다. 

## 데이터 렌더링하기 (HTML 템플릿들)

- 템플릿 시스템은 페이지 생성 시 데이터 플레이스홀더들을 통해 산출물 문서의 구조를 구체화하도록 돕는다
- 장고는 네이티브 템플레이팅 시스템과 Jinja2 out of the box 파이썬 라이브러리 모두 지원

```html
    ## filename: best/templates/best/index.html
    <!DOCTYPE html>
    <html lang="en">
    <body> 
     {% if youngest_teams %}
        <ul>
        {% for team in youngest_teams %}
            <li>{{ team.team_name }}</li>
        {% endfor %}
        </ul>
    {% else %}
        <p>No teams are available.</p>
    {% endif %}
    
    </body>
    </html>
```

- 이 템플릿은 데이터 쿼리 예제에서 살펴본 youngest_team 변수(render () 함수 내 컨텍스트 변수에 포함된)를 사용해서 반복문을 사용하고 반복을 돌면서 팀의 이름을 하나씩 반환한다.


# Links
[Mozllia django](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction)    
[Stackoverflow](https://stackoverflow.com/questions/20957388/what-is-a-context-in-django)
