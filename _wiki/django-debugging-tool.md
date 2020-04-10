---
layout  : wiki
title   : django-debugging-tool 
summary : 
date    : 2020-03-08 18:14:06 +0900
updated : 2020-04-07 14:43:16 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## django-extensions

```shell
$ pip install django_extensions
$ pip install Werkzeug
```

```python
## filename: settings.py

if DEBUG:
    INSTALLED_APPS += [
        'django_extensions',
]
```

```shell
$ python magage.py
"추가된 명령어 확인"
```

```shell
$ python manage.py runservier_plus
$ python manage.py shell_plus
$ python manage.py shell_plus --notebook
```

- runserver_plus - 오류 지점을 클릭하면 파이썬 콘솔이 뜬다.
    - `locals()` - 로컬 디비 출     - `1/0` - 원하는 위치 오류
- shell_plus - app들을 기본 임포

## django-debud-toolbar

```python
$ pip install django_debug_toolbar
```

```python
filename: settings.py

INSTALLED_APPS = [
    'debug_toolbar'
]
```

```python
MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]
```

```python
DEBUG_TOOLBAR_PANELS = [
    'debug_toolbar.panels.versions.VersionsPanel',
    'debug_toolbar.panels.timer.TimerPanel',
    'debug_toolbar.panels.settings.SettingsPanel',
    'debug_toolbar.panels.headers.HeadersPanel',
    'debug_toolbar.panels.request.RequestPanel',
    'debug_toolbar.panels.sql.SQLPanel',
    'debug_toolbar.panels.staticfiles.StaticFilesPanel',
    'debug_toolbar.panels.templates.TemplatesPanel',
    'debug_toolbar.panels.cache.CachePanel',
    'debug_toolbar.panels.signals.SignalsPanel',
    'debug_toolbar.panels.logging.LoggingPanel',
    'debug_toolbar.panels.redirects.RedirectsPanel',
    'debug_toolbar.panels.profiling.ProfilingPanel'
]
```

- 우측에 페이지로드하기 위해 사용한 `sql` 열람 가능
    - `explained` 실행계획
    - `duplicated 4 times` - 같은 쿼리 4번이나 실행됐으니 개선 필요 알림


## ngrok

- 포트 포워딩할 수 없는 환경에 퍼블릭 접근 가능한 도메인 부여
- https도 가능

```shell
$ brew cask install ngrok
```

```shell
$ ngrok http 8000
```

- `localhost:8000/inscpect/http`
    - 터널을 통해 접근한 요청을 모두 볼 수 있다

```python
$ ngrok authtoken <YOUR_AUTHTOKEN>
$ ngrok http 80 --authtoken={Auth-Token}
```

- auth 연결

## [Faker](https://factoryboy.readthedocs.io/en/latest/)

```python
from faker import Faker
fake = Faker()

email = fake.email()
username = fake.user_name()
age = fake.pyint(min_value=0, max_value=100)
```

```python
def test_post(self):
    post = Post.objects.create(
        blog=self.blog,
        title=faker.sentence(),
        tags=[faker.word()]
    )
```
- [raccoony님 블로그](https://www.44bits.io/ko/post/faker-and-factory-boy-for-clean-code-on-python-test)


```shell
$ manage.py test -v 3
```

- [in-menory DB](https://pypi.org/project/django-memdb/) -> 매번 깨끗한 test DB
- [DRF의 APITestCase Class](https://www.django-rest-framework.org/api-guide/testing/)
- [factory_boy](https://factoryboy.readthedocs.io/en/latest/) -> random parameter value generator
- [Vagrant Cloud](https://app.vagrantup.com/mvbcoding/boxes/awslinux/) - os 달라도 같은 가상환경 생성

## Links

- [django debugging 할 때 유용하게 쓸 수 있는 도구들 - 김슬](https://www.youtube.com/watch?v=VUGNYr_GxEY&t=265s)
- [django api server unit test and remote debugging](https://www.slideshare.net/addnull/20170813-django-api-server-unit-test-and-remote-debugging)
- [깔끔한 파이썬 테스트 코드를 위한 Faker와 Factory Boy](https://www.44bits.io/ko/post/faker-and-factory-boy-for-clean-code-on-python-test)
