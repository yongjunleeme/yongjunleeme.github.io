---
layout  : wiki
title   : 
summary : 
date    : 2020-04-23 15:38:33 +0900
updated : 2020-04-28 14:42:52 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


## 지능형 리스트(Comprehending Lists)

```python
# 시퀀스형 
# 컨테이너(Container : 서로다른 자료형[list, tuple, collections.deque], Flat : 한 개의 자료형[str,bytes,bytearray,array.array, memoryview])
# 가변(list, bytearray, array.array, memoryview, deque) vs 불변(tuple, str, bytes)
# 리스트 및 튜플 심화

chars = '!@#$%^&*()_+'
codes1 = []

for s in chars:
    # 유니코드 리스트
    codes1.append(ord(s))

# Comprehending Lists
codes2 = [ord(s) for s in chars]

# Comprehending Lists + Map, Filter
# 속도 약간 우세
codes3 = [ord(s) for s in chars if ord(s) > 40]
codes4 = list(filter(lambda x : x > 40, map(ord, chars)))

# 전체 출력
print('EX1-1 -', codes1)
print('EX1-2 -', codes2)
print('EX1-3 -', codes3)
print('EX1-4 -', codes4)
print('EX1-5 -', [chr(s) for s in codes1])
print('EX1-6 -', [chr(s) for s in codes2])
print('EX1-7 -', [chr(s) for s in codes3])
print('EX1-8 -', [chr(s) for s in codes4])
```

## 리스트 주의할 점

```python
marks1 = [['~'] * 3 for n in range(3)]
marks2 = [['~'] * 3] * 3

print('EX4-1 -', marks1)
print('EX4-2 -', marks2)

print()

# 수정
marks1[0][1] = 'X'
marks2[0][1] = 'X'

print('EX4-3 -', marks1)
print('EX4-4 -', marks2)

# 증명
print('EX4-5 -', [id(i) for i in marks1])
print('EX4-6 -', [id(i) for i in marks2])
```

## Generator

```python
import array

# Generator : 한 번에 한 개의 항목을 생성(메모리 유지X)
tuple_g = (ord(s) for s in chars)
# Array
array_g = array.array('I',  (ord(s) for s in chars))

print('EX2-1 -', type(tuple_g))
print('EX2-2 -', next(tuple_g))
print('EX2-3 -', type(array_g))
print('EX2-4 -', array_g.tolist())

print()
print()

# 제네레이터 예제
print('EX3-1 -', ('%s' % c + str(n) for c in ['A', 'B', 'C', 'D'] for n in range(1,11)))

for s in ('%s' % c + str(n) for c in ['A', 'B', 'C', 'D'] for n in range(1,11)):
    print('EX3-2 -', s)
```

## Tuple Advanced

### Unpacking

```python

a, b = divmod(100, 9) # 값이 2개(몫, 나머지)인데 a, b에 각각 할당

print('EX5-1 -', divmod(100, 9)) 
print('EX5-2 -', divmod(*(100, 9))) # 튜플(100, 9)는 인자가 1개인데 divmod는 2개 인자 필요하므 언패킹(*) 사용
print('EX5-3 -', *(divmod(100, 9)))

print()

x, y, *rest = range(10)
print('EX5-4 -', x, y, rest)
x, y, *rest = range(2)
print('EX5-5 -', x, y, rest)
x, y, *rest = 1, 2, 3, 4, 5
print('EX5-6 -', x, y, rest)
```

### Mutable(가변) vs Immutable(불변)

```python
l = (10, 15, 20)
m = [10, 15, 20]

print('EX6-1 -', l, id(l))
print('EX6-2 -', m, id(m))

l = l * 2
m = m * 2

print('EX6-3 -', l, id(l))
print('EX6-4 -', m, id(m))

l *= 2
m *= 2  # 재할당?을 해서 id 값이 바뀌지 않음

print('EX6-5 -', l, id(l))
print('EX6-6 -', m, id(m))
```

### sort vs sorted 

```python
# reverse, key=len, key=str.lower, key=func..

# sorted : 정렬 후 새로운 객체 반환
f_list = ['orange', 'apple', 'mango', 'papaya', 'lemon', 'strawberry', 'coconut']

print('EX7-1 -', sorted(f_list))
print('EX7-2 -', sorted(f_list, reverse=True))
print('EX7-3 -', sorted(f_list, key=len))
print('EX7-4 -', sorted(f_list, key=lambda x: x[-1])) # 함수로 키값 사용
print('EX7-5 -', sorted(f_list, key=lambda x: x[-1], reverse=True))
print()

print('EX7-6 -', f_list)

print()

# sort : 정렬 후 객체 직접 변경

# 반환 값 확인(None)
print('EX7-7 -', f_list.sort(), f_list)
print('EX7-8 -', f_list.sort(reverse=True), f_list)
print('EX7-9 -', f_list.sort(key=len), f_list)
print('EX7-10 -', f_list.sort(key=lambda x: x[-1]), f_list)
print('EX7-11 -', f_list.sort(key=lambda x: x[-1], reverse=True), f_list)

# List vs Array 적합 한 사용법 설명
# 리스트 기반 : 융통성, 다양한 자료형, 범용적 사용
# 숫자 기반 : 배열(리스트의 거의 모든 연산 지원)
```

