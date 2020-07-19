---
layout  : wiki
title   : 
summary : 
date    : 2020-07-18 15:17:52 +0900
updated : 2020-07-19 20:48:22 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Throttle

- 기간당 최대 요청 수 설정
- DRF는 rest_framework.throttleing 모듈에서 3개의 스로틀 클래스 지원
    - 이들 모두는 SimpleRateThrottle 클래스의 서브 클래스
- 범위를 지정하는 데 사용된 이전 요청 정보는 캐시에 저장
    - 이들 클래스는 범위를 결정하는 get_cache_key 메서드를 오버라이드
- AnnoRateThrottle - 익명의 사용자 요청 비율을 제한. 요청 IP 주소는 고유한 캐시 키이므로 동일한 IP 주소에서 오는 모든 요청은 총 요청 수로 누적된다.
- UserRateThrottle - 특정 사용자가 요청할 수 있는 속도를 제한. 인증된 사용자의 경우 인증된 사용자 ID가 고유한 캐시 키가 된다. 익명의 사용자라면 요청 IP 주소가 고유한 캐시 키가 된다.
- ScopedRateThrottle - throttle_scope 속성에 할당된 값으로 식별되는 API의 특정 부분에 대한 요청 비율을 제한한다. 이 클래스는 API의 특정 부분에 대한 접근을 다른 비율로 제한할 경우 유용하다.

### 스로틀 정책 구정

#### settings.py

```python
# settings.py

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS':
    'games.pagination.LimitOffsetPaginationWithMaxLimit',
    'PAGE_SIZE': 5,
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        ),
    'DEFAULT_THROTTLE_CLASSES': (
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ),
    'DEFAULT_THROTTLE_RATES': {
        'anon': '5/hour',
        'user': '20/hour',
        'game-categories': '30/hour',
    }
}
```

- `DEFAULT_THROTTLE_RATES` - 기본 스로틀 비율의 딕셔너리를 지정
    - `anon` - 키에 지정한 값은 익명 사용자에 대해 시간당 최대 5개의 요청을 허용한다는 의미
    - `user` - 키에 지정한 값은 인증된 사용자에 대해 시간당 최대 20개 요청을 허용한다는 의미
    - `game-categories` 해당 이름의 범위에 대해 30개 요청을 허용
    - 값의 형식 - `number_ofrequests/period`
    - period - s, sec, m, min, h, hort, d, day

#### views.py

```python
from rest_framework.throttling import ScopedRateThrottle

class GameCategoryList(generics.ListCreateAPIView):
    queryset = GameCategory.objects.all()
    serializer_class = GameCategorySerializer
    name = 'gamecategory-list'
    throttle_scope = 'game-categories' ##
    throttle_classes = (ScopedRateThrottle,) ##


class GameCategoryDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = GameCategory.objects.all()
    serializer_class = GameCategorySerializer
    name = 'gamecategory-detail'
    throttle_scope = 'game-categories'##
    throttle_classes = (ScopedRateThrottle,) ##
```

- `ScopedRateThrottle` 클래스 사용 AnnoRateThrottle, UserRateThroTTle에 적용되는 글로벌 설정은 고려되지 않는다.
- `throttle_scope` 속성의 값으로 `game-categories`를 설정
- 시간당 30개 요청만 처리
- 뷰의 본문을 실행하기 전에 스로틀 클래스 점검을 수행
    - 단일 스로틀 점검이 실패하면 Throttled 예외 발생해 본문을 실행하지 않는다.
    - 이러한 요청의 스로틀 점검 정보를 캐시에 저장

#### 스로틀 정책 테스트

```python
python manage.py runserver
python manage.py runserver 0.0.0.0:8000
```

```python
# 1회 요청
$ http :8000/player-scores/

# 6회 요청 한 번에
$ for i in {1..6} do http :8000/player-scores/; done;

$ for i in {1..6} do curl -iX GET :8000/player-scroes/; done;
```

