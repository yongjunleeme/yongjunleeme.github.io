---
layout  : wiki
title   : django-ORM
summary : 
date    : 2020-02-19 15:43:46 +0900
updated : 2020-03-08 21:13:01 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## ORM 메소드 

### `filter()` 

```python
import datetime

today = datetime.date.today() # 2017-08-26


Post.objects.filtert(created_at__lt=today) # 오늘보다 과거에 작성된 Post
# lt = less than(<) / lte = less than equal(<=)
# gt = greater than(>) / gte = greater than equal(>=)

Comment.objects.filter(created_at__day=today.day) # 연,월에 상관없이 26일에 작성된 댓글
# year, month, day 모두 사용 가능

id_list = [1, 3, 5, 6]
Comment.objects.filter(id__in=id_list) # id값이 id_list에 포함된 경우

Comment.objects.filter(content__isnull=False) # title이 null이 아닌 경우

Post.objects.filter(title__contain='django') # title에 django라는 단어를 포함하는 경우
Post.objects.filter(title__startswith='django') # title이 django로 시작하는 Post
Post.objects.filter(title_endswith='django') # title이 django로 끝나는 Post
# contain, startswith, endswith는 대소문자를 구분하여 검색
# 앞에 i를 붙인 icontain, istartswith, iendswith는 대소문자 구분없이 검색

last_week = today - datetime.timedelta(days=7) # 일주일 전 날짜를 반환

Comment.objects.filter(created_at__range=[last_week, today]) # 지난주부터 오늘 전까지 작성된 Comment

# postgreSQL을 사용한다면 search도 사용 가능
Comment.objects.filter(search='django') # django라는 단어가 포함된 모든 Comment, Full text search라 훨씬 빠름
```

- get은 오브젝트 반환
- filter와 all은 쿼리셋을 반환
- 쿼리셋이 사용될 때 실제 DB 커넥션이 이뤄짐. 그 전까지는 쿼리셋을 반환만
- 출처 - [이설님 블로그](https://lee-seul.github.io/django/2017/08/26/Django-ORM.html)

- [filter()와 filter().valuses() 차이, values와 values_list 차이](https://wangin9.tistory.com/entry/django-query-filter-value-distinct)

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

```python
Post.objects.all().values('title', content) # {'title':  value, 'content': value} 로 이루어진 리스트 반환

Post.objects.all().values_list('title', 'content') # (title, content)와 같이 값으로 이루어진 튜플의 리스트를 반환

Post.objects.all().values_list('title', flat=True) # 해당 필드값으로 이루어진 리스트를 반환
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

```pyhotn
from django.db.models import Count
from django.db.models.functions import Length


Post.objects.filter(comment__isnull=False).annotate(count_comment=Count('comment'))
# 각 Post의 comment 개수를 값으로 가지는 count_comment라는 이름의 필드를 반환

Post.objects.annotate(title_len=Length('title')).filter(title_len__lte=100) 
# title의 길이를 값으로 가지는 title_len이라는 임시 필드를 만들고 그 값을 통해 필터링
```

- `annotation`은 쿼리에 임시 쿼리를 추가하는 메소드

```python
from django.db.models import Sum


Post.objects.filter(comment__isnull=False).aggregate(totle_like=Sum('like'))
# 위의 쿼리는 1개의 행만을 리턴한다. (comment를 가진 Post의 like를 모두 더한 값)
```

- Annotation이 칼럼을 늘린다면, Aggregation은 반대로 Sum등을 활용하여 여러행을 하나로 합쳐서 하나의 행으로 만들어준다.

### get_or_create()

```python
Patient.objects.get_or_create(uid=10112)
```

- 있으면 가져오고 없으면 만들어서 가져온다.

### bulk_create()

```pathon
Patient.objects.bulk_create([Patient(name="soyoung'), Patient(name='person1')...])
```

- 한 번의 쿼리로 여러 objects를 db에 넣는다.
- 주의: `saver()`가 불리지 않음, `ManyToMany` 관계에서는 되지 않음

[예제코드 - kykevin님 블로그](https://velog.io/@kykevin/20191029-TIL-1%EC%B0%A8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-7%EC%9D%BC%EC%B0%A8-bulkcreate-mysql-connector-python-Error)

```python
tierstats_list = []
    for n in range(len(tiers)):
        tierstat_object = Tierstat(
            tier = tier_name[n],
            tier_numbers = tier_roman[n],
            summoner = summoner_count[n],
            summoner_percent = summoner_percentage[n],
            aggregate = aggregate_count[n],
            aggregate_percent = aggregate_percentage[n],
        )
        tierstats_list.append(tierstat_object)                              
Tierstat.objects.bulk_create(tierstats_list)

```

- 위와 같은 방식이 아닌 for문을 돌며 `save()`를 하면 DB 부하 문자를 초래할 수 있으므로 `bulk_create()`를 사용한다고 함

### ORM 효율

#### `all()`이 아닌 `exists()`, `count()`로

```python
posts = Post.objects.all() 

# 이러면 안된다
length = len(posts) # 쿼리셋에 포함된 오브젝트 숫자만을 위해 DB에서 모든 행을 가져와 캐시시킨다. 

# 대신
length = posts.count() # 행의 숫자만을 반환


comments = Comment.objects.filter(created_at__year=2017) # 2017년에 작성된 댓글 

# 안티 패턴
if comments: # Comment를 모두 호출~
    print '이러면...'


if comments.exists():
    print 'Good'

# list()... 
posts = list(posts) # DB에 저장된 데이터가 클 경우 굉장한걸 보게 될 수도.. 

# 순회해야할 경우는 iterator()를 
for post in posts.iterator():
    print post.title

```

#### `select_related`, `prefetch_related`

```python
class Post(models.Model):
    """블로그 포스트
    """
    title = models.CharField(max_length=100)
    content = models.TextField()
    like = models.IntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)


class Comment(models.Model):
    """포스트의 댓글"""
    post = ForeignKey(Post, on_delete=models.CASCADE)
    content = models.CharField(max_length=255)
    created_at = models.DateTimeField(auto_now_add=True)
```

```python
posts = Post.objects.all().select_related('comment') 

for post in posts: # n
    comment = Comment.objects.filter(post=post) # 이미 캐시되어 실행안됨
```

### 참고 링크

- [Django Filetersr(내장모듈)](https://yongbeomkim.github.io/django/dj-filter-orm-basic/)
- [반드시 알아야 할 5가지 ORM 쿼리](https://medium.com/@chrisjune_13837/django-%EB%8B%B9%EC%8B%A0%EC%9D%B4-%EB%AA%B0%EB%9E%90%EB%8D%98-orm-%EA%B8%B0%EC%B4%88%EC%99%80-%EC%8B%AC%ED%99%94-592a6017b5f5)


## ORM 특징 

### `Lazy_loading`

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

## Links

- [박준규님 블로그](https://velog.io/@devzunky/TIL-no.66-Django-Basic-19-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C%ED%95%98%EA%B8%B0)
- [불곰님 블로그](https://brownbears.tistory.com/63)
- [queryset](https://docs.djangoproject.com/en/3.0/ref/models/querysets/) 
- [이설님 블로그](https://lee-seul.github.io/django/2019/02/02/django-transactionmanagementerror.html)
