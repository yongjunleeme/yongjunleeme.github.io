---
layout  : wiki
title   : django-cache
summary : 
date    : 2020-03-26 13:33:47 +0900
updated : 2020-03-29 19:23:43 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 캐시란?

```python
given a URL, try finding that page in the cache

if the page is in the cache:
   return the cached page
else:
   generate the page
   save the generated page in the cache (for next time)
   return the generated page
```

- 참고 URL
    - [데이터베이스 접속 최적화에 대한 장고 공식문서](https://docs.djangoproject.com/ko/1.11/topics/db/optimization/)
    - [서드파티 캐시 패키지](https://djangopackages.org/grids/g/caching/)
    - [tsd-8percent-장고 성능 향상시키기](https://github.com/8percent/tsd/blob/master/chapter24/summary.md)

## [Memcached](www.memcached.org)

Memcached는 메모리 기반 캐시 프레임워크다. 메인 메모리의 특정 부분을 캐시 공간으로 바꾼다. 전체 메모리를 차지하지 않는다. 멀티플 서버로 운영 가능하다.

### [설치](https://pypi.org/project/python-memcached/)  

```shell
$ pip install python-memcached
```

### `settings.py`

```python
## filename: settings.py

CACHES = {
   'default': {
      'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
      'LOCATION': '127.0.0.1:8000',
   }
}

INTERNAL_IPS = [
    '127.0.0.1'
]
```

Or

```python
CACHES = {
   'default': {
      'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
      'LOCATION': 'unix:/tmp/memcached.sock',
   }
}

```


## 전체 사이트 캐싱

### 설치

```shell
$ pip install python-memcached
```

### `settings.py`

```python
## settings.py

MIDDLEWARE_CLASSES += (
   'django.middleware.cache.UpdateCacheMiddleware',
   'django.middleware.common.CommonMiddleware',
   'django.middleware.cache.FetchFromCacheMiddleware',
)

```

## 뷰 단위 캐싱

### `urls.py`

```python
from django.views.decorators.cache import cache_page

urlpatterns = [ url(r'^foo/([0-9]{1,2})/$', cache_page(60 * 15)(my_view)),
]
```

Or

```python
path('/ranking', cache_page(60 * 15)(RankingView.as_view()))
```

## 유저 캐싱

### `models.py`

```python
class Profile(models.Model):
	user = models.OneToOneField(Settings.AUTH_USER_MODEL)
	cash = models.INtegerField(default=0)

user.profile.cach로 접근
```

## etc

- [장고 공식문서 캐시](https://docs.djangoproject.com/en/3.0/topics/cache/]
- [ORM cache Cacheops]([https://github.com/Suor/django-cacheops](https://github.com/Suor/django-cacheops))
- [django-cache-header]([https://pypi.org/project/django-cache-headers/](https://pypi.org/project/django-cache-headers/))

## Links

- [tutorialspoint](https://www.tutorialspoint.com/django/django_caching.htm)
- [Data Flair](https://data-flair.training/blogs/django-caching/)
- [yurim learned](http://milooy.github.io/TIL/Django/django-cache.html#%EC%BA%90%EC%8B%9C)
- [Kyoung Up Jung](https://www.slideshare.net/perhapsspy/django-42665652)