- 429 Too many reqeusts
- 응답 헤더의 `Retry-After` 키에 다음 요청(3189)까지 기다려야 하는 시간(초)가 나온다.

#####  유저 인증자격 증명

- 수퍼유저 생성

```python
$ http -a superuser:'password' :8000/player-score/

$ for i in {1..6} do http -a superuser:'password' :8000/player-scores/; done;
```

- UserRateThrottle은 기본 스로틀 클래스 중하나로 구성되며 시간당 20개 요청을 지정
- 15번 더 실행해서 20번을 채우면 더 실행하면 요청 제한

##### 게임 카테고리 

```python
$ http :8000/game-categories/

$ for i in {1..30}; do http :8000/gagme-categories/; done;
```

- ScopeRateThrottle 클래스를 스로틀 권한 제어에 사용하기 때문에 시간당 30개의 요청을 처리 

## 클래스 필터링, 검색, 정렬

- 레스트프레임워크에서 쉽게 정의할 수 있는 필터링 기능 사용

```python
$ pip install django-filter

# 브라우저 API가 다른 필터를 렌더링하는 방법을 향상시키는 패키지
$ pip install django-crispy-forms
```

```python
# settings.py
INSTALLED_APPS = [
    # Games 애플리케이션
    'game.apps.GameConfig',
    # 크리스피 양식
    'crispy_forms'
]

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS':
    'games.pagination.LimitOffsetPaginationWithMaxLimit',
    'PAGE_SIZE': 5,
    'DEFAULT_FILTER_BACKENDS': ( #
        'rest_framework.filters.DjangoFilterBackend', #
        'rest_framework.filters.SearchFilter', #
        'rest_framework.filters.OrderingFilter', #
        ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        ),
    'DEFAULT_THROTTLE_CLASSES': (
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ),
    'DEFAULT_THROTTLE_RATES': {
        'anon': '5/hour',
        'user': '20/hour',
        'game-categories': '30/hour',
    }
}
```

- `DEFAULT_FILTER_BACKENDS` 설정 키의 값은 문자열 튜플로 글로벌 설정을 지정
    - `rest_framework.filters.DjangoFilterBackend` - 필터링 기능을 제공. `Django-filter` 패키지를 사용. 필터링할 수 있는 필드 세트를 지정하거나 좀 더 사용자 정의된 설정이 들어간 `rest_framework.filters.FilterSet` 클래스를 만들어 뷰와 연결할 수 있다.
    - `rest_framework.filters.SearchFilter` - 단일 쿼리 매개 변수 기반 검색 기능을 제공하며 장고 관리자의 검색 함수에 기반을 둔다. 검색에 포함할 필드 세트를 지정할 수 있으며, 클라이언트는 단일 쿼리로 이 필드를 검색하는 쿼리를 만들어 항목을 필터링할 수 있다. 요청에서 단일 쿼리로 여러 필드를 검색할 경우에 유용하다.
    - `rest_framework.filters.OrderingFilter` - 클라이언트가 단일 쿼리 매개 변수를 사용해 결과를 정렬할는 방법을 제어할 수 있다. 또한 정렬할 필드를 지정할 수도 있다.
- 필터링 검색, 정렬 기능에 사용하는 필드는 쿼리에 영향을 미치므로 적절한 데이터베이스 최적화가 이뤄져야 한다.

### 뷰의 필터링, 검색, 정렬에 대한 구성

```python
# views.py
from rest_framework import filters
from django_filters import NumberFilter, DateTimeFilter, AllValuesFilter

class GameCategoryList(generics.ListCreateAPIView):
    queryset = GameCategory.objects.all()
    serializer_class = GameCategorySerializer
    name = 'gamecategory-list'
    throttle_scope = 'game-categories'
    throttle_classes = (ScopedRateThrottle,)
    filter_fields = ('name',) #
    search_fields = ('^name',) #
    ordering_fields = ('name',) #
```

