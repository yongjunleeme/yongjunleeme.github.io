---
layout  : wiki
title   : django-mozilla-1
summary : 
date    : 2020-02-02 21:48:25 +0900
updated : 2020-02-02 22:02:37 +0900
tags    : django
toc     : true
public  : true
parent  : django
latex   : false
---
* TOC
{:toc}

> [Moziila djangdo tutorial](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 지적과 피드백 대환영입니다.

## Why
    * url, view, model, template이 무엇인지, 어떻게 작동하는지 조금이라도 알자. 

## 설명
- **URLs:** 단일 함수를 통해 모든 URL 요청을 처리하는 것이 가능하지만, 분리된 뷰 함수를 작성하는 것이 각각의 리소스를 유지보수하기 훨씬 쉽습니다. URL mapper는 요청 URL을 기준으로 HTTP 요청을 적절한 view로 보내주기 위해 사용됩니다. 또한 URL mapper는 URL에 나타나는 특정한 문자열이나 숫자의 패턴을 일치시켜 데이터로서 뷰 함수에 전달할 수 있습니다.
- **View:** view는 HTTP 요청을 수신하고 HTTP 응답을 반환하는 요청 처리 함수입니다. View는 Model을 통해 요청을 충족시키는 데 필요한 데이터에 접근합니다. 그리고 탬플릿에게 응답의 서식 설정을 맡깁니다.
- **Models:** Model은 application의 데이터 구조를 정의하고 데이터베이스의 기록을 관리(추가, 수정, 삭제)하고 query하는 방법을 제공하는 파이썬 객체입니다..
- **Templates:** 탬플릿은 파일의 구조나 레이아웃을 정의하고(예: HTML 페이지), 실제 내용을 보여주는 데 사용되는 플레이스홀더를 가진 텍스트 파일입니다. view는 HTML 탬플릿을 이용하여 동적으로 HTML 페이지를 만들고 model에서 가져온 데이터로 채웁니다. 탬플릿으로 모든 파일의 구조를 정의할 수 있습니다.탬플릿이 꼭 HTML 타입일 필요는 없습니다!

**Note**: 장고는 이 구조를 "모델 뷰 템플릿(Model View Template)(MVT)" 아키텍처라고 부릅니다. 이것은 더 익숙한 Model View Controller 아키텍처와 많은 유사점을 가지고 있습니다.

## 요청을 알맞은 view에 보내기 (urls.py)
```
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book/<int:id>/', views.book_detail, name='book_detail'),
        path('catalog/', include('catalog.urls')),
        re_path(r'^([0-9]+)/$', views.best),
    ]
```
- urlpatterns 객체  - path() 및/또는 re_path() 함수의 리스트(list)
- path() 메소드 - 꺾쇠 괄호()를 사용해서 인수를 정의
- re_path() 함수 - 정규식이라는 유연한 패턴 매칭 접근

## 요청 처리하기 (views.py/)

- views
    - HTTP 요청을 수신하고 HTTP 응답을 되돌려준다
    - 데이터베이스에 접근하고 템플릿을 렌더링하기 위해 프레임워크읟 다른 자원들을 정리

```
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

```
    # filename: models.py
    
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

- Team 객체는 장고 클래스models.Model에서 파생
- 이 객체는 팀 이름과 팀 레벨을 캐릭터 필드로 정의하고 각각의 기록에 저장될 최대 캐릭터 숫자를 정함
- team_level 은 랜덤으로 값이 선정되기 때문에, 우리는 이를 choice 필드로 정의하며, choices들 간에 선택된 값이 보여지고 디폴트 값에 따른 데이터가 저장

## 데이터 쿼리하기 (views.py)

- 장고 모델은 데이터베이스를 간단히 탐색하기 위한 쿼리 API를 제공
- 예 : 정확하게 일치(exact), 대소문자 구분없이(case-insensitive), 해당 숫자보다 큰(greater than) 등

```
    ## filename: views.py
    
    from django.shortcuts import render
    from .models import Team 
    
    def index(request):
        list_teams = Team.objects.filter(team_level__exact="U09")
        context = {'youngest_teams': list_teams}
        return render(request, '/best/index.html', context)
```

- 위 함수는 render() 함수를 사용해서 브라우저로 HttpResponse를 생성
- 특정 HTML 템플릿을 결합하거나 템플릿 안에 일부 데이터를 삽입하는 HTML 파일 생성 (변수 내에서 제공하는 이름은 "context")

## 데이터 렌더링하기 (HTML 템플릿들)

- 템플릿 시스템은 페이지 생성 시 데이터 플레이스홀더들을 통해 산출물 문서의 구조를 구체화하도록 돕는다.
- 템플릿은 HTML뿐만 아니라 다른 타입의 문서도 생성 가능
- 장고는 네이티브 템플레이팅 시스템과 Jinja2 out of the box로 불리는 파이썬 라이브러리 모두 지원

```
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


# Link
[Mozllia django](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction) 
