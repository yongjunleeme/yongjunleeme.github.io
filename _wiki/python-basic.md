---
layout  : wiki
title   : python-basic
summary : 
date    : 2020-04-07 17:43:42 +0900
updated : 2020-05-05 17:15:58 +0900
tags    : 
toc     : true
public  : true
parent  : python 
latex   : false
---
* TOC
{:toc}

## Generator

### 설명

[Generator](https://docs.python.org/3/tutorial/classes.html#generators) 는 함수가 호출될 떄 반환되는 [Iterator](https://docs.python.org/3/tutorial/classes.html#iterators) 의 일종이다. `yield` 구문을 사용해 데이터를 원하는 시점에 반환하고 처리를 다시 시작할 수 있다. 일반적인 함수는 진입점이 하나라면 제너레이터는 여러 개인 셈이다. 때문에 원하는 시점에 원하는 데이터를 받을 수 있다.

### 예제

```python
>>> def generator():
        yield 1
        yield 'string'
        yield True

>>> gen = generator()
>>> gen
<generator object generator at 0x10a47c678>
>>> next(gen)
1
>>> next(gen)
'string'
>>> next(gen)
True
>>> next(gen)
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
StopIteration
```

### 동작

1. yield문이 포함된 제네레이터 함수를 실행하면 제네레이터 객체가 반환되는데 이 떄는 함수의 내용이 실행되지 않는다.
2. `next()`라는 빌트인 메서드를 통해 제네레이터를 실행시킬 수 있으며 `next()` 메서드 내부적으로 iterarotor를 인자로 받아 이터레이터 `__next__()` 메서드를 실행시킨다.
3. 처음 `__next__()` 메서드를 호출하면 함수의 내용을 실행하다 yield문을 만났을 때 처리를 중단한다.
4. 이 때 모든 local state는 유지되는데 변수의 상태, 명령어, 포인터, 내부 스택, 예외 처리 상태를 포함한다.
5. 그 후 제어권을 상위 컨텍스트로 양보하고 또 `__next__()`가 호출되면 제네레이터는 중단된 시점부터 다시 시작한다.

- yield문의 값은 어떤 메서드를 통해 제네레이터가 다시 동작했는지에 따라 다른데 `__next__()`를 사용하면 None이고 `send()`를 사용하면 메서드로 전달된 값을 갖게 돼 외부에서 데이터를 입력받을 수 있게 된다.

### 이점

List, Set, Dict 표현식은 Iterable(이터러블)하기에 for 표현식 등에서 유용하게 쓰인다. 그러나 모든 값을 메모리에 담아 큰 값을 다룰 때는 좋지 않다. 제네레이터를 사용하면 yield를 통해 그때그때 필요한 값만 받아쓰기 떄문에 모든 값을 메모리에 넣을 필요가 없게 된다.

- `range()` 함수는 Python 2.x에서 리스트를 반환하고 Python 3.x에선 range 객체를 반환한다. 이 range 객체는 제네레이터, 이터레이터가 아니다. 실제로 `next(range(1))`를 호출하면 `TypeError: 'range' object is not an iterator` 오류가 발생한다. 그렇지만 내부 구현상 제네레이터를 사용한 것처럼 메모리 사용에 이점이 있다.

```python
>>> import sys
>>> a = [i for i in range(100000)]
>>> sys.getsizeof(a)
824464
>>> b = (i for i in range(100000))
>>> sys.getsizeof(b)
88
```

다만 제네레이터는 그떄그떄 필요한 값을 던져주고 기억은 하지 않기 때문에 `a 리스트`가 여러 번 사용될 수 있는 반면 `b 제네레이터`는 한 번 사용된 후 소진된다. 이는 모든 이터레이터가 마찬가지인데 List, Set은 이터러블하지만 이터레이터는 아니기에 소진되지 않는다.

```python
>>> len(list(b))
100000
>>> len(list(b))
0
```

또한 `While True` 구문으로 제공받을 데이터가 무한하거나 모든 값을 한 번에 계산하기엔 시간이 많이 소요돼 그때그때 필요한 만큼 받아 계산하고 싶을 때 제네레이터를 활용할 수 있다.

### Reference

- [제네레이터 `__next__()` 메서드](https://docs.python.org/3/reference/expressions.html#generator.__next__)
- [제네레이터 `send()` 메서드](https://docs.python.org/3/reference/expressions.html#generator.send)


## lambda

### 장점

- 메모리 절약, 가독성 향상
- 일반 함수는 객체 생성 -> 메모리(리소스) 할당 
- 람다는 즉시 실행(Heap 초기화) -> 메모리 초기화

```python
# 일반적 함수 -> 변수 할당
def mul_10(num):
    return num * 10


mul_func = mul_10

print(mul_func(5))
print(mul_func(6))

# 람다 함수 -> 할당
lambda_mul_func = lambda x: x * 10


def func_final(x, y, func):
    print(x * y * func(10))


func_final(10, 10, lambda_mul_func)
```

## Class

```python
class UserInfo: # 클래스는 객체
    def __init__(self, name):
        self.name = name
        
    def print_info(self):
        print("Name: " + self.name)
    
    def __del__(self):
        print("Instance removed")
```

```python
>>> user1 = UserInfo("Kim") # 인스턴스화 
>>> user2 = UserInfo("Park")

>>> print(id(user1))   # 고윳값 확인
>>> print(id(user2))

>>> user1.print_info()
>>> user2.print_info()

>>> print('user1: ', user1.__dict__)  # 클래스 네임스페이스 확인
>>> print('user2: ', user2.__dict__)

>>> print(user1.name)
```

- 클래스, 인스턴스 차이 - 클래스는 전체 공유, 인스턴스는 각자 네임스페이스, 클래스 네임스페이스는 인스턴스에 값이 없으면 이후 접근
- 클래스 변수 : 직접 사용 가능, 객체보다 먼저 생성
- 인스턴스 변수 : 객체마다 별도 존재, 인스턴스 생성 후 사용

### self 이해 (클래스 메소드, 인스턴스 메소드)

```python
class SelfTest:
    def function1(): # 클래스 메소드 - 여러 인스턴스들이 공유하는 메소드
        print("function1 called!")

    def function2(self): # 인스턴스 메소드
        print(id(self))
        print("function2 called!")
```

```python

>>> self_test = SelfTest()
>>> self_test.function1()  # 오류, self 인자가 없으면 인스턴스화되지 못하기 때문
>>> SelfTest.function1()  # function1 called!
```

### 클래스 변수, 인스턴스 변수

```python
class Warehouse:
    # 클래스 변수
    stock_num = 0

    def __init__(self, name):
        # 인스턴스 변수
        self.name = name
        Warehouse.stock_num += 1  # 클래스 변수 접근

    def __del__(self):
        Warehouse.stock_num -= 1
```

```python
user1 = Warehouse('Kim')
user2 = Warehouse('Park')

print(user1.name)
print(user2.name)
print(user1.__dict__)
print(user2.__dict__)
print(Warehouse.__dict__)  # 클래스 네임스페이스 , 클래스 변수 (공유)

# Warehouse.stock_num = 50 # 직접 접근 가능

print(user1.stock_num) # 자기 네임스페이스에 없으면 클래스에서 찾는다.
print(user2.stock_num)
```

### 상속

#### 상속 기본

```python
class Car:
    """Parent Class"""
    def __init__(self, tp, color):
        self.type = tp
        self.color = color
    
    def show(self):
        # print('Car Class "Show" Method!')
        return 'Car Class Show Method!'

class BmwCar(Car):
    """Sub Class"""
    def __init__(self, car_name, color):
        super().__init__(tp, color)
        self.car_name = car_name
    
    def show_model(self) -> None:
        return 'Your Car Name : %s' % self.car_name
        
class BenzCar(Car):
    """Sub Class"""
    def __init__(self, car_name, tp, color):
        super().__init__(tp, color)
        self.car_name = car_name
    
    def show(self):
        super().show()
        return 'Car Info : %s %s %s' % (self.car_name, self.color, self.type)
    
    def show_model(self) -> None:
        return 'Your Car Name : %s' % self.car_name
```

```python
model1 = BmwCar('520d', 'sedan', 'red')

print(model1.color)  # Super
print(model1.car_name)  # Sub
print(model1.show())  # Super
print(model1.show_model())  # Sub

# Method Overriding
model2 = BenzCar("220d", 'suv', 'black')
print(model2.show())

# Parent Method Call
model3 = BenzCar("350s", 'sedan', 'silver')
print(model3.show())

# Inheritance Info
print('Inheritance Info : ', BmwCar.mro())
print('Inheritance Info : ', BenzCar.mro())

```

#### 다중 상속

```python
class X():
    pass

class Y():
    pass

class Z():
    pass

class A(X, Y):
    pass

class B(Y, Z):
    pass

class M(B, A, Z):
    pass
```

```python
print(M.mro())
print(A.mro())
```

## 모듈

```python
# filename: pkg/fibonacci.py

# 클래스
from pkg.fibonacci import Fibonacci

print("ex1 : ", Fibonacci.fib2(200))
print("ex1 : ", Fibonacci().title)  # 함수 실행해 초기화 후 호출

# 함수
import pkg.calculations as c

print("ex4 : ", c.add(10,10))
print("ex4 : ", c.mul(10,4))

# 함수
from pkg.calculations import div as d

print("ex5 : ", int(d(100,10)))
```

## 파일 읽기, 쓰기

- 읽기 모드 r, 쓰기 모드(기존 파일 삭제) w, 추가 모드(파일 생성 또는 추가) a
- [공식](https://docs.python.org/3.7/library/functions.html#open)
- 상대 경로('../', './'), 절대 경로 확인('C:\...')


```python
## filename: review.txt

The film, projected in the form of animation,
imparts the lesson of how wars can be eluded through reasoning and peaceful dialogues,
which eventually paves the path for gaining a fresh perspective on an age-old problem.
The story also happens to centre around two parallel characters, Shundi King and Hundi King,
who are twins, but they constantly fight over unresolved issues planted in their minds
by external forces from within their very own units.

```

### r

```python
f = open('./resource/review.txt', 'r')
contents = f.read()
print(contents)

# print(dir(f))
# 반드시 close 리소스 반환 - 외부 파일과 커넥션 형성하는 것이므로
f.close()

print()
```

### with

```python
with open('./resource/review.txt', 'r') as f: # with 쓰면 close() 필요 없음
    c = f.read()
    print(iter(c))
    print(list(c))
    print(c)
```

```python
with open('./resource/review.txt', 'r') as f:
    for c in f:   # 프린트 찍어보면 텍스트의 라인 단위로 가져온다.
        # print(c)
        print(c.strip())   # 앞뒤 공백 제거
```

#### readline()

- readline : 한 줄씩 읽기, readline(문자수) : 문자수 읽기

```python
with open('./resource/review.txt', 'r') as f:
    line = f.readline()
    while line:
        print(line, end=' ####')
        line = f.readline()
```

#### readlines()

- readlines : 전체 읽은 후 라인 단위 리스트 저장
- \n 포함해서 리스트로 반환

```python
with open('./resource/review.txt', 'r') as f:
    contents = f.readlines()
    print(contents)
    print()
    for c in contents:
        print(c, end='') # end='' 줄바꿈을 없애줌
```

#### 평균

```python
## filename: score.txt
95
78
92
89
100
66
```

```python
with open('./resource/score.txt', 'r') as f:
    score = []
    for line in f:
        score.append(int(line))
    print(score)
    print('Average : {:6.3f}'.format(sum(score) / len(score)))
    # 6개 숫자 3번째 자리까지?, format()
```

#### 파일쓰기 (만들기)

```python
# 예제1 - w -> 생성 (기존파일 삭제)
with open('./resource/test.txt', 'w') as f:
    f.write('niceman!')

# 예제2 - a -> 기존 파일에 추가
with open('./resource/test.txt', 'a') as f:
    f.write('niceman!!')

# 예제3
from random import randint

with open('./resource/score2.txt', 'w') as f:
    for cnt in range(6): # 0부터 5까지 순환
        f.write(str(randint(50, 100))) # 50~100 사이 random int 생성
        f.write('\n')

# 예제4
# writelines : 리스트 -> 파일로 저장
with open('./resource/test2.txt', 'w') as f:
    list = ['Kim\n', 'Park\n', 'Lee\n']
    f.writelines(list)

# 예제5
with open('./resource/test3.txt', 'w') as f:
    print('Test Contents!', file=f)   # file을 쓰면 print함수 내용이 곧장 파일로 저장
    print('Test Contents!!', file=f)

```

## 예외 처리

### 예외 종류

```python
# 문법적으로 에러가 없지만 코드 실행 프로세스(런타임)에서 발생하는 예외 처리 중요
# 항상 예외가 발생하지 않을 것으로 가정하고 먼저 코딩
# 그 후 런타임 예외 발생 시 예외처리 권장(EAFP 코딩 스타일)

## NameError : 참조 변수 없음
a = 10
b = 15
# print(c)

## ZeroDivisionError : 0 나누기 에러
# print(10 / 0)

## KeyError
dic = {'name': 'Kim', 'Age': 33, 'City': 'Seoul'}
# print(dic['hobby'])     # 키가 존재하지 않으면 예외
# print(dic.get('hobby')) # 딕셔너리 호출할 때는 꼭 get을 쓰자 

## AttributeError : 모듈, 클래스에 있는 잘못된 속성 사용시에 예외
# print(time.time())
# print(time.month()) # 예외 발생
```

```python
# ValueError : 참조 값이 없을 때 예외
x = [1, 5, 9]
# x.remove(100)
# print(x) # 예외 발생

# FileNotFoundError
# f = open('test.txt') # 얘외 발생

# TypeError : 자료형에 맞지 않는 연산을 수행 할 경우
x = [1, 2]
y = (1, 2)
z = 'test'
# print(x + y) # 예외 발생
```

### 예외 처리 예제

```python
# 예외 처리 문법 
# try               에러가 발생 할 가능성이 있는 코드 실행
# except 에러명1:    여러 개 가능(에러 처리)
# except 에러명2: 
# else:             try 블록의 에러가 없을 경우 실행
# finally:          항상 실행

# 예제1
name = ['Kim', 'Lee', 'Park']

try:
    z = 'Kim'  # 'Cho' 예외 발생
    x = name.index(z)
    print('{} Found it! {} in name'.format(z, x + 1))
except ValueError:
    print('Not found it! - Occurred ValueError!')
else:
    print('ok! else!')
```

```python
# 예제2
try:
    z = 'Kim'  # 'Cho' 예외 발생
    x = name.index(z)
    print('{} Found it! {} in name'.format(z, x + 1))
except:  # 모든 에러를 처리(Exception:과 같다)
    print('Not found it! - Occurred ValueError!')
else:
    print('ok! else!')
```

```python
# 예제3
try:
    z = 'Kim'  # 'Cho' 예외 발생
    x = name.index(z)
    print('{} Found it! {} in name'.format(z, x + 1))
except Exception as e:
    print(e)  # 에러 내용 출력
    # pass # 임시로 에러 해결 시 예외 처리
else:
    print('ok! else!')
finally:
    print('ok! finally!')  # 무조건 수행 됨
```

#### except 없이 사용

```python
# 예제4
# 예외처리는 하지 않지만 무조건 수행되는 코딩 패턴
try:
    print('try') # 자주 쓰는 패턴
finally:
    print('finally')
```

#### raise

```python
# 예제5
# 예외 발생 : raise
# raise 키워드로 예외 직접 발생
try:
    a = 'Park'
    if a == 'Kim':
        print('Ok! pass')
    else:
        raise ValueError
except ValueError:
    print('Raise! Occurred ValueError')
except Exception:
    print('Occurred Exception')
else:
    print('ok! else!')

```

## 파일 읽기 쓰기(Excel, CSV)

### csv - 콤마로 구분

```python
# CSV : MIME - text/csv

import csv

# 예제1
with open('./resource/sample1.csv', 'r') as f:
    reader = csv.reader(f)  # import한 csv의 메소드인 csv.reader()
    next(reader) # 첫줄의 필드명(Header) 스킵하는 용도
    # 확인
    print(reader)
    print(type(reader))
    print(dir(reader))  # __iter__ 확인 - 반복문 가능하다는 뜻

    for c in reader: # 1행마다 리스트로 나옴 ['7', '이동철', '2017-03-01']...
        print(c)
```

### csv - |로 구분

```python
# 예제2
with open('./resource/sample2.csv', 'r') as f:
    reader = csv.reader(f, delimiter='|')  # 구분자 선택
```

### csv를 딕셔너리로 변환

```python
# 예제3 (Dict 변환)
with open('./resource/sample1.csv', 'r') as f:  # 값마다 필드명(맨윗줄 컬럼명)이 더해진 튜플로 출력
    reader = csv.DictReader(f)  # OrderDict([('번호', '5'), ('이름', '홍미진')... ])

    for c in reader:
        for k, v in c.items(): # 번호 6 <cr> 이름 김순철...
            print(k, v)
        print('-----')

```

#### 파일쓰기(만들기) - 리스트를 csv로 저장

```python
# 예제4
w = [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12], [13, 14, 15]]  # 1개 리스트가 1개 열로 저장

with open('./resource/sample3.csv', 'w', newline='' ) as f:  # newline='' 은 자동 줄바꿈 방지
    wt = csv.writer(f)
    # dir 확인
    print(dir(wt))
    print(type(wt))
    
    for v in w:  # 조건문을 걸어주면 1열씩 걸러낼 수 있다.
        wt.writerow(v)
```

### 파일쓰기(만들기) - 리스트 1열씩 아니라 전체를 csv로 저장

```python
# 예제5
with open('./resource/sample3.csv', 'w', newline='') as f:
    wt = csv.writer(f)

    wt.writerows(w)  # 1번에 전체를 불러옴:
```

### Excel 불러오기

```python
# Excel 불러올 라이브러리 많고 아래가 그 종류들. 보통 pandas를 주로 사용. pandas 쓰면 numpy도 설치
# openpyxl, xlswriter, xlrd, xlwt, xlutils
# pandas는 내부적으로 openpyxl, xlrd를 씀

# XSL, XLSX : MIME - applications/vnd.excel, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
# pip install pandas 설치 필요
# pip install xlrd   설치 필요
# pip install openpyxl 설치 필요

# openpyxl, xlsxwriter, xlrd, xlwt, xlutils 등 있으나 pandas를 주로 사용(openpyxl, xlrd) 포함

import pandas as pd

xlsx = pd.read_excel('./resource/sample.xlsx')
# 두 번째 인자 ('./resouce/sample.xlsx', 두 번째 인자)
# (엑셀시트명)sheetname='시트명' 또는 숫자
# (몇 번째 행을 헤더로 지정할 것인지)header=3
# (스킵할 행)skiprow=1 실습

print(xlsx.head()) # 상위 5개 데이터 확인
print(xlsx.tail()) # 끝 5개 데이터 확인
print(xlsx.shape)  # 행, 열 구조 확인
```

### 엑셀 or CSV로 내보내기

```python
xlsx.to_excel('./resource/result.xlsx', index=False)
xlsx.to_csv('./resource/result.csv', index=False)
```

## Sqlite

### 연동(SQLite), 테이블 생성

```python
import datetime
import sqlite3

# 삽입 날짜 생성
now = datetime.datetime.now()
print('now', now)

nowDatetime = now.strftime('%Y-%m-%d %H:%M:%S')   # strftime - 스트링포맷타임
print('nowDatetime', nowDatetime)

# sqlite3 버전
print('sqlite3.version : ', sqlite3.version)
print('sqlite3.sqlite_version', sqlite3.sqlite_version)
```

```python
# DB생성 & Autocommit
# 본인 DB 파일 경로
conn = sqlite3.connect('본인이 원하는 경로/database.db', isolation_level=None) # isolation_level=None -> auto commit

# DB생성(메모리)
# conn = sqlite3.connect(":memory:")

# Cursor연결
c = conn.cursor() # 현재 데이터베이스를 가리키는 위치
print('Cursor Type : ', type(c))
```

```python
# 테이블 생성(Datatype : TEXT NUMERIC INTEGER REAL BLOB)
c.execute(
    "CREATE TABLE IF NOT EXISTS users(id INTEGER PRIMARY KEY, username text, email text, phone text, website text, regdate text)")  # AUTOINCREMENT

# 데이터 삽입
c.execute("INSERT INTO users VALUES (1 ,'Kim','Kim@naver.com', '010-0000-0000', 'Kim.com', ?)", (nowDatetime,)) # ?로 변수(nowDatetime)가 할당된다, 튜플로
c.execute("INSERT INTO users(id, username, email, phone, website, regdate) VALUES (?, ?, ?, ?, ?, ?)",
          (2, 'Park', 'Park@naver.com', '010-1111-1111', 'Park.com', nowDatetime)) 

# Many 삽입(튜플, 리스트)
userList = (
    (3, 'Lee', 'Lee@naver.com', '011-2222-2222', 'Lee.com', nowDatetime),
    (4, 'Cho', 'Cho@naver.com', '010-3333-3333', 'Cho.com', nowDatetime),
    (5, 'Yoo', 'Yoo@naver.com', '010-4444-4444', 'Yoo.com', nowDatetime)
)
c.executemany("INSERT INTO users(id, username, email, phone, website, regdate) VALUES (?, ?, ?, ?, ?, ?)", userList)


# 테이블 데이터 삭제
# print("users db deleted : ", conn.execute("delete from users").rowcount, "rows")

# 커밋 : isolation_level=None 일 경우 자동 반영(Auto Commit)
conn.commit()

# 롤백
# conn.rollback()

# 접속 해제
conn.close()
```

### 테이블 조회

```python
import sqlite3

# DB 파일 조회(없으면 새로 생성)
conn = sqlite3.connect('본인이 원하는 경로/database.db')  # 본인 DB 파일 경로

# 커서 바인딩
c = conn.cursor()

# 데이터 조회(전체)
c.execute("SELECT * FROM users")

# 커서 위치가 변경 된다.
# 1개 로우 선택
print('One -> \n', c.fetchone())

# 지정 로우 선택
print('Three -> \n', c.fetchmany(size=3))

# 전체 로우 선택
print('All -> \n', c.fetchall())
```

#### 순회

```python
# 순회1
rows = c.fetchall()
for row in rows:
    print('retrieve1  >', row)  # 조회 없음

# 순회2
for row in c.fetchall():
    print('retrieve2 >', row)  # 조회 없음

# 순회3
for row in c.execute("SELECT * FROM users ORDER BY id desc"):
    print('retrieve3 > ', row)
```

#### 조건절(WHERE)

```python
# WHERE Retrieve1
param1 = (1,)
c.execute('SELECT * FROM users WHERE id=?', param1)
print('param1', c.fetchone())
print('param1', c.fetchall())

# WHERE Retrieve2
param2 = 1
c.execute("SELECT * FROM users WHERE id='%s'" % param2)  # %s %d %f
print('param2', c.fetchone())
print('param2', c.fetchall())

# WHERE Retrieve3
c.execute("SELECT * FROM users WHERE id= :Id", {"Id": 1})
print('param3', c.fetchone())
print('param3', c.fetchall())

# WHERE Retrieve4
param4 = (1, 4)
c.execute('SELECT * FROM users WHERE id IN(?,?)', param4)
print('param4', c.fetchall())

# WHERE Retrieve5
c.execute("SELECT * FROM users WHERE id In('%d','%d')" % (1, 4))
print('param5', c.fetchall())

# WHERE Retrieve6
c.execute("SELECT * FROM users WHERE id= :id1 OR id= :id2", {"id1": 1, "id2": 4})
print('param6', c.fetchall())
```

#### Dump

```python
c.execute("SELECT * FROM users WHERE id= :id1 OR id= :id2", {"id1": 1, "id2": 4})
print('param6', c.fetchall())

with conn:
    # Dump 출력(데이터베이스 백업 시 중요)
    with open('본인이 원하는 경로/dump.sql', 'w') as f:
        for line in conn.iterdump():
            f.write('%s\n' % line)
        print('Dump Print Complete.')
```

### 테이블 수정 및 삭제

```python
import sqlite3
# DB생성(파일)
conn = sqlite3.connect('본인이 원하는 경로/database.db/database.db')

c = conn.cursor()

# 데이터 수정1
c.execute("UPDATE users SET username = ? WHERE id = ?", ('niceman', 1))

# 데이터 수정2
c.execute("UPDATE users SET username = :name WHERE id = :id", {"name": 'niceman', 'id': 3})

# 데이터 수정3
c.execute("UPDATE users SET username = '%s' WHERE id = '%s'" % ('badboy', 5))

# Row Delete1
c.execute("DELETE FROM users WHERE id = ?", (7,))

# Row Delete2
c.execute("DELETE FROM users WHERE id = :id", {'id': 8})

# Row Delete3
c.execute("DELETE FROM users WHERE id = '%s'" % 9)

# 테이블 전체 데이터 삭제
print("users db deleted : ", conn.execute("delete from users").rowcount, "rows")

# 커밋
conn.commit()

# 접속 해제
conn.close()
```

### Typing-game

```python
import random
import time

words = []                                   # 영어 단어 리스트(1000개 로드)

n = 1                                        # 게임 시도 횟수
cor_cnt = 0                                  # 정답 개수

with open('./resource/word.txt', 'r') as f:  # 문제 txt 파일 로드
    for c in f:
        words.append(c.strip())

print(words)                                 

input("Ready? Press Enter Key!")             # input -> 스트링으로 입력받음-> 다른 타입 쓰려면 형변환해야 

start = time.time()                          # Start Time

while n <= 5:                                # 5회 반복
    random.shuffle(words)                    # List shuffle -> 임의로 섞어줌
    q = random.choice(words)                 # Choice -> 1개 뽑기 

	print()

    print("*Question # {}".format(n))
    print(q)                                 # 문제 출력

    x = input()                              # 타이핑 입력

	print()
    
    if str(q).strip() == str(x).strip():     # 입력 확인(공백제거)
        print("Pass!")
        cor_cnt += 1                         # 정답 개수 카운트
    else:
        print("Wrong!")

    n += 1                                   # 다음 문제 전환

end = time.time()                            # End Time
et = end - start                             # 총 게임 시간

et = format(et, ".3f")                       # 소수 셋째 자리 출력(시간)

if cor_cnt >= 3:                             # 3개 이상 합격
    print("결과 : 합격")
else:
    print("불합격")
    
# 수행 시간 출력
print("게임 시간 :", et, "초", "정답 개수 : {}".format(cor_cnt))

# 시작지점
if __name__ == '__main__':
    pass

```

#### 사운드 적용 및 DB 연동

```python
import random
import time
# 사운드 출력 필요 모듈
import winsound
import sqlite3
import datetime

# DB생성 & Autocommit
# 본인 DB 파일 경로
conn = sqlite3.connect('본인이 원하는 경로/records.db', isolation_level=None)

# Cursor연결
cursor = conn.cursor()

# 테이블 생성(Datatype : TEXT NUMERIC INTEGER REAL BLOB)
cursor.execute(
    "CREATE TABLE IF NOT EXISTS records(id INTEGER PRIMARY KEY AUTOINCREMENT,  cor_cnt INTEGER, record text, regdate text)"
)

words = []                                   # 영어 단어 리스트(1000개 로드)

n = 1                                        # 게임 시도 횟수
cor_cnt = 0                                  # 정답 개수

with open('./resource/word.txt', 'r') as f:  # 문제 txt 파일 로드
    for c in f:
        words.append(c.strip())

print(words)                                 # 단어 리스트 확인

input("Ready? Press Enter Key!")             # Enter Game Start!

start = time.time()                          # Start Time

while n <= 5:                                # 5회 반복
    random.shuffle(words)                    # List shuffle!
    q = random.choice(words)                 # List -> words random extract!

	print()

    print("*Question # {}".format(n))
    print(q)                                 # 문제 출력

    x = input()                              # 타이핑 입력

	print()
    
    if str(q).strip() == str(x).strip():     # 입력 확인(공백제거)
        winsound.PlaySound(                  # 정답 소리 재생
            './sound/good.wav',
            winsound.SND_FILENAME
        )
        print("Pass!")
        cor_cnt += 1                         # 정답 개수 카운트

    else:
        winsound.PlaySound(                  # 오답 소리 재생
            './sound/bad.wav',
            winsound.SND_FILENAME
        )

        print("Wrong!")

    n += 1                                   # 다음 문제 전환

end = time.time()                            # End Time
et = end - start                             # 총 게임 시간

et = format(et, ".3f")                       # 소수 셋째 자리 출력(시간)

print()
print('--------------')


if cor_cnt >= 3:                             # 3개 이상 합격
    print("결과 : 합격")
else:
    print("불합격")

# 기록 DB 삽입
cursor.execute(
    "INSERT INTO records('cor_cnt', 'record', 'regdate') VALUES (?, ?, ?)",
    (
        cor_cnt, et, datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'),
    )
)

# 접속 해제
conn.close()

# 수행 시간 출력
print("게임 시간 :", et, "초", "정답 개수 : {}".format(cor_cnt))
```

### 주소록 제작

```python
import os.path
# 파일 저장
import pickle


# 연락처 클래스
class Contact:
    def __init__(self, name, phone_num, email):
        self.name = name
        self.phone_num = phone_num
        self.email = email

    # 개인 연락처 출력
    def prt_info(self):
        print("Name : {}".format(self.name))
        print("Phone_number : {}".format(self.phone_num))
        print("e_mail : {}".format(self.email))
        print("-" * 20)
        print()


# 연락처 정보 입력
def add_cont(c_list):
    name = input_name()
    phone_num = input_phone()
    email = input_email()
    print()

    for v in c_list:
        # 같은 이름은 등록 불가능(프로그램 상에서만)
        if name == v.name:
            print('Name exists.')
            print()
            break
    else:
        # 인스턴스 생성
        cont = Contact(name, phone_num, email)
        print('saved.')
        print()
        # 인스턴스 반환
        c_list.append(cont)


# 메뉴 출력
def prt_menu():
    print("1. Add")
    print("2. Info")
    print("3. Delete")
    print("4. DB Save")
    print("5. DB Drop")
    print("6. Exit")
    print()
    # 메뉴 넘버 입력
    menu = input("Select Menu Number : ")
    print()
    return int(menu)


# 이름으로 조회 된 연락처 삭제
def del_cont(c_list):
    # 삭제 할 이름 입력
    nm = input("Name: ")
    print()

    if len(c_list) > 0:
        # enumerate : 인덱스 생성
        for i, cont in enumerate(c_list):
            if str(cont.name).strip() == nm.strip():
                print('"{}" deleted.'.format(cont.name))
                print()
                del c_list[i]  # 해당 리스트 삭제
                break
        else:
            # 삭제 할 데이터 없을 경우
            print('No files to delete.')
            print()
    else:
        # 연락처 리스트가 비어 있을 경우
        print('No files to delete.')
        print()


# 저장 된 모든 연락처 정보 출력
def prt_cont(c_list):
    # 연락처 리스트가 비어 있지 않은 경우
    if len(c_list) > 0:
        # 리스트 형태로 된 인스턴스 정보 출력
        for i in c_list:
            i.prt_info()
    else:
        # 연락처가 비어 있을 경우
        print('Contact is empty.')
        print()


# 파일로 저장
def store_cont_db(c_list):
    try:
        # 기존 DB 있으면 생성, 없으면 추가(Binary)
        with open("cont_db.bin", "wb") as f:
            # pickle 파일로 저장
            pickle.dump(c_list, f)

            print('db saved')
            print()
    except IOError as log:
        print(log)


# 파일 DB 로드
def load_cont_db():
    # pickle 데이터 저장 변수
    p_list = []
    # 파일이 존재하는지 체크
    if os.path.isfile('cont_db.bin'):
        # 예외처리
        try:
            with open("cont_db.bin", "rb") as f:
                p_list = pickle.load(f)
        except IOError as log:
            print(log)
    else:
        # 처음 실행 시 파일이 존재하지 않으면 출력
        print('DB File not found!')
        print()

    return p_list


# 파일 DB 삭제
def drop_cont_db():
    if os.path.isfile('cont_db.bin'):
        # 예외처리
        try:
            # 파일 삭제
            os.remove('cont_db.bin')
            print('DB File dropped.')
        except FileNotFoundError as log:
            print(log)
    else:
        # 파일이 존재하지 않으면 출력
        print('DB File not found!')
        print()


# 이름 입력
def input_name():
    while True:
        try:
            # input() 함수 : 입력 함수(자료형은 무조건 Str)
            name = input("Name: ")
            if len(name) < 2:
                raise ValueError
            else:
                break
        except ValueError:
            print("Name is too short.")
    return name


# 전화번호 입력
def input_phone():
    while True:
        try:
            # input() 함수 : 입력 함수(자료형은 무조건 Str)
            phone_num = input("Phone number: ")
            if len(phone_num) < 2:
                raise ValueError
            else:
                break
        except ValueError:
            print("Phone number is too short.")
    return phone_num


# 전화번호 입력
def input_email():
    while True:
        try:
            # input() 함수 : 입력 함수(자료형은 무조건 Str)
            phone_email = input("E-mail: ")
            if len(phone_email) < 2:
                raise ValueError
            else:
                break
        except ValueError:
            print("Email is too short.")
    return phone_email


# 프로그램 시작
def main():
    # 연락처 리스트 로드
    c_list = load_cont_db()

    while True:
        # 실행 메뉴 번호 저장
        menu = prt_menu()
        if menu == 1:  # 추가
            add_cont(c_list)
        elif menu == 2:  # 출력
            prt_cont(c_list)
        elif menu == 3:  # 삭제
            del_cont(c_list)
        elif menu == 4:  # DB 저장
            store_cont_db(c_list)
        elif menu == 5:  # DB 삭제
            drop_cont_db()
        else:
            break


if __name__ == "__main__":
    # 프로그램 시작
    main()
```


## Link

- [Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Python)
- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
