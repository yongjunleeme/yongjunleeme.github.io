---
layout  : wiki
title   : python-basic
summary : 
date    : 2020-04-07 17:43:42 +0900
updated : 2020-04-14 23:25:41 +0900
tags    : 
toc     : true
public  : true
parent  : study 
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


```txt
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

print()
```

```python
with open('./resource/review.txt', 'r') as f:
    for c in f:   # 프린트 찍어보면 텍스트의 라인 단위로 가져온다.
        # print(c)
        print(c.strip())   # 앞뒤 공백 제거

print()
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
        print(c, end='')
```

#### 평균

```txt
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

#### 파일쓰기

```python
# 예제1 - w -> 생성
with open('./resource/test.txt', 'w') as f:
    f.write('niceman!')

# 예제2 - a -> 기존 파일에 추
with open('./resource/test.txt', 'a') as f:
    f.write('niceman!!')

# 예제3
from random import randint

with open('./resource/score2.txt', 'w') as f:
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

#### 예외 처리

```python

# 문법적으로 에러가 없지만 코드 실행 프로세스(런타임)에서 발생하는 예외 처리 중요

## NameError : 참조 변수 없음

a = 10
b = 15

# print(c)


## ZeroDivisionError : 0 나누기 에러

# print(10 / 0)


## KeyError

dic = {'name': 'Kim', 'Age': 33, 'City': 'Seoul'}

# print(dic['hobby'])     # 키가 존재하지 않으면 예외
# print(dic.get('hobby')) # 안전


# 항상 예외가 발생하지 않을 것으로 가정하고 먼저 코딩
# 그 후 런타임 예외 발생 시 예외처리 권장(EAFP 코딩 스타일)


## AttributeError : 모듈, 클래스에 있는 잘못된 속성 사용시에 예외

# print(time.time())
# print(time.month()) # 예외 발생

x = [1, 2, 3]
# print(x.append(4))
# print(x.add(10))
```

```python
# ValueError : 참조 값이 없을 때 예외

x = [1, 5, 9]

# x.remove(5)
# print(x)

# x.remove(100)
# print(x) # 예외 발생

t = (10, 100, 1000)

print(t.index(100))
# print(t.index(7)) # 예외 발생


# FileNotFoundError

# f = open('test.txt') # 얘외 발생


# TypeError : 자료형에 맞지 않는 연산을 수행 할 경우

x = [1, 2]
y = (1, 2)
z = 'test'

# print(x + y) # 예외 발생
# print(x + z) # 예외 발생
# print(y + z) # 예외 발생
# print(sum([1,2,3],10,1)) # 예외 발생

# print(x + list(y))
# print(x + list(z)


# 예외 처리 기본
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

print()

# 예제2

try:
    z = 'Kim'  # 'Cho' 예외 발생
    x = name.index(z)
    print('{} Found it! {} in name'.format(z, x + 1))
except:  # 모든 에러를 처리(Exception)
    print('Not found it! - Occurred ValueError!')
else:
    print('ok! else!')

print()

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

print()

# 예제4
# 예외처리는 하지 않지만, 무조건 수행 되는 코딩 패턴

try:
    print('try')
finally:
    print('finally')

print()

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


## Link

- [Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Python)
- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
