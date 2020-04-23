---
layout  : wiki
title   : tdd-3-website-test 
summary : 
date    : 2020-03-29 19:35:40 +0900
updated : 2020-04-22 19:48:12 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 2장. unittest 모듈을 이용한 기능 테스트 확장

### 파이썬 기본 라이브러리의 unittest 모듈

```python
from selenium import webdriver
import unittest

class NewVisitorTest(unittest.TestCase): # 1

    def setUp(self): # 2
        self.browser = webdriver.Firefox()

    def tearDown(self): # 3
        self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self): # 4
        self.browser.get('http://localhost:8000')

        self.assertIn('To-Do', self.browser.title) # 5
        self.fail('Finish the test!') # 6

if __name__ == '__main__': # 7
    unittest.main(warnings='ignore') # 8
```

- 1. unitest.TestCase를 상속해서 테스트를 클래스 형태로 만든다.
- 2. test라는 명칭으로 시작하는 모든 메소드는 테스트 메소드이며 테스트 실행자에 의해 실행된다. 클래스당 하나 이상의 테스트 메소드를 작성할 수 있다.
- 3, 4. setUp과 tearDown은 특수한 메소드로 , 각 테스트 시작 전과 후에 실행된다.
- 5. [테스트 어설션 만드는 유용한 함수들](http://docs.python.org/3/library/unittest.html)
- 6. self.fail은 강제적으로 테스트 실패를 발생시켜서 에러 메시지를 출력. 필자는 테스트가 끝났다는 것을 알리기 위해 사용.
- 7. if __name__ = '__main__' - 파이썬 스크립트가 다른 스크립트에 임포트도니 것이 아니라 커맨드라인을 통해 실행됐다는 것을 확인하는 코드. unittest.main()을 호출해서 unittest 테스트 실행자를 가동한다. 이것은 자동으로 파일 내 테스트 클래스와 메소드를 찾아서 실행해주는 역할
- 8. warning = 'ignore'는 테스트 작성 시 발생하는 불필요한 리소스 경고를 제거하기 위한 것.


## 3장. 단위 테스트를 이용한 간단한 홈페이지 테스트

### 단위 테스트와 기능 테스트 차이

기능 테스트는 사용자 관점에서 애플리케이션 외부를 테스트하는 것이고, 단위 테스트는 프로그래머 관점에서 그 내부를 테스트한다는 것이다.

### MVC, URL, 뷰 함수

```python
from django.core.urlresolvers import resolve
from django.test              import TestCase
from lists.views              imoprt home_page

class HomePageTest(TestCase):

    def test_root_url_resolves_to_home_page_view(self):
        found = resolve('/')
        self.assertEqual(found.func, home_page)
```

- `resolve`는 Django가 내부적으로 사용하는 함수로, URL을 해석해서 일치하는 뷰 함수를 찾는다. 여기서는 "/"(사이트 루트)가 호출될 때 resolve를 실행해서 home_page라는 함수를 호출한다.

### 뷰를 위한 단위 테스트

```python
from django.core.urlresolvers import resolve
from django.test import TestCase
from django.http import HttpRequest

from lists.views import home_page

class HomePaseTest(TestCase):
    
    def test_root_url_resolvers_to_home_page_view(self):
        found = resolve('/')
        self.assertEqual(found.func, home_page)
        
    def test_home_page_returns_correct_html(self):
        request = HttpRequest()                                        # 1
        response = home_page(request)                                  # 2
        self.assertTrue(response.content.startswith(b'<html>'))        # 3
        self.assertIn(b'<title>To-Do lists</title>', response.content) # 4
        self.assertTrue(response.content.endswith(b'</html>'))         # 5
```

- 1 HttpRequest 객체를 생성해서 사용자가 어떤 요청을 브라우저에 보내는지 확인한다.
- 2 이것을 home_page 뷰에 전달해서 응답을 취득한다. 이 객체는 HttpResponse라는 클래스의 인스턴스이다. 응답 내용(HTML 형태로 사용자에게 보내는 것)이 특정 속성을 가지고 있는지 확인한다.
- 3, 5 그 다음은 응답 내용이 <html>로 시작하고 </html>로 끝나는지 확인한다. response.content는 byte형 데이터로, 파이썬 문자열이 아니다. 따라서 b'' 구문을 사용해서 비교한다. 
- 4 반환 내용의 title 태그에 "To-Do lists"라는 단어가 있는지 확인한다. 앞선 기능 테스트에서 확인한 것이기 때문에 단위 테스트도 확인해주어야 한다.

#### 단위 테스트 - 코드 주기

1. 터미널에서 단위 테스트 실행
2. 편집기에서 `최소 코드` 수정
3. 반복

### 기능테스트 실행

```python
python functional_tests.py
```

## Link

- [클린 코드를 위한 테스트 주도 개발](http://www.yes24.com/Product/Goods/16886031)
