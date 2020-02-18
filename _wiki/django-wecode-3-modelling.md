---
layout  : wiki
title   : django-wecode-3-modelling  
summary : 
date    : 2020-02-18 19:20:05 +0900
updated : 2020-02-18 21:09:31 +0900
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

> 아래 장고에 적용한 모델과는 조금 다르다.

## 배운 내용

- 공식문서
    -[필드](https://docs.djangoproject.com/en/3.0/ref/models/fields/)    
    -[관계](https://docs.djangoproject.com/en/3.0/topics/db/models/#many-to-one-relationships)
    -[쿼리셋](https://docs.djangoproject.com/en/3.0/ref/models/querysets/)    
- 정참조를 당하고 있는 테이블을 먼저 써줘야 나중에 정참조하는 테이블에 데이터가 제대로 돌아간다.
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

class ALlergy(models.Model):

    allergy_name     = models.CharField(max_length = 100)
    
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
## 데이터 다루기 예제

![SQL_JOIN_0](https://user-images.githubusercontent.com/48748376/74728122-91e42f00-5285-11ea-9bfc-7c6de66477e6.png)


