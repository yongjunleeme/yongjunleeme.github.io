---
layout  : wiki
title   : 
summary : 
date    : 2020-03-10 14:01:08 +0900
updated : 2020-03-29 19:23:39 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}



## Test 종류

- UI Testing / End-To-End Testing
    - 처음부터 끝까지(프론트, 백, DB 모두 연결한 다음 종합적 테스트)
    - 1개 수정 후 다시 테스트 시 나머지 전부를 테스트해야
    - 비용과 시간이 가장 많이 소요. 
- Intergration Testing
    - 내가 담당한 일부분만 테스트(예:서버의 일부분만 띄워서)
- Unit Testing
    - 가장 작은 단위 테스트(예: 함수)
    - 코드로 코드를 테스트(테스트 함수를 코딩해야)
- 테스트 피라미드 (E2E < Integration < Unit)
- 코딩과 테스트 비율 5:5까지 써야
- unit 테스트 끝나면 코드 지워야 다른 코드에 영향 X
- 테스트끼리는 독립적으로 운영
    - 예) 뷰, 서비스, 모델 따로 모두


## [Django unit-test]](https://docs.djangoproject.com/en/dev/topics/testing/overview/)


### unittest 모듈

```python
from selenium import webdriver
import unittest

class NewVisitorTest(unittest.TestCase):                    # 1
    
    def setUp(self):                                        # 2
        self.browser = webdriver.Firefox()
        
    def tearDown(self):                                     # 3
        self.browser.quit()
        
    def test_can_start_a_list_and_retrieve_it_later(self):  # 4
        self.browser.get('http://localhost:8000')
        
        self.assertIn('To-Do', self.browser.title)
        self.fail('Finish the test!')
    
    if __name__ = '__main__':                               # 7
        unittest.main(warning='ignore')                     # 8
```

#### 4 

클래스당 하나 이상의 테스트 메소드를 작성할 수 있다.

### 2, 3

setUp과 tearDown은 특수한 메소드로 각 테스트 시작 전과 후에 실행된다. 브라우저를 시작하고 닫을 때 사용 가능. `try/except`와 비슷한 구조로 트스트에 에러가 발생해도 tearDown이 실행(setUp 내 exception이 있는 경우네는 tearDown 실행 안 됨).

### 5

