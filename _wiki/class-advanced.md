---
layout  : wiki
title   : class-advanced
summary : 
date    : 2020-04-19 16:36:19 +0900
updated : 2020-04-21 16:31:45 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

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

print()
print()

print(studt1.__dict__)
print(studt2.__dict__)

# __str__ 문구 확인
print(studt1)

# Doctring - 주석 메시지 확인
print(Student.__doc__)
print()

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

print()

# 인스턴스 변수
# 직접 접근(PEP 문법적으로 권장X)

print(studt1._name, studt2._name)
print(studt1._email, studt2._email)

print()
print()

# 클래스 변수

# 접근
print(studt1.student_count)
print(studt2.student_count)
print(Student.student_count)

print()
print()


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
print()

# 전체 정보
print(student_1.detail_info())
print(student_2.detail_info())
print()

# 학비 정보(인상 전)
print(student_1.get_fee())
print(student_2.get_fee())
print()

# 학비 인상(클래스 메소드 미사용)
Student.tuition_per = 1.2

# 학비 정보(인상 후)
print(student_1.get_fee_culc())
print(student_2.get_fee_culc())
print()

# 학비 인상(클래스 메소드 사용)
student_1.raise_fee(1.6)
print()

# 학비 정보(인상 후 : 클래스메소드)
print(student_1.get_fee_culc())
print(student_2.get_fee_culc())
print()

# 학생 학비 변경 확인
print(student_1._tuition)
print(student_2._tuition)
print()

# 클래스 메소드 인스턴스 생성 실습

student_3 = Student.student_const(3, 'Park', 'Minji', 'Student3@gmail.com', '3', 550, 4.5)
student_4 = Student.student_const(4, 'Cho', 'Sunghan', 'Student4@naver.com', '4', 600, 4.1)

# 전체 정보
print(student_3.detail_info())
print(student_4.detail_info())
print()

# 학생 학비 변경 확인
print(student_3._tuition)
print(student_4._tuition)
print()


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
print()

# 장학금 혜택 여부(스테이틱 메소드 사용)
print('Static : ', Student.is_scholarship_st(student_1))
print('Static : ', Student.is_scholarship_st(student_2))
print('Static : ', Student.is_scholarship_st(student_3))
print('Static : ', Student.is_scholarship_st(student_4))
print()

print('Static : ', student_1.is_scholarship_st(student_1))
print('Static : ', student_2.is_scholarship_st(student_2))
print('Static : ', student_3.is_scholarship_st(student_3))
print('Static : ', student_4.is_scholarship_st(student_4))
```
