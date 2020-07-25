---
layout  : wiki
title   : class-advanced
summary : 
date    : 2020-04-19 16:36:19 +0900
updated : 2020-07-25 23:22:20 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

## Real Python - instance class and static methods demystified

```python
class MyClass:
    def method(self):
        return 'instance method called', self

    @classmethod
    def classmethod(cls):
        return 'class method called', cls

    @staticmethod
    def staticmethod():
        return 'static method called'
```

### 인스턴스 메소드

데코레이터가 없는 기본 메소드. 클래스의 인스턴스를 호출할 때 self를 인자로 받는다.  동일 오브젝트의 다른 메소드와 속성에 self를 통해 접근 가능하다. 인스턴스 메소드는 오브젝트의 상태를 바꿀 수 있을 뿐만 아니라 `self.__class__' 속성을 통해 클래스 자체에 접근할 수 있다. 이는 인스턴스 메소드가 클래스의 상태를 바꿀 수 있다는 뜻이다.

### 클래스 메소드

self 파라미터 대신에 클래스를 연결하는 cls 파라미터를 받는다. 오브젝트 인스턴스가 아니다. cls 인자로만 클래스에 접근할 수 있고 오브젝트 인스턴스의 상태를 바꿀 수 없기 때문이다. 그러나 클래스 인스턴스를 통해 클래스의 상태를 변경할 수 있다.

### 스테틱 메소드

스테틱메소드는 오브젝트의 상태와 클래스의 상태 모두 변경할 수 없다. 데이터 접근에 제한적이다.

```python
>>> obj = MyClass()
>>> obj.method()
('instance method called', <MyClass instance at 0x10205d190>)
```

### 예시

#### 인스턴스 메소드

self 인자를 통해 오브젝트 인스턴스에 접근(<Myclass instance>). 메소드가 호출될 때 파이썬이 self 인자를 인스턴스 오브젝트(obj)로 바꾼다.

```python
>>> MyClass.method(obj)
('instance method called', <MyClass instance at 0x10205d190>)
```

첫번째 인스턴스를 생성하지 않고 메소드를 호출할 수 있을까? self.__class__ 속성을 통해서 인스턴스 메소드가 클래스 자체에 접근할 수 있다. 

#### 클래스 메소드

```python
>>> obj.classmethod()
('class method called', <class MyClass at 0x101a2f4c8>)
```

classmethod()를 호출하면 <MyClass instance> 오브젝트가 아닌 <class MyClass> 오브젝트를 보여준다. 이는 클래스 자체를 말한다. (파이썬은 모두 오브젝트다. 심지어 클래스도)

MyClass.classmethod()를 호출할 때 어떻게 파이썬이 자동적으로 클래스의 첫 번째 인자로서 클래스를 전달할까? 인스턴스 메소드의 self 파라미터와 똑같이 작동하는 것이다.

#### 스테틱 메소드

```python
>>> obj.staticmethod()
'static method called'
```

일부 개발자들은 오브젝트 인스턴스에서 스테틱메소드 호출이 가능하다는 사실에 놀란다. 스테틱 메소드는 self나 cls 인자 없이  사용되는데 이는 클래스의 상태와 인스턴스의 상태에 모두 접근할 수 없다는 의미다. 보통 함수처럼 작동할 뿐 클래스의 네임스페이스(그리고 모든 인스턴스)에 속하지 않는다.

```python
>>> MyClass.classmethod()
('class method called', <class MyClass at 0x101a2f4c8>)

>>> MyClass.staticmethod()
'static method called'

>>> MyClass.method()
TypeError: unbound method method() must
    be called with MyClass instance as first
    argument (got nothing instead)
```

#### 인스턴스 메소드에서 self를 무시한다면?

인자 없이 classmethod()와 staticmethod()는 호출이 되지만 인스턴스 메소드인 method()는 TypeError가 나온다. 오브젝트 인스턴스를 만들지 않았고 인스턴스 함수를 호출하지도 않았기 때문인데 이는 파이썬이 self 인자를 채울 수 없다는 뜻이다. 

### Why

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __repr__(self):
        return f'Pizza({self.ingredients!r})'
```

```python
>>> Pizza(['cheese', 'tomatoes'])
Pizza(['cheese', 'tomatoes'])
```

#### 클래스메소드를 쓰는 이유

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __repr__(self):
        return f'Pizza({self.ingredients!r})'

    @classmethod
    def margherita(cls):
        return cls(['mozzarella', 'tomatoes'])

    @classmethod
    def prosciutto(cls):
        return cls(['mozzarella', 'tomatoes', 'ham'])
```

- [Don’t Repeat Yourself (DRY)]([https://en.wikipedia.org/wiki/Don't_repeat_yourself](https://en.wikipedia.org/wiki/Don't_repeat_yourself))을 위한 방법
- 클래스 이름을 변경해도 클래스 메소드를  사용하면 바뀐 Consturtor 이름을 몰라도 된다?

```python
>>> Pizza.margherita()
Pizza(['mozzarella', 'tomatoes'])

