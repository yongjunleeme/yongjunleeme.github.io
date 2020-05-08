---
layout  : wiki
title   : 
summary : 
date    : 2020-05-08 12:01:50 +0900
updated : 2020-05-08 12:51:27 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Closure

### dis

```python
def func_v3(a):
    # global b
    print(a)
    print(b)
    b = 20

# func_v3(5)

from dis import dis

print('EX1-1 -')
print(dis(func_v3))
```

### 결과를 누적하는 방법

#### 클래스

```python
class Averager():
    def __init__(self):
        self._series = []

    def __call__(self, v):
        self._series.append(v)
        print('class >>> {} / {}'.format(self._series, len(self._series)))
        return sum(self._series) / len(self._series)

avg_cls = Averager()

# 누적 확인
print('EX3-1 -', avg_cls(15))
print('EX3-2 -', avg_cls(35))
print('EX3-3 -', avg_cls(40))
```

#### 클로저

```python
def closure_avg1():
    # Free variable
    series = [] # 스냅샷, 값이 누적된다.
    # 클로저 영역
    def averager(v):
        # series = [] # 내부에서 선언하면 누적이 안 된다.
        series.append(v)
        print('def >>> {} / {}'.format(series, len(series)))
        return sum(series) / len(series)
    
    return averager  # 값이 아닌 함수를 리턴

avg_closure1 = closure_avg1()

print('EX4-1 -', avg_closure1(15))
print('EX4-2 -', avg_closure1(35))
print('EX4-3 -', avg_closure1(40))
```

### function inspection

```python
print('EX5-1 -', dir(avg_closure1))
print()
print('EX5-2 -', dir(avg_closure1.__code__))  # dir로 나오는 메소드의 dir도 찍어볼 수 있군
print()
print('EX5-3 -', avg_closure1.__code__.co_freevars)  # 클로저의 자유 영역 변수 확인
print()
print('EX5-4 -', dir(avg_closure1.__closure__[0]))
print()
print('EX5-4 -', avg_closure1.__closure__[0].cell_contents)   # 사용가능한 함수 프린트, 클로저 자체도 함수인 것 확인
```

### 잘못된 클로저 사용 예

```python
def closure_avg2():
    # Free variable
    cnt = 0
    total = 0
    # 클로저 영역
    def averager(v):
        # series = [] # Check
        cnt += 1 # cnt = cnt + 1  # 자유영역의 변수와 함수 내 변수는 다르다
        total += v
        return total / cnt
    
    return averager
    
avg_closure2 = closure_avg2()

# print('EX5-1 -', avg_closure2(15)) # 예외
```

### Nonlocal 사용 -> Free variable 변환

```python
def closure_avg3():
    # Free variable
    cnt = 0
    total = 0
    # 클로저 영역
    def averager(v):
        nonlocal cnt, total
        cnt += 1
        total += v
        return total / cnt
    
    return averager

avg_closure3 = closure_avg3()

print('EX6-1 -', avg_closure3(15))
print('EX6-2 -', avg_closure3(35))
print('EX6-3 -', avg_closure3(40))
```

## Decorator

```python
import time

def perf_clock(func):
    def perf_clocked(*args):
        # 시작 시간 
        st = time.perf_counter() 
        result = func(*args)
        # 종료 시간
        et = time.perf_counter() - st 
        # 함수명
        name = func.__name__
        # 매개변수 
        arg_str = ', '.join(repr(arg) for arg in args)
        # 출력
        print('[%0.5fs] %s(%s) -> %r' % (et, name, arg_str, result)) 
        return result 
    return perf_clocked

@perf_clock
def time_func(seconds):
    time.sleep(seconds)

@perf_clock
def sum_func(*numbers):
    return sum(numbers)

@perf_clock
def fact_func(n):
    return 1 if n < 2 else n * fact_func(n-1)
```

### 데코레이터 미사용

```python
non_deco1 = perf_clock(time_func)
non_deco2 = perf_clock(sum_func)
non_deco3 = perf_clock(fact_func)

print('EX7-1 -', non_deco1, non_deco1.__code__.co_freevars)
print('EX7-2 -', non_deco2, non_deco2.__code__.co_freevars)
print('EX7-3 -', non_deco3, non_deco3.__code__.co_freevars)

print('*' * 40, 'Called Non Deco -> time_func')
print('EX7-4 -')
non_deco1(0.5)
print('*' * 40, 'Called Non Deco -> sum_func')
print('EX7-5 -')
non_deco2(10, 15, 25, 30, 35)
print('*' * 40, 'Called Non Deco -> fact_func')
print('EX7-6 -')
non_deco3(10)
```

### 데코레이터 사용

```python
# @functools.lru_cache() -> 추가 학습 권장

print('*' * 40, 'Called Deco -> time_func')
print('EX8-1 -')
time_func(0.5)
print('*' * 40, 'Called Deco -> sum_func')
print('EX8-2 -')
sum_func(10, 15, 25, 30, 35)
print('*' * 40, 'Called Deco -> fact_func')
print('EX8-3 -')
fact_func(10)
```

## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
