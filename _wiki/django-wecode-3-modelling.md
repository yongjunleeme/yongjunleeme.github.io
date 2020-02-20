---
layout  : wiki
title   : django-wecode-3-modelling  
summary : 
date    : 2020-02-18 19:20:05 +0900
updated : 2020-02-20 20:07:09 +0900
tags    : 
toc     : true
public  : true
parent  : django
latex   : false
---
* TOC
{:toc}

## Why

- 쿼리의 효율성(?)을 높이기 위해 테이블이 합쳐질 모양을 집합으로 그릴 수 있어야 한다.
- SQL 조인도 중요하지만 장고의 `select_related`(정참조), `prefetched_related`(역참조)를 자유자재로 쓸 줄 알아야 쿼리 효율이 올라갈 것.


## ERD

![starbucks_20200218_05_04](https://user-images.githubusercontent.com/48748376/74728817-c4daf280-5286-11ea-9acf-987d6acd349f.png)

> 태어나 처음 그려 봄. 어떻게 그리는지 ForeignKey가 뭔지도 모르고 그림. 아래 장고에 적용한 모델과는 조금 다르다.

## 배운 내용

- 공식문서
    -[필드](https://docs.djangoproject.com/en/3.0/ref/models/fields/), [관계](https://docs.djangoproject.com/en/3.0/topics/db/models/#many-to-one-relationships), [쿼리셋](https://docs.djangoproject.com/en/3.0/ref/models/querysets/)      
- 정참조를 당하는(?)  테이블을 먼저 써줘야 나중에 정참조하는 테이블에 데이터가 제대로 돌아간다.
- `max_length` - 실제 길이 초과해도 데이터 들어간다. 쓰는 이유는 논리적 선언(?) 때문
- 장고는 ForeignKey 선언 시 자동으로 필드 변수에 `_id`를 붙여준다.
- 로직을 십분 활용하려면 **주체가 되는 테이블** 관점에서 생각

## 장고모델 적용

```python
filename: beverage/models.py

from django.db import models

class Category(models.Model):
    
    COLDBREW         = 'CB'
    BREWD            = 'BW'
    ESPRESSO         = 'ES'
    CATEGORY_CHOCES  = [
            (COLDBRES, 'Coldbrew'),
            (BREWD, 'Brewd'),
            (ESPRESSO, 'espresso')
    ]
    
    name             = models.CharField(max_length = 20, choices=CATEGORY_CHOICES)
        db_table     = 'categories'

class Nutrition(models.Model):

    sodium           = models.DecimalField(max_digets = 4, decimal_places = 2)
    sugars           = models.DecimalField(max_digets = 4, decimal_places = 2)
    caffein          = models.DecimalField(max_digets = 4, decimal_places = 2)
    protein          = models.DecimalField(max_digets = 4, decimal_places = 2)

    clss Meta:
        db_table = 'nutrition'

class Allergy(models.Model):

    name     = models.CharField(max_length = 100)
    
    class Meta:
        db_table = 'allergies'

class AllergyBeverage(models.Model):

    beverage         = models.ForeginKey('Beverage', on_delete = models.SET_NULL, null = True)
    allergy          = models.ForeginKey('Allergy', on_delete = models.SET_NULL, null = True)
    
    class Meta:
        db_table = "allergy_beverage"
    
class Beverage(models.Model):

    name            = models.CharField(max_length = 50)
    description     = models.CharField(max_length = 100)
    category        = models.ForeginKey('Category', on_delete = models.SET_NULL, null = True)
    nutrition       = models.ForeginKey('Nutrition', on_delete = models.SET_NULL, null = True)
    allergy_beverages = models.ManytoManyField('Allergy', through = 'AllergyBeverage')
    
    class Meta:
        db_table = "beverages"
```
## 쿼리 효율 높이려면?

### `select_related` 

장고는 Record 내지 Instance 관계 기반 쿼리를 줄이는 ORM 지원

```shell
>>> Beverage.objects.select_related("category").get(id=1)
```
- `ForeeginKey`로 정참조하는 데이터를 연결
- 다시 쿼리 요청할 필요 없이 캐싱된 값을 불러옴
- 여러 필드 한 번에 가져올 수도 있음

### `prefetch_related` 

```shell
>>> category.beverage_set.all()
>>> Category.objects.filter(id=1).prefetch_related("beverage_set")
```

- `ManytoMany`로 정참조하는 모델 연결


```python
class City(models.Model):
     name = models.CharField(max_length=20)

class Language(models.Model):
     name = models.CharField(max_length=100)
 
class Person(models.Model):
     city = models.ForeignKey(City, on_delete=models.CASCADE)
     name = models.CharField(max_length=20)
     language = models.ManyToManyField(Language, through='JoinPersonLanguage')

class JoinPersonLanguage(models.Model):
     person = models.ForeignKey(Person, on_delete=models.CASCADE)
     language = models.ForeignKey(Language, on_delete=models.CASCADE)
```

> 위 예제와 설명은 [박준규님 블로그](https://velog.io/@devzunky/TIL-no.81-Django-To-Reduce-Query) 를 참조했습니다.

- 역참조하는 데이터를 연결
- seoul이라는 row가 있고 seoul을 참조하고 있는 Person의 객체가 있다면
- 아래와 같이 호출 가능

```python
>>> seoul = City.objects.get(id=1)
>>> seoul.person_set.values()
<QuerySet [{'id': 1, 'city_id': 1, 'name': 'zunky'}, {'id': 4, 'city_id': 1, 'name': 'byeong-min'}, {'id': 5, 'city_id': 1, 'name': 'sae-geul'}]>
```

- `prefetch_related`를 사용하면 쿼리 수를 줄일 수 있다.

```python
cities = City.objects.prefetch_related('person_set').all()
for city in cities:
  print(city.person_set.values())
```

* 요약
    * 정방향
        * ForeignKey나 one-to-one같은 Single-valued relationship은 `select_related()`를 활용
        * ManyToMany의 경우 `pregetch_related()`를 활용
    * 역방향
        * 참조당하는 객체를 호출하려면 `prefetch_related()`의 lookup에 `_set`d을 활용:


### `__`

```sehll
>>> a = Store.objects.all().select_related('city', 'gungu')
>>> a = Store.objects.filter(id=1).select_related('city', 'gungu').values('id', 'name', 'city__name')
```
- 1개가 아닌 다수 연결도 가능
- `__` - lookup -> 역, 정참조 모두 가능(포린키로 참조한 객체에 접근할 때)
- select_related로 조인해놓으면 2뎁스 이상도 연결 가능 'gungu__city__name'
- 정참조하면 중간 거치지 않고 뎁스 끝도 연결 가능한 모양

- 겍체 저장사례

```shell
>>> from resume.models import Resume, Career
>>> a = Resume.objects.create(name='abc', education='b', age=13, certi='a') 
낫휘발

>>> Career.objects.create(name='asb', company='12', description='23', resume=a)
"resume=a를 인자로 넣으면 포린키 클래스와 연결되나 봄"

>>> Career.Objects.values('resume__name')
낫휘발

>>> a.save()
낫휘발

>>> Resume.objects.create(name='abc', education='b', age=13, certi='a').save()
휘발
```

- [더 많은 ORM 명령어](https://velog.io/@devzunky/TIL-no.54-Django-Basic-18-ORM)    

### [Realationship](https://velog.io/@devzunky/TIL-no.65-Django-Relationships)



![SQL_JOIN_0](https://user-images.githubusercontent.com/48748376/74728122-91e42f00-5285-11ea-9bfc-7c6de66477e6.png)


