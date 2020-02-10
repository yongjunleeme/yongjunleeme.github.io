---
layout  : wiki
title   : django-basic-url 
summary : 
date    : 2020-02-10 15:38:38 +0900
updated : 2020-02-10 18:49:01 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

## Example

**sample URLconf**
```
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```

* Note:
    * URL 값을 캡처하기 위해 ()를 쓴다.
    * 캡처된 값은 converter type을 선택적으로 include할 수 있다. 이를테면 정수 파라미터를 캡처하기 위해 `<int:name>`를 쓴다. 만약 converter가 포함되지 않는다면 어떤 스트링(슬래시를 제외한)도 사용 가능하다.
    * 맨 앞에 슬래시를 붙일 필요는 없다. 예를 들어 `articles`로 쓰지 `/articles`로 쓰지 않는다.

* Example requests:
    * `/articles/2005/03/`은 위 리스트이 3번째줄에 위치한 코드에 매치된다. 장고는 `views.month_archive(request, year=2005, month=3)` 함수를 호출한다.
    * `/articles/2003/`은 위 리스트 2번쨰줄이 아닌 1번째줄에 위치한 코드와 매치된다. 순서대로 테스트되는 패턴이기 때문이다.
    * `/articles/2003`은 이 패턴들 어떤 것과도 매치되지 않는다. 각 패턴들은 모두 URL 끝에 슬래시를 요청하는 이유다.
    * `/articles/2003/03/building-a-django-site/`는 마지막 패턴과 매치된다.장고는 `views.article_detail(request, year=2003, month=3, slug="building-a-django-site")`를 호출할 것이다.
 
## Path converters
    다음의 path conveter들은 디폴트로 쓸 수 있다.
    * **str** - 경로를 나누는 슬래시를 제외하고 어떤 문자열도 매치된다. 표현식 converter가 없으면 strtind이 디폴트로 설정된다.
    * **int** - 0 내지 양의 정수가 매치된다. int를 리턴.
    * **slug** - 아스키 문자의 slug 문자나 숫자, 하이픈, 언더스코어가 매치된다. 예를 들어  building-your-1st-django-site.
    * **uuid** - 같은 페이지 내 매핑된 다중 URL들을 막기 위해 대시와 소문자로 포함된다. 예를 들어 075194d3-6885-417e-a8a8-6c931e272f00.

# Links

* [장고 공식문서](https://docs.djangoproject.com/en/3.0/topics/http/urls/)
