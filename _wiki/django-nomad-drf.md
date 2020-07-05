---
layout  : wiki
title   : 
summary : 
date    : 2020-05-22 21:23:57 +0900
updated : 2020-07-05 15:48:55 +0900
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

- [공식문서](https://docs.djangoproject.com/en/3.0/topics/serialization/)
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
    - `class RoomSerializer(serializers.Serializer)` -> 필드 속성까지 선언해줘야
- [Api-guide](https://www.django-rest-framework.org/api-guide/views/)
- [Throttling](https://www.django-rest-framework.org/api-guide/throttling/) - 요청에 제한을 걸 수 있는 방법
    - [참고블로그](https://ssungkang.tistory.com/entry/Django-Throttling)

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

```python
from rest_framework import serializers

class RoomSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=140)
    price = serializers.IntegerField()
    bedrooms = serializers.IntegerField()
    instant_book = serializers.BooleanField()
```

### [1.3 Serializers part Two](https://github.com/nomadcoders/airbnb-api/commit/322dc866e7cb3548a9e119000245372b70b96a3b)

- [Serializers](https://www.django-rest-framework.org/api-guide/serializers/)
    - [공식문서 번역 블로그](https://brownbears.tistory.com/71)
    - `class RoomSerializer(serializers.ModelSerialzer)` -> 모델 선언, 필드명 간단하게 선언
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
- [GenericAPIView](https://www.django-rest-framework.org/api-guide/generic-views/#genericapiview)


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

- `settings.py` 설정
    - [rest_framework default 설정](https://ssungkang.tistory.com/entry/Django-restframework-default-%EC%84%A4%EC%A0%95?category=366160)

```python
## confing/settings.py
# Django Rest Framework

REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10,
}

```

### [1.5 ListAPIView](https://github.com/nomadcoders/airbnb-api/commit/852b4c851ff02735a5d9499c19328762bc2e68d8)

- 리스트가 아니라 pk로 호출(RetrieveAPIView)
    - 왜 이름이 retrieve? 
        - retrieve - to get stored informaion from a computer

```python
# urls.py
urlpatterns = [
    path("<int:pk>/", views.SeeRoomView.as_view()),
]
```

```python
# serlalizer.py
class BigRoomSerializer(serializers.ModelSerializer):
    class Meta:
        model = Room
        exclude = () # fields가 아닌 exclude
```

```python
# views.py
class SeeRoomView(RetrieveAPIView):

    queryset = Room.objects.all()
    serializer_class = BigRoomSerializer
``` 

#### Reference

- [Classy Class-Based Views](http://ccbv.co.uk/) - 장고 클래스 기반 뷰의 디테일한 정보
- [Classy Django REST Framework](http://www.cdrf.co/) - DRF 클래스 기반 뷰 종류별 선언할 수 있는 변수 총정리
- [APIView, Mixins, generics APIView, ViewSet을 알아보자](https://ssungkang.tistory.com/entry/Django-APIView-Mixins-generics-APIView-ViewSet%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

### [1.6 ModelViewSet](https://github.com/nomadcoders/airbnb-api/commit/14321042d1337a9478ad75c24d5ca4632a05e4e3)

#### viewset

- 그냥 api 완성
- url 제공 - `rooms`, `rooms/1/` 모두 자동으로
    - basename에 적은 이름이 기존 url에 붙어서 나오니 api root를 확인해야
        - 기본 주소로 접속하면 Api Root를 알려준다
- PUT, DELETE 메소드까지 제공
- Pagination 등도 제공
- 그러나 모든 기능이 on 되어 있어서 비즈니스로직 [viewset actions](https://www.django-rest-framework.org/api-guide/viewsets/) 구현해야 할 수도

```python
# urls.py
from rest_framework.routers import DefaultRouter
from . import viewsets

router = DefaultRouter()
router.register("", viewsets.RoomViewset, basename="room")

urlpatterns = router.urls
```

```python
# viewset.py

from rest_framework import viewsets
from .models import Room
from .serializers import BigRoomSerializer


class RoomViewset(viewsets.ModelViewSet):

    queryset = Room.objects.all()
    serializer_class = BigRoomSerializer
```

- ViewSet actions, 공식문서 내용
- [공식문서 번역 블로그 Viewset](https://brownbears.tistory.com/82)


```python
class UserViewSet(viewsets.ViewSet):
    """
    Example empty viewset demonstrating the standard
    actions that will be handled by a router class.

    If you're using format suffixes, make sure to also include
    the `format=None` keyword argument for each action.
    """

    def list(self, request):
        pass

    def create(self, request):
        pass

    def retrieve(self, request, pk=None):
        pass

    def update(self, request, pk=None):
        pass

    def partial_update(self, request, pk=None):
        pass

    def destroy(self, request, pk=None):
        pass
```

- [Introspecting ViewSet actions](https://www.django-rest-framework.org/api-guide/viewsets/#introspecting-viewset-actions)
- ViewSet의 속성
    - `Basename` - 생성된 URL 이름을 위한 베이스
    - `action` - 현재 액션 이름(예: list, create)
    - `detail` - 현재 액션이 디테일 뷰나 리스트로 설정돼 있다면 불리언으로 표현?
    - `suffix` - 뷰셋 타입의 접미사 - `detail` 속성을 반영?
    - `name` - 뷰셋 이름, `suffix`와는 상호배타적인 인자
    - `description` - 뷰셋의 개별 뷰에 관한 설명

```python
def get_permissions(self):
    """
    Instantiates and returns the list of permissions that this view requires.
    """
    if self.action == 'list':
        permission_classes = [IsAuthenticated]
    else:
        permission_classes = [IsAdmin]
    return [permission() for permission in permission_classes]
```

- [Marking extra actions for routing](https://www.django-rest-framework.org/api-guide/viewsets/#marking-extra-actions-for-routing)

```python
from django.contrib.auth.models import User
from rest_framework import status, viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from myapp.serializers import UserSerializer, PasswordSerializer

class UserViewSet(viewsets.ModelViewSet):
    """
    A viewset that provides the standard actions
    """
    queryset = User.objects.all()
    serializer_class = UserSerializer

    @action(detail=True, methods=['post'])
    def set_password(self, request, pk=None):
        user = self.get_object()
        serializer = PasswordSerializer(data=request.data)
        if serializer.is_valid():
            user.set_password(serializer.data['password'])
            user.save()
            return Response({'status': 'password set'})
        else:
            return Response(serializer.errors,
                            status=status.HTTP_400_BAD_REQUEST)

    @action(detail=False)
    def recent_users(self, request):
        recent_users = User.objects.all().order_by('-last_login')

        page = self.paginate_queryset(recent_users)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(recent_users, many=True)
        return Response(serializer.data)
```

- 데코레이터의 인자에 라우트된 뷰를 추가할 수도 있다.
- 메소드는 get이 디폴트로 라우트 되고 다른 메소드는 추가해서 사용
- 아래와 같이 2가지 액션을 추가하면 URL은 `^users/{pk}/set_password/$`, `^users/{pk}/unset_password/$`
- 액션을 추가하라면 `.get_extra_action()` 메소드를 사용

```python
    @action(detail=True, methods=['post'], permission_classes=[IsAdminOrIsSelf])
    def set_password(self, request, pk=None):
       ...
```

### [1.7 Bye Bye ViewSet](https://github.com/nomadcoders/airbnb-api/commit/f2c32baa9b307df5f73d557ae2cf152c72d4c33a)

### [2.0 ListRoomsView & SeeRoomView](https://github.com/nomadcoders/airbnb-api/commit/ee78a1cceaab0735e92caeae6abdde5a822a82a6)

### [2.1 Create Room part One](https://github.com/nomadcoders/airbnb-api/commit/db494d5f585511aa0a3620b3a84b215b2ac79bc3)

### [2.2 Create Room part Two](https://github.com/nomadcoders/airbnb-api/commit/d502905179d671b143006b0c731a843c2dcd308d)


- [공식](https://www.django-rest-framework.org/api-guide/serializers/)
- create
    - create 메소드를 직접 콜하면 안 된다
    - save 메소드를 쓰면 create, update 등을 감지할 것
    - create 메소드를 쓰면 반드시 instance를 리턴해야

```python
# serializer.py
class WriteRoomSerializer(serializers.Serializer):

    name = serializers.CharField(max_length=140)
    address = serializers.CharField(max_length=140)
    price = serializers.IntegerField()
    beds = serializers.IntegerField(default=1)
    lat = serializers.DecimalField(max_digits=10, decimal_places=6)
    lng = serializers.DecimalField(max_digits=10, decimal_places=6)
    bedrooms = serializers.IntegerField(default=1)
    bathrooms = serializers.IntegerField(default=1)
    check_in = serializers.TimeField(default="00:00:00")
    check_out = serializers.TimeField(default="00:00:00")
    instant_book = serializers.BooleanField(default=False)

    def create(self, validated_data):
        return Room.objects.create(**validated_data)
```


```python
## views.py
@api_view(["GET", "POST"])
def rooms_view(request):
    if request.method == "GET":
        rooms = Room.objects.all()[:5]
        serializer = ReadRoomSerializer(rooms, many=True).data
        return Response(serializer)
    elif request.method == "POST":
        if not request.user.is_authenticated: # 이렇게 인증 안 해주면 에러
            return Response(status=status.HTTP_401_UNAUTHORIZED)
        serializer = WriteRoomSerializer(data=request.data)
        if serializer.is_valid():
            room = serializer.save(user=request.user)  # create()가 아니라 save()
            room_serializer = ReadRoomSerializer(room).data
            return Response(data=room_serializer, status=status.HTTP_200_OK)
        else:
            return Response(status=status.HTTP_400_BAD_REQUEST)

```

### [2.3 Room Detail GET](https://github.com/nomadcoders/airbnb-api/commit/de21510bbf93a506e81d4be816a47541bc15b01d)

- [공식](https://www.django-rest-framework.org/api-guide/validators/)
- Validate
    - return해야

```python
# serializer.py
...
    def create(self, validated_data):
        return Room.objects.create(**validated_data)

    def validate(self, data):
        check_in = data.get("check_in")
        check_out = data.get("check_out")
        if check_in == check_out:
            raise serializers.ValidationError("Not enough time between changes")
        else:
            return data
```

### [2.4 Room Detail DELETE PUT part One](https://github.com/nomadcoders/airbnb-api/commit/4d6d41df504ec9a24ed2287a7dc968ae0c54d410)



## Link

- [에어비앤비 앱 클론 코딩](https://nomadcoders.co/airbnb-native/lectures/965)