## Dict Advanced

### Dict 구조

```python
# 시퀀스형
# 해시테이블(hashtable) -> 적은 리소스로 많은 데이터를 효율적으로 관리
# Dict -> Key 중복 허용 X, Set -> 중복 허용 X
# Dict 및 Set 심화

# Dict 구조
print('EX1-1 -')
# print(__builtins__.__dict__)

# Hash 값 확인
t1 = (10, 20, (30, 40, 50))
t2 = (10, 20, [30, 40, 50]) # 리스트는 가변형이라 해시값이 없음

print('EX1-2 -', hash(t1))
# print('EX1-3 -', hash(t2))

```

### 지능형 딕셔너리(Comprehending Dict)

```python
import csv

# 외부 CSV TO List of tuple

with open('./resources/test1.csv', 'r', encoding='UTF-8') as f:
    temp = csv.reader(f)
    # Header Skip
    next(temp)
    # 변환
    NA_CODES = [tuple(x) for x in temp]

print('EX2-1 -',)
print(NA_CODES)

n_code1 = {country: code for country, code in NA_CODES}
n_code2 = {country.upper(): code for country, code in NA_CODES}

print('EX2-2 -',)
print(n_code1)

print('EX2-3 -',)
print(n_code2)
```

### Dict Setdefault

```python
source = (('k1', 'val1'),
            ('k1', 'val2'),
            ('k2', 'val3'),
            ('k2', 'val4'),
            ('k2', 'val5'))

new_dict1 = {}
new_dict2 = {}

# No use setdefault
for k, v in source:
    if k in new_dict1:
        new_dict1[k].append(v)
    else:
        new_dict1[k] = [v]

print('EX3-1 -', new_dict1)
```

```python
# Use setdefault
for k, v in source:
    new_dict2.setdefault(k, []).append(v)

print('EX3-2 -', new_dict2)
```

### 사용자 정의 dict 상속(UserDict 가능)

```python
class UserDict(dict):
    def __missing__(self, key):
        print('Called : __missing__')
        if isinstance(key, str):
            raise KeyError(key)
        return self[str(key)]

    def get(self, key, default=None):
        print('Called : __getitem__')
        try:
            return self[key]
        except KeyError:
            return default
    
    def __contains__(self, key):
        print('Called : __contains__')
        return key in self.keys() or str(key) in self.keys()

user_dict1 = UserDict(one=1, two=2)
user_dict2 = UserDict({'one': 1, 'two': 2})
user_dict3 = UserDict([('one',1),('two',2)])

# 출력
print('EX4-1 -', user_dict1, user_dict2, user_dict3)
print('EX4-2 -', user_dict2.get('two'))
print('EX4-3 -', 'one' in user_dict3)
# print('EX4-4 -', user_dict3['three'])
print('EX4-5 -', user_dict3.get('three'))
print('EX4-6 -', 'three' in user_dict3)
```

### immutable Dict

```python
from types import MappingProxyType

d = {'key1': 'TEST1'}

# Read Only
d_frozen = MappingProxyType(d)

print('EX5-1 -', d, id(d))
print('EX5-2 -', d_frozen, id(d_frozen))
print('EX5-3 -', d is d_frozen, d == d_frozen)

# 수정 불가
# d_frozen['key2'] = 'TEST2'

d['key2'] = 'TEST2'

print('EX5-4 -', d)

s1 = {'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'}
s2 = set(['Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'])
s3 = {3}
s4 = set() # Not {}
s5 = frozenset({'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'})

# 추가
s1.add('Melon')

# 추가 불가
# s5.add('Melon')

print('EX6-1 -', s1, type(s1))
print('EX6-2 -', s2, type(s2))
print('EX6-3 -', s3, type(s3))
print('EX6-4 -', s4, type(s4))
print('EX6-5 -', s5, type(s5))

# 선언 최적화

from dis import dis # 파이썬 인터프리터 처리과정 프린트

print('EX6-5 -')
print(dis('{10}'))

print('EX6-6 -')
print(dis('set([10])')) # 배열을 넣으면 그냥 셋으로 선언하는 것에 비해 리스트를 빌드하고 함수를 호출하는 부가적인 과정이 추가된다.
```

### 지능형 집합(Comprehending Set)

```python
from unicodedata import name

print('EX7-1 -')

print({name(chr(i), '') for i in range(0,256)}) # 유니코드와 유니코드 설명
```
