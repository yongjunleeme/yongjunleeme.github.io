---
layout  : wiki
title   : python-basic
summary : 
date    : 2020-04-07 17:43:42 +0900
updated : 2020-04-12 00:16:41 +0900
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

## Link

- [Interview_Question_for_Beginner](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Python)
- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
