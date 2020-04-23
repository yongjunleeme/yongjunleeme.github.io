---
layout  : wiki
title   : 
summary : 
date    : 2020-04-21 16:11:20 +0900
updated : 2020-04-22 15:08:40 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Namedtuple

```python
# 데이터 모델(Data Model)
# 참조 : https://docs.python.org/3/reference/datamodel.html
# Namedtuple 실습
# 파이썬의 중요한 핵심 프레임워크 -> 시퀀스(Sequence), 반복(Iterator), 함수(Functions), 클래스(Class)

# 객체 -> 파이썬의 데이터를 추상화
# 모든 객체 -> id, type -> value
# 파이썬 -> 일관성

# 일반적인 튜플 사용

pt1 = (1.0, 5.0)
pt2 = (2.5, 1.5)

from math import sqrt

line_leng1 = sqrt((pt2[0] - pt1[0]) ** 2 + (pt2[1] - pt1[1]) ** 2)

print('EX1-1 -', line_leng1)

# 네임드 튜플 사용

from collections import namedtuple

# 네임드 튜플 선언
Point = namedtuple('Point', 'x y')

# 두 점 선언
pt1 = Point(1.0, 5.0)
pt2 = Point(2.5, 1.5)

# 계산
line_leng2 = sqrt((pt2.x - pt1.x) ** 2 + (pt2.y - pt1.y) ** 2)

# 출력
print('EX1-2 -', line_leng2)
print('EX1-3 -', line_leng1 == line_leng2)
```

```python
# 네임드 튜플 선언 방법
Point1 = namedtuple('Point', ['x', 'y'])
Point2 = namedtuple('Point', 'x, y')
Point3 = namedtuple('Point', 'x y')
Point4 = namedtuple('Point', 'x y x class', rename=True) # Default=False, 잘못 입력된 인자는 _3, _2 등 파이썬이 다른 변수이름으로 표시

# 출력
print('EX2-1 -', Point1, Point2, Point3, Point4)

# Dict to Unpacking
temp_dict = {'x': 75, 'y': 55}

# 객체 생성
p1 = Point1(x=10, y=35)
p2 = Point2(20, 40)
p3 = Point3(45, y=20)
p4 = Point4(10, 20, 30, 40)
p5 = Point3(**temp_dict) 

# 출력
print('EX2-2 -', p1, p2, p3, p4, p5)

# 사용
print('EX3-1 -', p1[0] + p2[1]) # Index Error 주의
print('EX3-2 -', p1.x + p2.y) # 클래스 변수 접근 방식

# Unpacking
x, y = p3

print('EX3-3 -', x+y)

# Rename 테스트
print('EX3-4 -', p4)
```

### Namedtuple 메소드

```python
# 네임드 튜플 메소드

temp = [52, 38] 

# _make() : 새로운 객체 생성
p4 = Point1._make(temp)

print('EX4-1 -', p4)

# _fields : 필드 네임 확인
print('EX4-2 -', p1._fields, p2._fields, p3._fields)

# _asdict() : OrderedDict 반환
print('EX4-3 -', p1._asdict(), p4._asdict())

# _replace() : 수정된 '새로운' 객체 반환, 튜플은 immutable이니 새로운 객체로 반환(id값이 달라진다)
print('EX4-4 -', p2._replace(y=100))
```

### 연습

```python
# 반20명 , 4개의 반-> (A,B,C,D) 번호

# 네임드 튜플 선언
Classes = namedtuple('Classes', ['rank', 'number'])

# 그룹 리스트 선언
numbers = [str(n) for n in range(1, 21)]
ranks = 'A B C D'.split()

# List Comprehension
students = [Classes(rank, number) for rank in ranks for number in numbers]

print('EX5-1 -', len(students))
print('EX5-2 -', students)

# 출력
for s in students:
    print('EX7-1 -', s)
```

## Masic Method

```python
# Special Method(Magic Method)
# 참조1 : https://docs.python.org/3/reference/datamodel.html#special-method-names
# 참조2 : https://www.tutorialsteacher.com/python/magic-methods-in-python

# 매직메소드 실습
# 파이썬의 중요한 핵심 프레임워크 -> 시퀀스(Sequence), 반복(Iterator), 함수(Functions), 클래스(Class)

# 매직메소드 기초 설명

# 기본형

print(int)

# 모든 속성 및 메소드 출력
print(dir(int))
print()
print()

n = 100

# 사용
print('EX1-1 -', n + 200)
print('EX1-2 -', n.__add__(200))
print('EX1-3 -', n.__doc__)
print('EX1-4 -', n.__bool__(), bool(n))
print('EX1-5 - ', n * 100, n.__mul__(100))

print()
print()

# 클래스 예제1
class Student:
    def __init__(self, name, height):
        self._name = name
        self._height = height

    def __str__(self):
        return 'Student Class Info : {} , {}'.format(self._name, self._height)

    def __ge__(self, x):
        print('Called >> __ge__ Method.')
        if self._height >= x._height:
            return True
        else:
            return False

    def __le__(self, x):
        print('Called >> __le__ Method.')
        if self._height <= x._height:
            return True
        else:
            return False

    def __sub__(self, x):
        print('Called >> __sub__ Method.')
        return self._height - x._height
```

```python
# 인스턴스 생성
s1 = Student('James', 181)
s2 = Student('Mie', 165)

# 매직메소드 출력
print('EX2-1 -', s1 >= s2)
print('EX2-2 -', s1 <= s2)
print('EX2-3 -', s1 - s2)
print('EX2-4 -', s2 - s1)
print('EX2-5 - ', s1)
print('EX2-6 - ', s2)

print()
print()
```

```python
# 클래스 예제2

class Vector(object):
    def __init__(self, *args):
        '''Create a vector, example : v = Vector(1,2)'''
        if len(args) == 0:
            self._x, self._y = 0, 0
        else:
            self._x, self._y = args

    def __repr__(self):
        '''Returns the vector infomations'''
        return 'Vector(%r, %r)' % (self._x, self._y)

    def __add__(self, other):
        '''Returns the vector addition of self and other'''
        return Vector(self._x + other._x, self._y + other._y)
    
    def __mul__(self, y):
        return Vector(self._x * y, self._y * y)

    def __bool__(self):
        return bool(max(self._x, self._y))
```

```python
# Vector 인스턴스 생성
v1 = Vector(3,5)
v2 = Vector(15, 20)
v3 = Vector()

# 매직메소드 출력
print('EX3-1 -', Vector.__init__.__doc__)
print('EX3-2 -', Vector.__repr__.__doc__)
print('EX3-3 -', Vector.__add__.__doc__)
print('EX3-4 -', v1, v2, v3)
print('EX3-5 -', v1 + v2)
print('EX3-6 -', v1 * 4)
print('EX3-7 -', v2 * 10)
print('EX3-8 -', bool(v1), bool(v2))
print('EX3-9 -', bool(v3))

print()
print()



# 참고 : 파이썬 바이트 코드 실행
import dis
dis.dis(v2.__add__)

```
