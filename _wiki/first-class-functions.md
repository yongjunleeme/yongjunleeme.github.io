---
layout  : wiki
title   : first-class-functions
summary : 
date    : 2020-04-27 17:27:53 +0900
updated : 2020-05-06 15:53:54 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

## 파이썬 함수

```python
# 일급 함수(일급 객체)
# 파이썬 함수 특징
# 1.런타임 초기화 가능
# 2.변수 등에 할당 가능
# 3.함수 인수 전달 가능
# 4.함수 결과로 반환 가능

# 함수 객체 예제
def factorial(n):
    '''Factorial Function -> n : int'''
    if n == 1: # n < 2
        return 1
    return n * factorial(n-1)

class A:
    pass

print('EX1-1 -', factorial(5))
print('EX1-2 -', factorial.__doc__)
print('EX1-3 -', type(factorial), type(A))   # 파이썬에서 사용하는 클래스 메소드, 변수 메소드 등은 어트리뷰트(속성)라고 부름
print('EX1-4 -', set(sorted(dir(factorial))) - set(sorted(dir(A)))) # 함수 속성에서 클래스 속성을 뺀 결과 -> 함수만 가지고 있는 속성
print('EX1-5 -', factorial.__name__) # 함수 이름 출력
print('EX1-5 -', factorial.__code__) # 함수에 쓰인 코드, 코드가 로컬 위치 출력
```

```python
# 변수 할당
var_func = factorial # factorial() 이 아님

print('EX2-1 -', var_func)
print('EX2-2 -', var_func(10))
print('EX2-3 -', map(var_func, range(1,11))) # 원형으로 할당(내부에서 실행하므로)
print('EX2-4 -', list(map(var_func, range(1,6)))) # 결괏값 모두 출력
```

### reduce()

```python
# 함수 인수 전달 및 함수로 결과 반환 -> 고위 함수(Higher-order function)
# map, filter, reduce 등

print('EX3-1 -', list(map(var_func, filter(lambda x: x % 2, range(1,6)))))
print('EX3-2 -', [var_func(i) for i in range(1,6) if i % 2])

# reduce()

from functools import reduce # 원래는 빌트인, 많이 안 써서 따로 분류
from operator import add

print('EX3-3 -', reduce(add, range(1,11))) # 함수를 인자로 받는데 누적값이 필요할 경우
print('Ex3-4 -', sum(range(1,11)))
```

### lambda

```python
# 익명함수(lambda)
# 가급적 주석 작성
# 가급적 람다보다 함수 사용
# 일반 함수 형태로 리팩토링 권장

print('EX3-4 -', reduce(lambda x, t: x + t, range(1,11)))
```

### callable

```python
# __call__ 속성이 있으면 ()를 붙여 호출 가능
# Callable : 호출 연산자 -> 메소드 형태로 호출 가능한지 확인
# def, built-in functions, 내장 메소드, class, method...

# 호출 가능 확인
print('EX4-1 -', callable(str), callable(list), callable(var_func), callable(3.14))
```

#### 로또 추첨 클래스

```python
import random

# 로또 추첨 클래스 선언
class LottoGame:
    def __init__(self):
        self._balls = [n for n in range(1, 46)]

    def pick(self):
        random.shuffle(self._balls)
        return sorted([random.choice(self._balls) for n in range(6)])

    def __call__(self): # __call__을 안 쓰면 game()으로 못 쓴다
        return self.pick()


# 객체 생성
game = LottoGame()

# 게임 실행
print('EX4-2 -', game.pick())

# 객체 직접 호출
print('EX4-4 -', callable(game)) # 호출 가능 확인
print('EX4-5 -', game.pick())
print('EX4-6 -', callable(game))
print('EX4-7 -', game())
```

### *args, **kwargs

```python
# 다양한 매개변수 입력(*args, **kwargs)
def agrs_test(name, *contents, point=None, **attrs):
    return '<agrs_test> -> ({}) ({}) ({}) ({})'.format(name, contents, point, attrs)


print('EX5-1 -', agrs_test('test1')) # * -> 튜플, ** -> 딕셔너리로 반환
print('EX5-2 -', agrs_test('test1', 'test2'))
print('EX5-3 -', agrs_test('test1', 'test2', 'test3'))
print('EX5-4 -', agrs_test('test1', 'test2', 'test3', id='admin')) # ** -> 딕셔너리로 받을 때 키값에 따옴표 노노
print('EX5-4 -', agrs_test('test1', 'test2', 'test3', id='admin', point=7))
print('EX5-4 -', agrs_test('test1', 'test2', 'test3', id='admin', password='1234', point=7))
```

### signatures

```python
# 함수 Signatures 
# 함수의 인자 정보를 표시할 클래스 메소드
# 딥러닝에서 인자를 많이 받을 때 사용

from inspect import signature

sg = signature(agrs_test)

print('EX6-1 -', sg) # 파라미터 출력
print('EX6-2 -', sg.parameters) # 파라미터 상세 정보 출력

# 모든 정보 출력
for name, param in sg.parameters.items():
    print('EX6-3 -', name, param.kind, param.default)
```

### partial

```python
# partial 사용법 : 인수 고정 -> 주로 특정 인수 고정 후 콜백 함수에 사용

from operator import mul
from functools import partial

print('EX7-1 -', mul(10,10)) # 2개 인수 곱하기, 함수를 인수로 받을 때 유용

# 인수 고정
five = partial(mul, 5) # 2중 1개를 고정

# 고정 추가
six = partial(five, 6) # 이미 5로 고정된 함수에 6까지 고정

print('EX7-2 -', five(10))
print('EX7-3 -', six())
print('EX7-4 -', [five(i) for i in range(1,11)])
print('EX7-5 -', list(map(five, range(1,11))))
```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
