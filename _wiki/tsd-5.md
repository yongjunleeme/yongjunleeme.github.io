---
layout  : wiki
title   : tsd-5 
summary : 
date    : 2020-03-27 09:15:02 +0900
updated : 2020-04-07 23:36:21 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


모델은 크게(Fat Models), 유틸리티는 모듈로(Utility Modules), 뷰는 가볍게(Thin Views), 템플릿은 단순하게(Stupid Template)

## 1장 코딩 스타일

### 1.1 읽기 쉬운 코드를 만드는 것이 왜 중요한가

- 축약적이거나 함축적인 변수명은 피한다.
- 함수 인자의 이름들은 꼭 써 준다.
- 클래스와 메서드를 문서화한다.
- 코드에 주석은 꼭 달도록 한다.
- 재사용 가능한 함수 또는 메서드 안에서 반복되는 코드들은 리팩터링을 해둔다.
- 함수와 메서드는 가능한 한 작은 크기를 유지한다. 어림잡아 스크롤 없이 읽을 수 있는 길이가 적합하다.

### 1.2 PEP 8

- [PEP 8](http://www.python.org/dev/peps/pep-0008)
- 코드품질을 위한 flask8

#### 1.2.1 79칼럼의 제약

- 오픈 소스 프로젝트에서는 79칼럼 제약을 반드시 지킨다. 경험상 프로젝트의 기여자나 방문자들은 이 줄 길이 제약에 대해 끊임없이 불평할 것이다.
- 프라이빗 프로젝트에 한해서는 99칼럼까지 제약을 확장함으로써 요즘 나오는 모니터들의 장점을 좀 더 누릴 수 있다.

### 1.3 임포트에 대해

장고 프로젝트 임포트 순서

1. 표준 라이브러리
2. 코어 장고 
3. 장고와 무관한 앱
4. 프로젝트 앱

```python
# 표준 라이브러리
from __future__ import absolute_import
from math import sqrt
from os.path import abspath

# 코어 장고
from django.db import models
from django.utils.translation import ugettext_lazy as _

# 서드 파티 앱
from django_extensions.db.models improt TimeStampeModel

# 프로젝트 앱
from splits.models import BananaSplit

```

#### 1.6.1 장고 코딩 스타일

- [스타일 가이드 라인](https://docs.djangoproject.com/en/1.8/internals/contributing/writing-code/coding-style/)

## 2장. 최적화된 장고 환경 꾸미기

### 2.1 다른 종류의 데이터베이스 사이에는 다른 성격의 필드 타입과 제약 조건이 존재한다.

장고 ORM이 여러분의 코드가 SQLite3와 좀 더 엄격한 타입으로 서로 작용하게 해준다. 하지만 개발 환경에서 발생한 폼과 모델 사이의 문제는 운영 환경으로 가기 전까지는 발견되지 않으며(테스트를 통해서도 발견되지 않는다) 아마 복잡한 쿼리라도 로컬 환경에서 문제 없이 처리될 것이다. 하지만 운영 환경으로 가면 PostgreSQL이나 MySQL 데이터베이스가 로컬 환경에서는 전혀 본 적이 없는 제약 조건 에러(constraint error)를 뱉어 낼 것이다. 로컬 환경에 ㅎ운영 환경과 같은 데이터베이스 엔진을 구축하기 전까지는 이 문제를 재현하는 데 매우 큰 어려움이 따를 것이다.

### 2.3 

- PYTHONPATH 설정
명령행과 환경 변수에 이미 익숙하다면 virtualenv의 PYTHONPATH를 설정함으로써 `django-admin.py``를 여러 작업에 사용할 수도 있다.
또한 virtualenv의 PYTHONPATH를 현재 디렉터리와 최신 버전의 pip를 포함하도록 설정하고, 현재 프로젝트의 최상위 디렉터리에서 `pip install -e .`를 실행하면, 현재 디렉터리에서 패키지를 수정 가능한 상태로 설치할 수 있다.
설정 방법이 익숙하지 않거나 절차가 복잡해 보인다면 걱정하지 말고 manage.py를 이용하면 된다.

- [참고1](http://hope.simons-rock.edu/~pshields/cs/python/pythonpath.html)
- [참고2](http://docs.djangoproject.com/en/1.8/ref/django-admin/)

#### 2.5.1 베이그런트와 버추얼박스

베이그런트는 재생산이 간으한 개발 환경 생성, 설정, 관리하는 데 쓰는 대중적인 도구다. 베이그런트의 큰 장점은 버추얼박스(또는 다른 VM 도구들)와 쉽게 연동된다는 점이다.

## 3장. 어떻게 장고 프로젝트를 구성할 것인가

장고 프로젝트 템플릿
- [Cookiecutter](https://github.com/pydanny/cookiecutter-django)
- [다른 Cookiecutter](https://www.djangopackages.com/grids/g/cookecutters)

### 3.2 우리가 선호하는 프로젝트 구성

```
<repository_root>
    <django_project.root>/
        <configuration_root>
```

#### 3.2.1 최상위 레벨: 저장소 루트

최상위 <repository-root>/ 디렉터리는 프로젝트의 최상위 절대 루트다. <django_project_root> 이외에 README.rst, docs/ 디렉터리, .gitignore, requirements.txt, 그리고 배포에 필요한 다른 파일 등 중요한 내용이 위치한다.

#### 3.2.2 두 번째 레벨: 프로젝트 루트
두 번째 레벨은 장고 프로젝트 소스들이 위치하는 디렉터리다. 모든 파이썬 코드는 <django_project_root>/ 디렉터리 아래와 그 하부 디렉터리들에 위치한다.

#### 3.2.3 세 번째 레벨: 설정 루트

<configuration_root> 디렉터리는 settings 모듈과 기본 URLConf(urls.py)이 위치

### 3.3 예제 프로젝트 구성

```
icecreamratings_project/
    .gitignore
    Makerfile
    docs/
    README.rst
    requirements.txt
    icecreamratings/
        manage.py
        meida/ # 개발 전용
        products/ # 앱
        prifiles/ # 앱
        ratings/ # 앱
        static/ 
        templates/
        config/
            __init__.py
            settings/
            urls.py
            wsgi.py
```
- Makerfile - 간단한 배포 작업 내용과 매크로들을 포함한 파일이다. 복잡한 구성의 경우에는 인보크(Invoke), 페이버(Paver) 또는 페브릭(Fabric) 등의 도구를 이용한다.
- media/ - 개발 용도로 이용되는 디렉터리다. 사용자가 올리는 사진 등의 미디어 파일이 올라가는 정소다. 큰 프로젝트의 경우 사용자들이 올리는 미디어 파일들은 독립된 서버에서 호스팅한다.
- static/ - CSS, 자바스크립트, 이미지 등 사용자가 올리는 것 이외의 정적 파일들을 위치시키는 곳이다. 큰 프로젝트의 경우 독립된 서버에서 호스팅한다.
- templates/ -  시스템 통합 템플릿 저장 장소

### 3.5 startproject

#### 3.5.2 우리가 선호하는 프로젝트 템플릿

```shell
$ cookiecutter https://github.com/pydanny/cookiecutter-django
```

cookiecutter-django를 포크하여 해당 장고 프로젝트 요구에 맞게 수정해서 마음대로 변형할 수 있다.

#### 3.5.3 대안 템플릿: django-kevin

https://github.com/imkevinxu/django-kevin


### 4장. 장고 앱 디자인의 기본




## Link

- [Two scoops of Django](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=88857020)
