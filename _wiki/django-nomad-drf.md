---
layout  : wiki
title   : 
summary : 
date    : 2020-05-22 21:23:57 +0900
updated : 2020-07-09 19:52:37 +0900
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
# filename: serializer.py

from rest_framework import serializers
from .models import Customer

class ReadCustomerSerializer(serializers.ModelSerializer):
    class Meta:
        model = Customer
        fields = "__all__"

class WriteCustomerSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=50)
    password = serializers.CharField(max_length=100)
    
    def create(self, validated_data):
        return Customer.objects.create(**validated_data)
```

```python
# filename: views.py

from .serializer import ReadCustomerSerializer, WriteCustomerSerializer
from rest_framework.generics import RetrieveAPIView
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status

@api_view(["GET", "POST"])
def customer_view(request):
    if request.method == "GET":
        customer = Customer.objects.all()
        serializer = ReadCustomerSerializer(customer, many=True).data
        return Response(serializer)
    elif request.method == "POST":
        if not request.user.is_authenticated:
            return Response(status=status.HTTP_401_UNAUTHORIZED)
        serializer = WriteCustomerSerializer(data=request.data)
        if serializer.is_valid():
            customer = serializer.save(customer=request.customer) # create 메소르 직접 콜하면 안 되고 save 메소드를 쓰면 시리얼라이즈에 정의된 create나 update를 선택적으로 불러온다
            customer_serializer = ReadCustomerSerializer(customer).data
            return Response(data=customer_serializer, status=status.HTTP_200_OK)
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

```python
class RoomView(APIView):
    def get_room(self, pk):
        try:
            room = Room.objects.get(pk=pk)
            return room
        except Room.DoesNotExist:
            return None

    def get(self, request, pk):
        room = self.get_room(pk)
        if room is not None:
            serializer = ReadRoomSerializer(room).data
            return Response(serializer)
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)

    def put(self, request):
        customer = self.get_customer(pk)
        if customer is not None:
            if customer.name != request.user:
                return Response(status=status.HTTP_403_FORBIDDEN)
            serializer = WriteCustomerSerializer(
                customer, data=request.data, partial=True
            )
            if serializer.is_valid():
                customer = serializer.save()
                return Response(ReadCustomerSerializer(customer).data)
            else:
                return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
            return Response()
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)
            
    def delete(self, request, pk):
        room = self.get_room(pk)
        if room is not None:
            if room.user != request.user:
                return Response(status=status.HTTP_403_FORBIDDEN)
            room.delete()
            return Response(status=status.HTTP_200_OK)
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)
```

### [2.5 Room Detail PUT part Two](https://nomadcoders.co/airbnb-native/lectures/970)

```python
class WriteCustomerSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=50)
    password = serializers.CharField(max_length=100)

    def update(self, instance, validated_data):
        instance.name = validated_data.get("name", instance.name)
        instance.password = validated_data.get("password", instance.password)
        instance.save()
        return instance
```

### [2.6 MeView and user_detail](https://nomadcoders.co/airbnb-native/lectures/972)

### [2.7 MeView PUT](https://github.com/nomadcoders/airbnb-api/commit/118908ad4db98526111ce6b0f5705c7f7c607742)

