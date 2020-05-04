---
layout  : wiki
title   : 
summary : 
date    : 2020-05-04 10:53:00 +0900
updated : 2020-05-04 13:18:52 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 파이썬 클래스 특별 메소드 및 상속

```python
# Special Method, Class ABC (abstract class)

# class 선언
class VectorP(object):
    # private 선언
    def __init__(self, x, y):
        self.__x = float(x) # __은 직접 접근 불가능, _는 직접 접근 가능
        self.__y = float(y)

    def __iter__(self):
        return (i for i in (self.__x, self.__y)) # Generator

    @property  # Getter
    def x(self):  # __init__에 조건문을 달면 나중에 다시 할당했을 때 무용지물이므로 프로퍼티에 직접 접근 불가능하도록 __x를 쓰고 데코레이터를 사용
        return self.__x  # 호출할 때는 __x가 아닌 x로 호출
    
    @x.setter
    def x(self, v):
        self.__x = v

    @property
    def y(self):
        return self.__y

    @y.setter
    def y(self, v):
        if v < -273:
            raise ValueError("Temperature below -273 is not possible")
        self.__y = v
```

```python
# 객체 선언
v5 = VectorP(20, 40)

# print('EX1-2 -', v5.__x, v5.__y) 

print('EX1-3 -',dir(v5), v5.__dict__)
print('EX1-4 -', v5.x, v5.y) # 타 언어와 달리 근본적인 해결책 X, 파이썬 개발자 간의 암묵적 약속

# Iter 확인
for v in v5:
    print('EX1-5 -', v)
```

### __slot__

```python
# 파이선 인터프리터에게 통보
# 해당 클래스가 가지는 속성을 제한
# __dict__ - 해시 테이블을 사용하므로 메모리 많이 쓴다.
# __dict__ 속성 최적화 -> 다수 객체 생성시 -> 메모리 사용(공간) 대폭 감소
# 머신러닝 등의 대량 데이터를 사용해야 하는 경우 slot을 사용함

# 성능 비교 (클래스 2개 선언)
class TestA(object):
    __slots__ = ('a',)

class TestB(object):
    pass

use_slot = TestA()
no_slot = TestB()

print('EX2-1 -', use_slot)
# print('EX2-2 -', use_slot.__dict__)  # 딕셔너리가 아닌 Set을 사용하므로 에러
print('EX2-3 -', no_slot)
print('EX2-4 -', no_slot.__dict__)

use_slot.a = 10
# use_slot.b = 10
```

#### 메모리 사용량 비교

```python
import timeit

# 측정을 위한 함수 선언
def repeat_outer(obj):
    def repeat_inner():
        obj.a = 'test'
        del obj.a
    return repeat_inner

print('EX3-1 -', min(timeit.repeat(repeat_outer(use_slot), number=1000)))  # 1000번 중 제일 적게 걸린 수
print('EX3-2 -', min(timeit.repeat(repeat_outer(no_slot), number=1000)))
```

### 객체 슬라이싱

```python
class ObjectS:
    def __init__(self):
        self._numbers = [n for n in range(1, 10000, 3)]
    
    def __len__(self):
        return len(self._numbers)

    def __getitem__(self, idx):
        return self._numbers[idx]

s = ObjectS()

print('EX3-1 -', s.__dict__)
print('EX3-2 -', len(s._numbers))
print('EX3-3 -', len(s))
print('EX3-4 -', s[1:10])
print('EX3-5 -', s[-1])
print('EX3-6 -', s[::10])
```

## 파이썬 추상 클래스

```python
# 참고 : https://docs.python.org/3/library/collections.abc.html

from collections.abc import Sequence

# Sequence 상속 받지 않았지만, 자동으로 __iter__, __contains__ 기능 작동
# 객체 전체를 자동으로 조사 -> 시퀀스 프로토콜

class IterTestA():
    def __getitem__(self, idx):
        return range(1, 50, 2)[idx] # range(1, 50, 2)


i1 = IterTestA()

print('EX4-1 -', i1[4])
print('EX4-2 -', i1[4]) # [idx] 제거 후
print('EX4-3 -', 3 in i1[1:10])
# print('EX4-4 -', [i for i in i1[:]])
```

### Sequence 상속 

```python
# 요구사항인 추상메소드 모두 구현해야 동작

class IterTestB(Sequence):
    def __getitem__(self, idx):
        return range(1, 50, 2)[idx] # range(1, 50, 2)

    def __len__(self, idx):
        return len(range(1, 50, 2)[idx])


i2 = IterTestB()

print('EX4-5 -', i2[4])
# print('EX4-6 -', len(i2[:]))
print('EX4-7 -', len(i2[1:6]))
```

### abc 활용 예제

```python
import abc

class RandomMachine(abc.ABC): # metaclass=abc.ABCMeta (3.4 이하 버전)
    # __metaclass__ = abc.ABCMeta
    
    # 추상 메소드
    @abc.abstractmethod
    def load(self, iterobj):
        '''iterable 항목 추가'''
    

    # 추상 메소드
    @abc.abstractmethod
    def pick(self, iterobj):
        '''무작위 항목 뽑기'''
    
    def inspect(self):
        items = []
        while True:
            try:
                items.append(self.pick())
            except LookupError:
                break
        return tuple(sorted(items))
```

```python
import random

class CraneMachine(RandomMachine):
    def __init__(self, items):
        self._randomizer = random.SystemRandom()
        self._items = []
        self.load(items)
    
    def load(self, items):
        self._items.extend(items)
        self._randomizer.shuffle(self._items)
    
    def pick(self):
        try:
            return self._items.pop()
        except IndexError:
            raise LookupError('Empty Crane Box.')
    
    def __call__(self):
        return self.pick()
    
    # 메소드 오버라이드
    # def inspect(self):
    #     print('Override Test')
```

```python
# 서브 클래스 확인
print('EX5-1 -', issubclass(RandomMachine, CraneMachine))
print('EX5-2 -', issubclass(CraneMachine, RandomMachine))
# 상속 구조 확인
print('EX5-3 -', CraneMachine.__mro__)

cm = CraneMachine(range(1,100)) # 추상메소드 구현 안하면 에러

print('EX5-4 -', cm._items)
print('EX5-5- ', cm.pick())
print('EX5-6- ', cm())
print('EX5-7- ', cm.inspect())
```