>>> Pizza.prosciutto()
Pizza(['mozzarella', 'tomatoes', 'ham'])
```

- 파이썬은 클래스에서 오직 하나의 `__init__`메소드를 허용한다. 클래스 메소드를 사용하면 다른 construcrot를 추가할 수 있다. 

#### 스테틱메소드를 언제 써야 하나

```python
import math

class Pizza:
    def __init__(self, radius, ingredients):
        self.radius = radius
        self.ingredients = ingredients

    def __repr__(self):
        return (f'Pizza({self.radius!r}, '
                f'{self.ingredients!r})')

    def area(self):
        return self.circle_area(self.radius)

    @staticmethod
    def circle_area(r):
        return r ** 2 * math.pi
```

- area() 내에서 곧장 계산하지 않고 circla_area() 스테틱 메소드로 나눠서 사용

```python
>>> p = Pizza(4, ['mozzarella', 'tomatoes'])
>>> p
Pizza(4, ['mozzarella', 'tomatoes'])
>>> p.area()
50.26548245743669
>>> Pizza.circle_area(4)
50.26548245743669
```

- 스테틱메소드는 클래스나 인스턴스의 상태에 접근할 수 없으므로 다른 것들에 대해 독립적일 수 있다. circle_area()는 클래스나 클래스 인스턴스를 수정할 수 없다.
- 이러한 제한은 파이썬 런타임에 의해 강제된 제한인데 자연스럽게 정해진 경계 내에서 개발하도록 유도된다. 설계에 어긋나는 우발적인 변경을 방지할 수 있다.
- 테스트 코드도 쉽게 작성 가능하다. 나머지 클래스에 독립적이므로. 
- 또한 향후 유지보수를 더 쉽게 만든다.




## 클래스

```python
# 클래스 구조
# 구조 설계 후 재사용성 증가, 코드 반복 최소화, 메소드 활용

class Student():
    def __init__(self, name, number, grade, details):
        self._name = name
        self._number = number
        self._grade = grade
        self._details = details

    def __str__(self):
        return 'str : {} - {}'.format(self._name, self._number)

    def __repr__(self):  # str과 같은 기능 
        return 'repr : {} - {}'.format(self._name, self._number)

student1 = Student('Kim', 1, 1, {'gender': 'Male', 'score1': 95, 'score2': 88})
student2 = Student('Lee', 2, 2, {'gender': 'Female', 'score1': 77, 'score2': 92})
student3 = Student('Park', 3, 4, {'gender': 'Male', 'score1': 99, 'score2': 100})

print(student1.__dict__)
print(student2.__dict__)
print(student3.__dict__)
```

### 클래스 변수, 인스턴스 변수

```python
class Student():
    """
    Student Class
    Author : Kim
    Date : 2020.04.21.
    """
    
    # 클래스 변수 - 클래스를 통해서 접근 -> Student.student_count += 1
    student_count = 0
    
    def __init__(self, name, number, grade, details, email=None): # 메소드
        # 인스턴스 변수
        self._name = name  # 속성
        self._number = number
        self._grade = grade
        self._details = details
        self._email = email
        
        Student.student_count += 1
    
    def __str__(self):
        return 'str {}'.format(self._name)
    
    def __repr__(self):
        return 'repr {}'.format(self._name)
    
    def detail_info(self):
        print('Current Id {}'.format(id(self)))
        print('Student Detail Info : {} {} {}'.format(self._name, self._email, self._details))
    
    def __del__(self):
        Student.student_count -= 1
```

- 클래스 속성: 모든 인스턴스가 공유. 인스턴스 전체가 사용해야 하는 값을 저장할 때 사용
- 인스턴스 속성: 인스턴스별로 독립되어 있음. 각 인스턴스가 값을 따로 저장해야 할 때 사용

```python
# Self 의미
studt1 = Student('Cho', 2, 3, {'gender': 'Male', 'score1': 65, 'score2': 44})
studt2 = Student('Chang', 4, 1, {'gender': 'Female', 'score1': 85, 'score2': 74}, 'stu2@naver.com')

# ID 확인
print(id(studt1))
print(id(studt2))

print(studt1._name == studt2._name) # 값을 비교
print(studt1 is studt2) # id를 비교

# dir & __dict__ 확인
print(dir(studt1))
print(dir(studt2))

print(studt1.__dict__)
print(studt2.__dict__)

# __str__ 문구 확인
print(studt1)

# Doctring - 주석 메시지 확인
print(Student.__doc__)

# 실행
studt1.detail_info()
studt2.detail_info()

# 에러
# Student.detail_info()

Student.detail_info(studt1)
Student.detail_info(studt2)

# 비교
print(studt1.__class__, studt2.__class__)  # 부모를 알려준
print(id(studt1.__class__) == id(studt2.__class__))

# 인스턴스 변수
# 직접 접근(PEP 문법적으로 권장X)

print(studt1._name, studt2._name)
print(studt1._email, studt2._email)

# 클래스 변수

# 접근
print(studt1.student_count)
print(studt2.student_count)
print(Student.student_count)

# 공유 확인
print(Student.__dict__)
print(studt1.__dict__)
print(studt2.__dict__)

