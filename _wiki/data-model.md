---
layout  : wiki
title   : 
summary : 
date    : 2020-04-21 16:11:20 +0900
updated : 2020-04-21 16:31:06 +0900
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