테스트 어설션을 만들기 위해 `self.assertIn`을 사용. `assertEqual`, `assertTrue`, `assertFalse` 등 유용한 함수 제공. [unnittest 문서](http://docs.python.org/3/library/unittest.html) 참고

### 6

`self.fail`은 테스트 실패를 발생시켜 에러 메시지 출력. 테스트가 끝난 것을 알리기 위해서도 사용

### 7

마지막은 `if __name__ == '__main__'` 부분이다(파이썬 스크립트가 다른 스크립트에 임포트된 것이 아니라 커맨드라인을 통해 싫행됐다는 것을 확인하는 코드). `unittest.main()`을 호출해서 unittest 실행자를 가동. 이는 자동으로 파일 내 테스트 클래스와 메소드를 찾아서 실행해주는 역할

### 8

`warnings = 'ignore'`는 테스트 작성 시 발생하는 불필요한 리소스 경고를 제거하기 위한 것.

### `Client()`

```python
from django.test import TestCase, Client #테스트를 위한 클래스 임포트

client = Client() #client는 requests나 httpie가 일하는 방식과 같이 동작하는 객체.
class JustTest(TestCase):
    def test_just_get_view(self):
        response = client.get('/just') 
				#현재 우리는 localhost로서가 아닌 기준 경로로부터 실행 
        self.assertEqual(response.status_code, 200)
				#응답 코드를 비교 
        self.assertEqual(response.json(), {
            "message": "Just Do Python with >Wecode"
        })
				#JSON 데이터를 비교
```

-`Client()` - requests나 http와 비슷한 역할, 이름대로 클라이언트로서 동작
- 테스트 케이스를 만들 때는 항상 `TestCase()` 객체를 상속받아 새로운 테스트 클래스를 생성
- 내부에 작성하는 테스트 함수 명은 언제나 `test_`로 시작
-

### 예제코드

```python

import json

import bcrypt

from .models import Book, Author, AuthorBook, User
from django.test       import TestCase
from django.test       import Client
from unittest.mock     import patch, MagicMock

class AuthorTest(TestCase):

    def setUp(self):
        Author.objects.create(
            name = 'Brendan Eich',
            email = 'BrendanEich@gmail.com'
        )

    def tearDown(self):
        Author.objects.all().delete()

    def test_authorkview_post_success(self):
        client = Client()
        author = {
            'name'  : 'Guido van Rossum',
            'email' : 'GuidovanRossum@gmail.com'
        }
        response = client.post('/book/author', json.dumps(author), content_type='application/json')

        self.assertEqual(response.status_code, 200)

    def test_authorkview_post_duplicated_name(self):
        client = Client()
        author = {
            'name'  : 'Brendan Eich',
            'email' : 'GuidovanRossum@gmail.com'
        }
        response = client.post('/book/author', json.dumps(author), content_type='application/json')

        self.assertEqual(response.status_code, 400)
        self.assertEqual(response.json(),
            {
                'message':'DUPLICATED_NAME'
            }
        )

    def test_authorkview_post_invalid_keys(self):
        client = Client()
        author = {
            'first_name'  : 'Guido van Rossum',
            'email' : 'GuidovanRossum@gmail.com'
        }
        response = client.post('/book/author', json.dumps(author), content_type='application/json')

        self.assertEqual(response.status_code, 400)
        self.assertEqual(response.json(),
            {
                'message':'INVALID_KEYS'
            }
        )

class AuthorBookTest(TestCase):

    def setUp(self):
        client = Client()
        Book.objects.create(
            id = 1,
            title = 'python'
        )

        Book.objects.create(
            id =2,
            title = 'javascript'
        )

        Author.objects.create(
            id = 1,
            name = 'Guido van Rossum',
            email = 'GuidovanRossum@gmail.com'
        )

        Author.objects.create(
            id =2,
            name = 'Brendan Eich',
            email = 'BrendanEich@gmail.com'
        )

        AuthorBook.objects.create(
            book = Book.objects.get(id=1),
            author = Author.objects.get(id=1)
        )

        AuthorBook.objects.create(
            book = Book.objects.get(id=1),
            author = Author.objects.get(id=2)
        )

        AuthorBook.objects.create(
            book = Book.objects.get(id=2),
            author = Author.objects.get(id=1)
        )

        AuthorBook.objects.create(
            book = Book.objects.get(id=2),
            author = Author.objects.get(id=2)
        )

    def tearDown(self):
        Book.objects.all().delete()
        Author.objects.all().delete()
        AuthorBook.objects.all().delete()

    def test_authorbook_get_success(self):
        client = Client()
        response = client.get('/book/author-book/python')
        self.assertEqual(response.json(),
            {
                'authors': [{'author': 1}, {'author': 2}]
            }
        )
        self.assertEqual(response.status_code, 200)  

    def test_authorbook_get_fail(self):
        client = Client()
        response = client.get('/book/author-book/c-language')
        self.assertEqual(response.json(),
            {
                
            }
        )
        self.assertEqual(response.status_code, 400)

    def test_authorbook_get_not_found(self):
        client = Client()
        response = client.get('/book/author-book?book=python')
        self.assertEqual(response.status_code, 400)


class SocialKakaoViewTest(TestCase):
    def setUp(self):
    
        User.objects.create(
            nick_name = 'evergreen',
            email     = 'evergreen@gmail.com',
            password  = bcrypt.hashpw('12345678'.encode('utf-8'), bcrypt.gensalt()).decode('utf-8'),
            kakao = 1234
        )
    
    def tearDown(self):
        User.objects.all().delete()

    @patch('book.views.requests') #book 앱의 views.py에서 사용될 requests를 patch
    def test_kakao_signin(self, mocked_requests):
        client = Client()
        # 실제로 kakao API를 호출하지 않고
        # kakao API의 응답을 Fake하기 위해 MockedResponse 작성
        class MockedResponse:
            def json(self):
                return {
                    'id' : '1234', 
                    'properties' : {'nickname' : 'evergreen'}
                }

        mocked_requests.get = MagicMock(return_value = MockedResponse())

        header = {'HTTP_Authorization' : '1234ABCD'}
        response = client.post('/book/kakao-signin', content_type='applications/json', **header)

        self.assertEqual(response.status_code, 200)

```


## Link

- [Wecode](https://github.com/evergreen-david/django-unit-test)
- [클린 코드를 위한 테스트 주도 개발](http://www.yes24.com/Product/Goods/16886031)