- `filter_fields` 속성은 필터링할 수 있는 필터 이름을 나타내는 튜플을 지정한다. 장고 레스트 프레임워크는 내부에서 자동으로 rest_framework.filters.FilterSet 클래스를 생성하고 이를 GameCategoryList 뷰에 연결한다. 그러면 name 필드를 기준으로 필터링할 수 있다.
- `search_fields` 속성은 검색용 텍스트 타입 필드 이름을 나타내는 문자열의 튜플을 지정한다. 여기서는 이름 필드에 대해서만 검색하고 시작 부분 일치 검색을 수행할 것이다. 필드 이름의 접두사로 포함한 '^'은 검색 동작을 시작 부분 일치로 제한한다는 의미다.
- `ordering_fields` 속성은 클라이언트가 결과를 정렬하기 위해 지정할 수 있는 필드 이름을 나타내는 값의 튜플을 지정한다? 클라이언트가 정렬을 위한 필드를 지정하지 않은 경우 응답은 뷰와 관련 모델에 표시된 기본 정렬 필드를 사용할 것이다.

```python
class GameList(generics.ListCreateAPIView):
    queryset = Game.objects.all()
    serializer_class = GameSerializer
    name = 'game-list'
    permission_classes = (
        permissions.IsAuthenticatedOrReadOnly,
        IsOwnerOrReadOnly,
        )
    filter_fields = ( #
        'name', 
        'game_category', 
        'release_date', 
        'played', 
        'owner',
        )
    search_fields = ( #
        '^name',
        )
    ordering_fields = ( #
        'name',
        'release_date',
        )

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
```

- `filter_fields` 속성에 여러 필드 이름을 지정

```python
class PlayerList(generics.ListCreateAPIView):
    queryset = Player.objects.all()
    serializer_class = PlayerSerializer
    name = 'player-list'
    filter_fields = (
        'name', 
        'gender', 
        )
    search_fields = (
        '^name',
        )
    ordering_fields = (
        'name',
        )
```

```python
class PlayerScoreFilter(filters.FilterSet):
    min_score = NumberFilter(
        name='score', lookup_expr='gte')
    max_score = NumberFilter(
        name='score', lookup_expr='lte')
    from_score_date = DateTimeFilter(
        name='score_date', lookup_expr='gte')
    to_score_date = DateTimeFilter(
        name='score_date', lookup_expr='lte')
    player_name = AllValuesFilter(
        name='player__name')
    game_name = AllValuesFilter(
        name='game__name')

    class Meta:
        model = PlayerScore
        fields = (
            'score',
            'from_score_date',
            'to_score_date',
            'min_score',
            'max_score',
            #player__name will be accessed as player_name
            'player_name',
            #game__name will be accessed as game_name
            'game_name',
            )
```

- PlayerScoreFilter는 rest_framework.filters.FilterSet 클래스의 서브 클래스
    - `min_score` - `djnago_filters.NumberFilter` 인스턴스이며 score 숫자값이 지정된 숫자보다 크거나 같은 플레이어 점수를 필터링할 수 있다. name 값은 숫자 필터가 적용되는 필드인 'score'를 나타내며 lookup_expr 값은 조회 표현식 'gte'(greater than or equal)를 나타낸다.
    - `from_score_date` - `django_filters.DateTimeFilter` 인스턴스이며 score_datetime 값이 지정된 datetime 값보다 크거나 같은 플레이어 점수를 필터링할 수 있다.
    - `player_name` - `django_filters.AllValueFilter`이다. 플레이어의 이름이 지정 문자열 값과 일치하는 플레이어 점수를 클라이언트가 필터링할 수 있게 하는 인스턴스.

### 필터링, 검색, 정렬 테스트

```python
$ python manage.py runserver

$ http :800/game-categories/?name=3D+RPG

$ http ':8000/games/?game_category=3&played=True&ordering=-release_date'
```

## 단위 테스트 설정

