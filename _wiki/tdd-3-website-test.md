---
layout  : wiki
title   : tdd-3-website-test 
summary : 
date    : 2020-03-29 19:35:40 +0900
updated : 2020-04-05 19:48:14 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

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

- 1. HttpRequest 객체를 생성해서 사용자가 어떤 요청을 브라우저에 보내는지 확인한다.
- 2. 이것을 home_page 뷰에 전달해서 응답을 취득한다. 이 객체는 HttpResponse라는 클래스의 인스턴스이다. 응답 내용(HTML 형태로 사용자에게 보내는 것)이 특정 속성을 가지고 있는지 확인한다.
- 3. 5. 그 다음은 응답 내용이 <html>로 시작하고 </html>로 끝나는지 확인한다. response.content는 byte형 데이터로, 파이썬 문자열이 아니다. 따라서 b'' 구문을 사용해서 비교한다. 
- 4. 반환 내용의 <title> 태그에 "To-Do lists"라는 단어가 있는지 확인한다. 앞선 기능 테스트에서 확인한 것이기 때문에 단위 테스트도 확인해주어야 한다.

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