- Permissions
    - [공식](https://www.django-rest-framework.org/api-guide/permissions/)
    - [Django REST Framework -Permissions](https://dean-kim.github.io/rest_framework/2017/05/22/Django-REST-Framework-Permissions.html)

```python
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from rest_framework.decorators import api_view
from rest_framework.views import APIView
from rest_framework import status
from .models import User
from .serializers import ReadUserSerializer, WriteUserSerializer

class MeView(APIView):

    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response(ReadUserSerializer(request.user).data)

    def put(self, request):
        serializer = WriteUserSerializer(request.user, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response()
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(["GET"])
def user_detail(request, pk):
    try:
        user = User.objects.get(pk=pk)
        return Response(ReadUserSerializer(user).data)
    except User.DoesNotExist:
        return Response(status=status.HTTP_404_NOT_FOUND)
```

### [2.8 Magic + FavsView](https://github.com/nomadcoders/airbnb-api/commit/6849428754f659dc961ce3aee0bf8f1ecaf9d8bc)

- `read_only_fields` - get에 쓸 필드 할당하고 Serializer를 get, post 한꺼번에 사용

```python
class RoomSerializer(serializers.ModelSerializer):

    user = RelatedUserSerializer()

    class Meta:
        model = Room
        exclude = ("modified",)
        read_only_fields = ("user", "id", "created", "updated")

```

```python
from rest_framework import serializers
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Room
from .serializers import RoomSerializer


class RoomsView(APIView):
    def get(self, request):
        rooms = Room.objects.all()[:5]
        serializer = RoomSerializer(rooms, many=True).data
        return Response(serializer)

    def post(self, request):
        if not request.user.is_authenticated:
            return Response(status=status.HTTP_401_UNAUTHORIZED)
        serializer = RoomSerializer(data=request.data)
        if serializer.is_valid():
            room = serializer.save(user=request.user)
            room_serializer = RoomSerializer(room).data
            return Response(data=room_serializer, status=status.HTTP_200_OK)
        else:
            return Response(data=serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class RoomView(APIView):
    def get_room(self, pk):
        try:
            room = Room.objects.get(pk=pk)
            return room
        except Room.DoesNotExist:
            return None

    def get(self, request, pk):
        room = self.get_room(pk)
        if room is not None:
            serializer = RoomSerializer(room).data
            return Response(serializer)
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)

    def put(self, request, pk):
        room = self.get_room(pk)
        if room is not None:
            if room.user != request.user:
                return Response(status=status.HTTP_403_FORBIDDEN)
            serializer = RoomSerializer(room, data=request.data, partial=True)
            if serializer.is_valid():
                room = serializer.save()
                return Response(RoomSerializer(room).data)
            else:
                return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
            return Response()
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)

    def delete(self, request, pk):
        room = self.get_room(pk)
        if room is not None:
            if room.user != request.user:
                return Response(status=status.HTTP_403_FORBIDDEN)
            room.delete()
            return Response(status=status.HTTP_200_OK)
        else:
            return Response(status=status.HTTP_404_NOT_FOUND)

```

### [2.9 FavsView part Two](https://github.com/nomadcoders/airbnb-api/commit/9f9d44c8614ed0fc1ffc1230b933660007885a1b)


### [2.10 Creating Account](https://github.com/nomadcoders/airbnb-api/commit/c2af2ca510cc9387619eee864a85ffe54dfe43e1)

- 패스워드 설정

```python
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):

    password = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = (
            "id",
            "username",
            "first_name",
            "last_name",
            "email",
            "avatar",
            "superhost",
            "password",
        )
        read_only_fields = ("id", "superhost", "avatar")

    def validate_first_name(self, value):
        return value.upper()

    def create(self, validated_data):
        password = validated_data.get("password")
        user = super().create(validated_data)
        user.set_password(password)
        user.save()
        return user
```

```python
class UsersView(APIView):
    def post(self, request):
        serializer = UserSerializer(data=request.data)
        if serializer.is_valid():
            new_user = serializer.save()
            return Response(UserSerializer(new_user).data)
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

### [2.11 Log In (JWT)](https://github.com/nomadcoders/airbnb-api/commit/b2badc5651be7f3862c4af0cfcb723e29bc0a44e)


- [authenticate 공식](https://docs.djangoproject.com/en/3.0/topics/auth/default/)
- [인증(Authentication)과 인가(Authorization)](https://velog.io/@aaronddy/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)
- [JWT](https://jwt.io/)
    - 민감한 데이터(패스워드, 이메일, 유저이름)를 JWT에 넣으면 안 되고 식별 가능한 데이터(예: pk)를 넣어야 한다. 누구나 JWT를 해독할 수 있기 때문. 공식 사이트에서 토큰 넣고 Decode -> pk 나옴
        - 토큰 안의 정보를 아무도 못보게 만드는 게 목적이 아니라 토큰을 아무도 건드리지 않았는지 판단하는 게 목적
    - settings.py 직접 import하면 안 되고 `from django.conf import settings` 

```python
$ pip install pyjwt
```

```python
import jwt
from django.conf import settings
from django.contrib.auth import authenticate

@api_view(["POST"])
def login(request):
    username = request.data.get("username")
    password = request.data.get("password")
    if not username or not password:
        return Response(status=status.HTTP_400_BAD_REQUEST)
    user = authenticate(username=username, password=password)
    if user is not None:
        encoded_jwt = jwt.encode(
            {"id": user.pk}, settings.SECRET_KEY, algorithm="HS256"
        )
        return Response(data={"token": encoded_jwt})
    else:
        return Response(status=status.HTTP_401_UNAUTHORIZED)
```

### [2.12 JWT Decoding and Auth](https://github.com/nomadcoders/airbnb-api/commit/b5c8990172b2ee5dd65298daafdfb0b956c686a0)

- [Custom authentication 공식](https://www.django-rest-framework.org/api-guide/authentication/#custom-authentication)
- 참고 - [restframework용 simple JWT](https://github.com/SimpleJWT/django-rest-framework-simplejwt)
- 헤더에 {'Authorization': 'X-JWT 토큰'} 넣어 get으로 MeView로 보낸다

```python
# filename :config/authentication.py 

import jwt
from django.conf import settings
from rest_framework import authentication
from users.models import User


class JWTAuthentication(authentication.BaseAuthentication):
    def authenticate(self, request):
        try:
            token = request.META.get("HTTP_AUTHORIZATION")
            if token is None:
                return None
            xjwt, jwt_token = token.split(" ")
            decoded = jwt.decode(jwt_token, settings.SECRET_KEY, algorithms=["HS256"])
            pk = decoded.get("pk")
            user = User.objects.get(pk=pk)
            return (user, None)
        except (ValueError, jwt.exceptions.DecodeError, User.DoesNotExist):
            return None
```

```python
# filename: config/settings.py

REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10,
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "config.authentication.JWTAuthentication",
        "rest_framework.authentication.SessionAuthentication",
    ],
}
```

```python
class MeView(APIView):

    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response(UserSerializer(request.user).data)

    def put(self, request):
        serializer = UserSerializer(request.user, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response()
        else:
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```


### [2.14 Manual Pagination](https://github.com/nomadcoders/airbnb-api/commit/6439a0303edbf529cf1c4d4de6a72c387103e868)

- [Pagination](https://www.django-rest-framework.org/api-guide/pagination/#pagination)
- url뒤에 `/?page=1` 붙여서 테스트

```python
from rest_framework.decorators import api_view
from rest_framework.pagination import PageNumberPagination

class OwnPagination(PageNumberPagination):
    page_size = 20


class RoomsView(APIView):
    def get(self, request):
        paginator = OwnPagination()
        rooms = Room.objects.all()
        results = paginator.paginate_queryset(rooms, request)
        serializer = RoomSerializer(results, many=True)
        return paginator.get_paginated_response(serializer.data)
```

### [2.15 Searching Part One](https://github.com/nomadcoders/airbnb-api/commit/b0e32969e0d25622f934ce3e5dd405f7f7afdfff)

- [Filtering](https://www.django-rest-framework.org/api-guide/filtering/)

```python
@api_view(["GET"])
def room_search(request):
    max_price = request.GET.get("max_price", None)
    min_price = request.GET.get("min_price", None)
    beds = request.GET.get("beds", None)
    bedrooms = request.GET.get("bedrooms", None)
    bathrooms = request.GET.get("bathrooms", None)
    filter_kwargs = {}
    if max_price is not None:
        filter_kwargs["price__lte"] = max_price
    if min_price is not None:
        filter_kwargs["price__gte"] = min_price
    if beds is not None:
        filter_kwargs["beds__gte"] = beds
    if bedrooms is not None:
        filter_kwargs["bedrooms__gte"] = bedrooms
    if bathrooms is not None:
        filter_kwargs["bathrooms__gte"] = bathrooms
    paginator = OwnPagination()
    try:
        rooms = Room.objects.filter(**filter_kwargs)
    except ValueError:
        rooms = Room.objects.all()
    results = paginator.paginate_queryset(rooms, request)
    serializer = RoomSerializer(results, many=True)
    return paginator.get_paginated_response(serializer.data)
```

### [2.16 Searching Part Two](https://github.com/nomadcoders/airbnb-api/commit/d459f76600a9f6bb03a7a8d8781ce116c9d49851)


```python
...
    lat = request.GET.get("lat", None)
    lng = request.GET.get("lng", None)
...    
    if lat is not None and lng is not None:
        filter_kwargs["lat__gte"] = float(lat) - 0.005
        filter_kwargs["lat__lte"] = float(lat) + 0.005
        filter_kwargs["lng__gte"] = float(lng) - 0.005
        filter_kwargs["lng__lte"] = float(lng) + 0.005
```

### [3.0 This is super important. Watch this](https://github.com/nomadcoders/airbnb-api/commit/a143f0917c6b303a71dcf083ddc4fd8a17f6a668)

- dynamic field - 페이이를 요청하는 유저에 따라 바뀌는 핖드
- [SerializerMethodField](https://www.django-rest-framework.org/api-guide/fields/#serializermethodfield) - 함수
    - 함수명 앞에 `get_`을 붙여줘야 함 
- views에서 context = {'request': request} -> request로 누가 요청하는지 등의 정보를 알 수 있다

```python
class RoomSerializer(serializers.ModelSerializer):

    user = UserSerializer()
    is_fav = serializers.SerializerMethodField()
    
    def get_is_fav(self, obj):
    request = self.context.get("request")
    if request:
        user = request.user
        if user.is_authenticated:
            return obj in user.favs.all()
    return False
```

```python
class RoomsView(APIView):
    def get(self, request):
        paginator = OwnPagination()
        rooms = Room.objects.all()
        results = paginator.paginate_queryset(rooms, request)
        serializer = RoomSerializer(results, many=True, context={"request": request})
        return paginator.get_paginated_response(serializer.data)
```

### [3.1 RoomViewSet permissions](https://github.com/nomadcoders/airbnb-api/commit/0be8f0efe8ab40eaa15b11fddeed3aa36307ef41)

- [Viewset](https://www.django-rest-framework.org/api-guide/viewsets/#introspecting-viewset-actions)
- Viewset이 모두 지원하지만 인증되지 않은 유저도 정보를 바꿀 수 있다.
- `get_permission`을 사용해서 조정
    - list - GET - /rooms
    - create - POST - /rooms
    - retrieve - get - /rooms/1

### [3.2 RoomViewSet IsOwner](https://github.com/nomadcoders/airbnb-api/commit/ab702bda7d57451d1d450c6bc08fa54737fc8303)

- [Custom Permission](https://www.django-rest-framework.org/api-guide/permissions/#custom-permissions)
    - `has_object_permission(self, request, view, obj)` - `get_object` 메소드를 덮어쓰거나 자신만의 뷰를 만들고 싶을 때? 인스턴스 수준의 Permission 
        - 하나의 오브젝트에 접근할 필요가 있을 때 사용
        - 별도 호출 과정 필요
    - `has_permission` - 뷰 수준의 permission 
        - 요청이 들어올 떄 자동 실행
        
```python
# filename : rooms/urls.py
from rest_framework.routers import DefaultRouter
from . import views

app_name = "rooms"
router = DefaultRouter()
router.register("", views.RoomViewSet)

urlpatterns = router.urls
```

```python
# filename : rooms/permissions.py
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    def has_object_permission(self, request, view, room):
        return room.user == request.user
```

```python
# filename: rooms/views.py
from rest_framework.viewsets import ModelViewSet
from rest_framework import permissions
from .models import Room
from .serializers import RoomSerializer
from .permissions import IsOwner

class RoomViewSet(ModelViewSet):

    queryset = Room.objects.all()
    serializer_class = RoomSerializer

    def get_permissions(self):

        if [[self.action]] == "list" or self.action == "retrieve":
            permission_classes = [permissions.AllowAny]
        elif self.action == "create":
            permission_classes = [permissions.IsAuthenticated]
        else:
            permission_classes = [IsOwner]
        return [permission() for permission in permission_classes]
```

### [3.3 I Will Marry DRF (Create Room Logic)](https://github.com/nomadcoders/airbnb-api/commit/166df6196b0b73c18f4e3d19b4d01c6078cd9282)

- [Writable nested representations](https://www.django-rest-framework.org/api-guide/serializers/#writable-nested-representations)
- [get_serializer_context](http://www.cdrf.co/3.9/rest_framework.viewsets/ModelViewSet.html)
    - 추가적인 Context를 serializer로 전달

```python
    def get_serializer_context(self):
        """
        Extra context provided to the serializer class.
        """
        return {
            'request': self.request,
            'format': self.format_kwarg,
            'view': self
        }
```

- (read_only=True)

```python
from rest_framework import serializers
from users.serializers import UserSerializer
from .models import Room

class RoomSerializer(serializers.ModelSerializer):

    user = UserSerializer(read_only=True)
    is_fav = serializers.SerializerMethodField()
    
    def create(self, validated_data):
        print(self.context.get("request").user)
        request = self.context.get("request")
        room = Room.objects.create(**validated_data, user=request.user)
        return room
``` 

### [3.4 Including search in Viewset](https://nomadcoders.co/airbnb-native/lectures/1023)

- [Marking extra actions for routing](https://www.django-rest-framework.org/api-guide/viewsets/#marking-extra-actions-for-routing)
    - action을 정할 때 detail 설정해야
        - True -> /rooms/1, False -> rooms/
        - action의 url 주소는 함수명
            - 주소명 변경을 원하면 action(url_path='')로

### [3.5 Users Viewset](https://github.com/nomadcoders/airbnb-api/commit/5eb108e1990f7217f2d8275624c0d414f9a34b02)

### [3.6 Permissions And Login](https://github.com/nomadcoders/airbnb-api/commit/de5850328bfbef935b891898eb5600fb7609954b)

### [3.7 Favs](https://github.com/nomadcoders/airbnb-api/commit/a257d5db6b37d4207ea8656e510891d1eac5c600)

### [3.8 Conclusions](https://github.com/nomadcoders/airbnb-api/commit/686130776ae35639d34efabc2399c018a1c1b059)

- aws 엘라스틱빈토크로 배포 시 설정에 반드시 아래 내용 넣어야
    - 안 넣으면 AWS에서 헤더의 내용을 삭제
    - [Apache mod_wsgi specific configuration](https://www.django-rest-framework.org/api-guide/authentication/#apache-mod_wsgi-specific-configuration)
- Debug = False일 때는 JSONRenderer를 작동해서 유저가 DRF 화면 볼 수 없도록?

```python
# config/settings.py
if not DEBUG:
    REST_FRAMEWORK["DEFAULT_RENDERER_CLASSES"] = [
        "rest_framework.renderers.JSONRenderer",
    ]
```

## Link

- [에어비앤비 앱 클론 코딩](https://nomadcoders.co/airbnb-native/lectures/965)