# 인스턴스 네임스페이스 없으면 상위에서 검색
# 즉, 동일한 이름으로 변수 생성 가능(인스턴스 검색 후 -> 상위(클래스 변수, 부모 클래스 변수))

del studt2

print(studt1.student_count)
print(Student.student_count)

```

### 클래스, 메소드, 인스턴스 메소드, 스테틱 메소드

```python
class Student(object):
    '''
    Student Class
    Author : Me
    Date : 2019.07.05
    Description : Class, Static, Instance Method
    '''

    # Class Variable
    tuition_per = 1.0

    def __init__(self, id, first_name, last_name, email, grade, tuition, gpa):
        self._id = id
        self._first_name =  first_name
        self._last_name = last_name
        self._email = email
        self._grade = grade
        self._tuition = tuition
        self._gpa = gpa

    # Instance Method
    # self : 객체의 고유한 속성 값 사용
    def full_name(self):
        return '{} {}'.format(self._first_name, self._last_name)

    # Instance Method
    def detail_info(self):
        return 'Student Detail Info : {}, {}, {}, {}, {}, {}'.format(self._id, self.full_name(), self._email, self._grade, self._tuition, self._gpa)
    
    # Instance Method
    def get_fee(self):
        return 'Before Tuition -> id : {}, fee : {}'.format(self._id, self._tuition)

    # Instance Method
    def get_fee_culc(self):
        return 'After Tuition -> id : {}, fee : {}'.format(self._id, self._tuition * Student.tuition_per)

    # Class Method
    @classmethod
    def raise_fee(cls, per):  # cls -> 클래스 -> Student
        if per <= 1:
            print('Please Enter 1 or More')
            return
        cls.tuition_per = per
        print('Succeed! tuition increased.')

    # Class Method
    @classmethod
    def student_const(cls, id, first_name, last_name, email, grade, tuition, gpa):
        return cls(id, first_name, last_name, email, grade, tuition * cls.tuition_per, gpa)

    # Static Method
    @staticmethod
    def is_scholarship_st(inst):
        if inst._gpa >= 4.3:
            return '{} is a scholarship recipient.'.format(inst._last_name)
        return 'Sorry. Not a scholarship recipient.'
    
    def __str__(self):
        return 'Student Info -> name: {} grade: {} email: {}'.format(self.full_name(), self._grade, self._email)
```

```python
# 학생 인스턴스    
student_1 = Student(1, 'Kim', 'Sarang', 'Student1@naver.com', '1', 400, 3.5)
student_2 = Student(2, 'Lee', 'Myungho', 'Student2@daum.net', '2', 500, 4.3)

# 기본 정보
print(student_1)
print(student_2)

# 전체 정보
print(student_1.detail_info())
print(student_2.detail_info())

# 학비 정보(인상 전)
print(student_1.get_fee())
print(student_2.get_fee())

# 학비 인상(클래스 메소드 미사용)
Student.tuition_per = 1.2

# 학비 정보(인상 후)
print(student_1.get_fee_culc())
print(student_2.get_fee_culc())

# 학비 인상(클래스 메소드 사용)
student_1.raise_fee(1.6)

# 학비 정보(인상 후 : 클래스메소드)
print(student_1.get_fee_culc())
print(student_2.get_fee_culc())

# 학생 학비 변경 확인
print(student_1._tuition)
print(student_2._tuition)

# 클래스 메소드 인스턴스 생성 실습
student_3 = Student.student_const(3, 'Park', 'Minji', 'Student3@gmail.com', '3', 550, 4.5)
student_4 = Student.student_const(4, 'Cho', 'Sunghan', 'Student4@naver.com', '4', 600, 4.1)

# 전체 정보
print(student_3.detail_info())
print(student_4.detail_info())

# 학생 학비 변경 확인
print(student_3._tuition)
print(student_4._tuition)

# 장학금 혜택 여부(스테이틱 메소드 미사용)
def is_scholarship(inst):
    if inst._gpa >= 4.3:
        return '{} is a scholarship recipient.'.format(inst._last_name)
    return 'Sorry. Not a scholarship recipient.'

# 별도의 메소드 작성 후 호출
print(is_scholarship(student_1))
print(is_scholarship(student_2))
print(is_scholarship(student_3))
print(is_scholarship(student_4))

# 장학금 혜택 여부(스테이틱 메소드 사용)
print('Static : ', Student.is_scholarship_st(student_1))
print('Static : ', Student.is_scholarship_st(student_2))
print('Static : ', Student.is_scholarship_st(student_3))
print('Static : ', Student.is_scholarship_st(student_4))

print('Static : ', student_1.is_scholarship_st(student_1))
print('Static : ', student_2.is_scholarship_st(student_2))
print('Static : ', student_3.is_scholarship_st(student_3))
print('Static : ', student_4.is_scholarship_st(student_4))
```

## Link

- [Python's Instance, Class, and Static Methods Demystified]([https://realpython.com/instance-class-and-static-methods-demystified/](https://realpython.com/instance-class-and-static-methods-demystified/))
- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
- [파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2378)
