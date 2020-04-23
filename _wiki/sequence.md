---
layout  : wiki
title   : 
summary : 
date    : 2020-04-23 15:38:33 +0900
updated : 2020-04-23 15:44:01 +0900
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

## Tuple Advanced

### Unpacking

```python
print('EX5-1 -', divmod(100, 9))
print('EX5-2 -', divmod(*(100, 9)))
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
m *= 2

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
print('EX7-4 -', sorted(f_list, key=lambda x: x[-1]))
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