```python
$ pip install coverage
$ pip install django-nose
```

```python
# settings.py
INSTALLED_APPS = [
    'crispy_forms',
    'django_nose',
    ]

# nose를 사용해 테스트 실행
TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'

# nose가 게임 앱에 대한 커버리지를 측정
NOSE_ARGS = [
    '--with-coverage',
    '--cover-erase',
    '--cover-inclusive',
    '--cover-package=games',
]
```

- `--with-coverage` - 항상 테스트 커버리지 리포트를 생성하도록 지정
- `--cover-erase` - 테스트 러너가 이전 실행에서 커버리지 테스트 결과를 삭제하게 한다.
- `--cover-inclusive` - 커버리지 리포트의 작업 디렉터리 아래 있는 모든 파이썬 파일을 인클루드. 테스트 스위트의 모든 파일을 임포트하지 않을 때 테스트 커버리지에서 구멍을 발견할 수 있다. 모든 파일을 임포트하지 않을 테스트 스위트를 생성하므로 이 옵션은 정확한 테스트 커버리지 리포트를 작성하는 데 중요하다.
- `--cover-package=games` - 다루고자 하는 모듈을 나타낸다.

```python
# filename : .coveragerc
[run]
omit = *migrations*
```

- 루트 폴더에 위와 같은 텍스트파일을 만든다. coverage 유틸리티는 테스트 커버리지 리포트를 제공할 때 생성된 마이그레이션과 관련된 많은 것들을 고려하지 않을 것이다. 이 설정 파일로 더욱 정확한 테스트 커버리지 리포트를 작성할 것이다.

### 단위 테스트

```python
# gages/test.py

from django.test import TestCase
from django.core.urlresolvers import reverse
from django.utils.http import urlencode
from rest_framework import status
from rest_framework.test import APITestCase
from games.models import GameCategory


class GameCategoryTests(APITestCase):
    def create_game_category(self, name):
        url = reverse('gamecategory-list')
        data = {'name': name}
        response = self.client.post(url, data, format='json')
        return response

    def test_create_and_retrieve_game_category(self):
        """
        Ensure we can create a new GameCategory and then retrieve it
        """
        new_game_category_name = 'New Game Category'
        response = self.create_game_category(new_game_category_name)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(GameCategory.objects.count(), 1)
        self.assertEqual(
            GameCategory.objects.get().name, 
            new_game_category_name)
        print("PK {0}".format(GameCategory.objects.get().pk))
```

- create_game_category 메서드를 선언
    - self.client를 사용해서 테스트용 HTTP 요청을 쉽게 작성해 보낼 수 있게 해주는 APIClient 인스턴스에 접근
    - 생성된 URL, data, 딕셔너리, json 형식으로 post 메서드를 호출
- test_create_and_retrieve_game_category
    - assertEqual을 사용해 다음과 같은 예상 결과를 점검
        - 응답 status_code
        - 데이터베이스에서 검색한 총 GameCategory 객체 수는 1개

