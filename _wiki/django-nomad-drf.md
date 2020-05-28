---
layout  : wiki
title   : 
summary : 
date    : 2020-05-22 21:23:57 +0900
updated : 2020-05-27 22:01:52 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Introduction to DRF

### 환경설정

```python
$ git clone https://github.com/nomadcoders/airbnb-api --branch blueprint --single-branch awesome-api-course
$ git remote -v
$ rm -rf .git

# 내 레파지토리 생성
$ git remote add origin <repo 주소>
```

```python
$ pipenv shell
$ pipenv install
$ python manage.py createsueruser
$ python manage.py mega_seed

++++
mega_seed에서 오류날 경우
django_seed/__init.__.py 35번째 줄
cls.fakers[code].seed(random.randint(1, 10000)) 를
cls.fakers[code].seed_instance(random.randint(1, 10000)) 로 수정
++++
```

### [1.0 APIs the Django Way](https://github.com/nomadcoders/airbnb-api/commit/05122a1646d411438be9c24e4eadcc320827a174)

- 쿼리셋을 JSON으로 변환할 수 없다
- For문을 돌려도 JSON 변환 안 된다

```python
import json
from django.http import HttpResponse
from rooms.models import Room

def list_rooms(request):
    rooms = Room.objects.all()
    rooms_json = []
    for room in rooms:
        rooms_json.append(json.dumps(room))
    response = HttpResponse(content=rooms_json)
    return response
```

- Serializers -> JSON 변환

```python
from django.core import serializers
from django.http import HttpResponse
from rooms.models import Room

def list_rooms(request):
    data = serializers.serialize("json", Room.objects.all())
    response = HttpResponse(content=data)
    return response
```

### [1.1 @api_view](https://github.com/nomadcoders/airbnb-api/commit/ae7d2e25239ed3ce5191b35f774c2fdb18cc1835)

- 심플한 drf

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(["GET"])
def list_rooms(request):
    return Response()
```


### [1.2 Serializers](https://github.com/nomadcoders/airbnb-api/commit/f352b8466566921099c9492c161d3ed9fef6b5f9)

- [Serializers](https://www.django-rest-framework.org/)
    - 쿼리셋과 같은 복잡한 데이터를 JSON, XML로 쉽게 변환, 그 반대도 가
    - 응답을 제어하는 폼과 같은 역할


```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Room
from .serializers import RoomSerializer


@api_view(["GET"])
def list_rooms(request):
    rooms = Room.objects.all()
    serialized_rooms = RoomSerializer(rooms, many=True) # many=True -> 리스트 가능
    return Response(data=serialized_rooms.data)
```

### [1.3 Serializers part Two](https://github.com/nomadcoders/airbnb-api/commit/322dc866e7cb3548a9e119000245372b70b96a3b)

- 외래키로 이어진 데이터 가져오는 방법
- 위처럼 모델을 나열하지 않고 필드명만 써줘도 된다

```python
from rest_framework import serializers
from users.serializers import TinyUserSerializer
from .models import Room

class RoomSerializer(serializers.ModelSerializer):

    user = TinyUserSerializer()  # 다른 테이블 가져와서 선언만 해주면 끝

    class Meta:
        model = Room
        fields = ("name", "price", "instant_book", "user")
```


### [1.4 Class Based Views](https://github.com/nomadcoders/airbnb-api/commit/934c61e2996f6d51ec61172557757dfc7de6c652)

#### APIView

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Room
from .serializers import RoomSerializer

class ListRoomView(APIView):
    def get(self, request):
        rooms = Room.objects.raw("select * from rooms_Room limit 20")
        serializer = RoomSerializer(rooms, many=True)
        return Response(serializer.data)
```

#### ListAPIView

- 대단히 간단하다...

```python
## rooms/views.py
from rest_framework.generics import ListAPIView
from .models import Room
from .serializers import RoomSerializer

class ListRoomsView(ListAPIView):

    queryset = Room.objects.all()
    serializer_class = RoomSerializer
```

#### Pagination

```python
## confing/settings.py
# Django Rest Framework

REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10,
}

```


