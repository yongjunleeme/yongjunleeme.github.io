---
layout  : wiki
title   : django-ORM
summary : 
date    : 2020-02-19 15:43:46 +0900
updated : 2020-02-23 23:32:46 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

### `disply()`

```python
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

```shell
>>> p = Person(name="Fred Flintstone", shirt_size="L")
>>> p.save()
>>> p.shirt_size
'L'
>>> p.get_shirt_size_display()
'Large'
```

### `values_list`

```shell
>>> fruit = Fruit.objects.create(name='Apple')
>>> fruit.name = 'Pear'
>>> fruit.save()
>>> Fruit.objects.values_list('name', flat=True)
<QuerySet ['Apple', 'Pear']>
```


### `filter(**kwargs)`

* 주어진 검색어와 매치되는 오브젝트를 포함해 새로운 쿼리셋을 리턴
* 다수 파라미터가 주어지면 AND 조건
* OR 조건을 구현하려면 [Q objects](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#django.db.models.Q) 를 참고

### `exclude(**kwargs)`

* 주어진 검색어와 매치되지 않는 오브젝트를 포함해 새로운 쿼리셋을 리턴 
* 마찬가지로 다수 파라미터는 AND 조건

* 아래 예는 2015-1-3보다 늦으면서 `headline`이 Hello인 `pub_date`

```shell
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3), headline='Hello')
```

### `annotate()`

여기부터 다시 해석해서 옮겨 놓아야


### get_or_create()

```python
Patient.objects.get_or_create(uid=10112)
```

- 있으면 가져오고 없으면 만들어서 가져온다.

### bulk_create()

```pathon
Patient.objects.bulk_create([Patient(name="soyoung'), Patiendt(name='person1')...])
```

- 한 번의 쿼리로 여러 objects를 db에 넣는다.
- 주의: `saver()`가 불리지 않음, `ManyToMany` 관계에서는 되지 않음

### ORM 특징 `Lazy_loading`

1. Lazy querySet - 쿼리를 쓴다고 해서 바로 실행이 되는 게 아니라 특정부분에서만 Evaluate

```python
patients = Patient.objects.filter(gender='F)
patents = patients.filter(age__gte=23)
print(patients) " <-여기서만 Evluation 수행, 쿼리셋을 쓴다고 바로 evaluated되는 것이 아님"
```

2. Lazy Fetching - related object에 실제로 접근할 떄 그제서야 related object를 가져옴

```shell
>>> book.get(title="book A").author.name
```

→ N+1 Problem - 쿼리 1번으로 N건을 가져왔는데 관련 컬럼을 얻기 위해 N번의 쿼리를 더 수행하는 문제  >> prefetch_related, select_related로 해결

## 프로파일링 툴

### `str(<MY_QUERYSET>.query)` : 리턴값이 쿼리셋인 경우

```shell
>>> my_query = Patient.objects.filter(id=1)
>>> str(my_query.query)
SELECT 'emr_patient'.'uid', 'emr_patient','name' ..
FROM 'emr_paatient' WHERE 'emr_patient'.'ie'=1
```

- ORM에 내장된 `.query`라는 애트리뷰트를 스트링으로 전환하면 ORM으로 실행한 실제 SQL문이 나옴

### `connection.queries[-1]

```shell
>>> from django.db dimport connection
>>> Patient.objects.count()
>>> connection.queries[-1]
{'sql': 'SELECT COUNT(*) AS '__count' FROM 'emr_patient'', 'tiem': '0.001'}
```

- `connection` 모듈을 통해 쿼리셋으로 만들어진 실제 sql문을 shell에서 확인할 수 있다.
- 가장 마지막을 실행된 쿼리스트링, 걸린 시간
- 실제 실행되는 valid sql은 아니고 ORM이 데이터베이스 어댑터에게 쿼리와 파라미터를 각각 보내는 것일 뿐 실제 작업은 어댑터가 한다.

### `Django-debug-toolbar` 

#### 설치

```python
# filename: settings.py

INSTALLED_APPS = [
'debug_toolbar',
]

MEDDLEWARE = [
'debug_toolbar.middleware.DebugToolbarMiddleware',
]

INTERNAL_IPS = ('127.0.0.1',)
```

```python
# filename: urls.py
import debug_toolbar

urlpatterns += [
path('__debug__/', include('debug_toolbar.urls')),
]
```

#### 실행

- localhost 실행 시 DJDT 메뉴가 생긴다. (웹페이지 오른쪽)
- 하지만 로컬에서만 돌아가는 한계. 라이브 데이터 측정 불가.

### [Silk](https://github.com/jazzband/django-silk)

- 라이브 데이터뿐만 아니라 과거내역까지 한 번에 보여줌

#### 설치

```python
# filename: settings.py

INSTALLED_APPS = [
'silk',
]

MIDDLEWARE =[
'silk.middleware.SilkyMiddleware',
]
```

```python
# filename: urls.py
urlpatterns += [
path('v1/silk/', include('silk.urls', namespace='silk')],
]
```

```
+ migration
```

- 프로파일링 원하는 곳에 데코레이터를 붙여줌

```python
from silk.profiling.profiler import silk_profile

class PatientHandlerView(LoggingMixin, APIView):
	serializer_class = PatientHandlerSerializer
	
	@silk_profile()
	def post(self, request, *args, **kwargs):
		serializer = PaatientHandlerSerializer(data=request.data)
		
		patients =  serializer.validated_data.get('data')
		parse(patients, 'patient')
		return Responce()
```

- silk페이지에 가면 데코레이터를 붙인 url에 대한 정보 나옴
- 응답시간, 쿼리 총 갯수, 쿼리에 걸린 시
- 이벤트 상세페이지에 들어가면 실제 쿼리문도 보여
- 각 쿼리 Traceback과 Profile graph도 보여

- 장점
	- Live 환경에서 사용 가능
	- 여러 개를 비교하면 서 한번에 볼 수 있음
	- Profile Grapsh, Traceback 지원
- 단점
	- 느리다(쿼리 많아질수록 느림)
	- 실제 실행 시간도 같이 느려진다
	- ORM을 사용하지 않으면 SQL 쿼리를 보여주지 않는다.






























[박준규님 블로그](https://velog.io/@devzunky/TIL-no.66-Django-Basic-19-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C%ED%95%98%EA%B8%B0)
[불곰님 블로그](https://brownbears.tistory.com/63)
[queryset](https://docs.djangoproject.com/en/3.0/ref/models/querysets/) 