```python
    def test_create_duplicated_game_category(self):
        """
        Ensure we can create a new GameCategory.
        """
        url = reverse('gamecategory-list')
        new_game_category_name = 'New Game Category'
        data = {'name': new_game_category_name}
        response1 = self.create_game_category(new_game_category_name)
        self.assertEqual(
            response1.status_code, 
            status.HTTP_201_CREATED)
        response2 = self.create_game_category(new_game_category_name)
        self.assertEqual(
            response2.status_code, 
            status.HTTP_400_BAD_REQUEST)

    def test_retrieve_game_categories_list(self):
        """
        Ensure we can retrieve a game cagory
        """
        new_game_category_name = 'New Game Category'
        self.create_game_category(new_game_category_name)
        url = reverse('gamecategory-list')
        response = self.client.get(url, format='json')
        self.assertEqual(
            response.status_code, 
            status.HTTP_200_OK)
        self.assertEqual(
            response.data['count'],
            1)
        self.assertEqual(
            response.data['results'][0]['name'],
            new_game_category_name)

    def test_update_game_category(self):
        """
        Ensure we can update a single field for a game category
        """
        new_game_category_name = 'Initial Name'
        response = self.create_game_category(new_game_category_name)
        url = reverse(
            'gamecategory-detail', 
            None, 
            {response.data['pk']})
        updated_game_category_name = 'Updated Game Category Name'
        data = {'name': updated_game_category_name}
        patch_response = self.client.patch(url, data, format='json')
        self.assertEqual(
            patch_response.status_code, 
            status.HTTP_200_OK)
        self.assertEqual(
            patch_response.data['name'],
            updated_game_category_name)

    def test_filter_game_category_by_name(self):
        """
        Ensure we can filter a game category by name
        """
        game_category_name1 = 'First game category name'
        self.create_game_category(game_category_name1)
        game_caregory_name2 = 'Second game category name'
        self.create_game_category(game_caregory_name2)
        filter_by_name = { 'name' : game_category_name1 }
        url = '{0}?{1}'.format(
            reverse('gamecategory-list'),
            urlencode(filter_by_name))
        response = self.client.get(url, format='json')
        self.assertEqual(
            response.status_code, 
            status.HTTP_200_OK)
        self.assertEqual(
            response.data['count'],
            1)
        self.assertEqual(
            response.data['results'][0]['name'],
            game_category_name1)
```

- test_create_duplicated_game_category - 고유한 제약으로 인해 같은 이름의 두 게임 카테고리를 생성할 수 없는지 여부를 테스트. 두 번쨰 카테고리 이름이 중복된 HTTP POST 요청을 보내면 400 에러를 받아야 한다.
- test_retrieve_game_categories_list - 기본 키 또는 id로 특정 게임 카테고리를 얻을 수 있는지 테스트한다.
- test_update_game_category - 게임 카테고리의 단일 필드를 업데이트할 수 있는지 테스트한다.
- test_filter_game_category_by_name - 게임 카테고리를 이름에 따라 필터링할 수 있는지 테스트한다.

```python
filter_by_name = {'name': game_category_name1}
url = '{0}{1}'.format(
    reverse('gamecategory-list'),
    urlencode(filter_by_name))
```

- test_filter_game_category_by_name 메서드는 django_utils.http.urlencode 함수를 호출해 filter_by_name 딕셔너리로부터 인코딩된 URL을 생성하는데 이 딕셔너리에 검색 데이터를 필터링하는 데 사용할 필드 이름과 값을 지정한다. URL을 생성해 url 변수에 저장하는 코드다. game_categofy_name1이 'First game category name'이라면 urlencode 함수를 호출한 결과는 'name = First + game + category + name'이 될 것이다.

### 단위 테스트 실행과 테스트 커버리지 점검

- 명령행에 입력하지 않아도 사용되는 기본 명령행 옵션을 이미 구성
- 테스트러너가 수행 중인 몯느 작업을 확인하기 위해 상세 레벨 2를 사용하는 -v 2 옵션을 이용

```python
$ python manage.py test -v 2
```

- 커버리지 패키지 리포트
    - Name : 파이썬 모듈 이름
    - Stmts : 파이썬 모듈의 실행 가능 문 개수
    - Miss : 누락된 실행 가능 문 수, 실행되지 않은 문 수
    - Cover : 실행 가능 문의 커버리지
- models.py 커버리지는 매우 낮은데 Gamecategory 모델과 관련된 몇 가지 테스트만 작성했기 때문이다.

```python
$ coverage report -m
```

- Missing 열에 누락된 명령문의 행 번호 표시

```python
$ coverage html
```

- 누락된 HTML 상세 리스트
    - 웹브라우저에서 htmlcov 폴더에 생성된 index.html 파일을 열 수 있다.


## Link

- [RESTful 파이썬 웹 서비스 제작](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=113415300)
