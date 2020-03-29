---
layout  : wiki
title   : tsd-5 
summary : 
date    : 2020-03-27 09:15:02 +0900
updated : 2020-03-29 19:45:08 +0900
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

베이그런트는 재생산이 간으한 개발 환경 생성, 설정, 관리하는 데 쓰는 대중적인 도구다. 베이그런트의 큰 장점은 버추얼박스(또는 다른 VM 도구들)와 쉽게 엳농된다는 점이다.

## Link

- [Two scoops of Django](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=88857020)
