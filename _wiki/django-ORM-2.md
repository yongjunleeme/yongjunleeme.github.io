---
layout  : wiki
title   : django-ORM-2
summary : 
date    : 2020-03-15 11:53:15 +0900
updated : 2020-03-15 18:26:44 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
--
* TOC
{:toc}


### `update_or_create`

> Implementing an idempotent function

- 멱등성을 보장하는 함수
- 주어진 조건의 데이터가 있으면 반환, 없으면 생성해서 반환
- 예) 정산버튼 2번클릭해도 데이터는 1개만 생성 가능

```python
def settle(load_id, sequece, amount):
    settlement, created = \
        Settlement.objects.updated_or_create(
        loan_id = loan_id, sequence = sequence,
        defaults = {'amount' : amount})
    )
    if created:
        return 'Settlement completed!'
    return 'You have already settled this!'
```

### def save()

> Overriding Predefined Model Methods

- 예) 채권 참여자 실시간 카운트(리스트) 오버헤드-> 채권참여자가 아닌 채권 테이블에 `investor_cont`라는 필드를 추가해서 역정규
- `update_fields` - save메서드 쓸 때 모델에 해당하는 모든 값을 저장하므로 선택저긍로 저장할 때 사용

```python
class Investment(models.Model):

    def save(self, *args, **kwargs):
        self.loan.investor_conut += 1
        self.loan.save(
            update_fields = ['investor_count', 'updated_at'])
        super().save(*args, **kwargs)
```

### `model-utils` package

- enum을 장고에서 활용

```python
from model_utils import Choices

class Loan(models.Model):
    GRADES = Choices(
        'A1','A2','A3'
    )
    grade = models.CahrField(choices = GRADES)
```

```python
def use_choices():
    loans = Loan.objects.filter(
        grade = Loan.GRADES.A1
    )
```

- 파이썬 숫자로 변수 못쓰니 아래와 같은 방법 활용

```python
from models_utils import Choices

class Loan(models.Model):
    GRADES = Choices(
        (1, 'CODE1', 1), (2, 'CODE', 2)
    )
    grage = models.CharField(choices = GRADES)
```

```python
def use_choices():
    loans = Loan.objects.filter(
        grade = Loan.GRADES.CODE1
    )
```

### Abstraction model

- 예) 유저 행동의 시간 데이터를 요청받음
- 모델의 메타클래스에 `abstract = True`로 설정 - 실제 데이터베이스에 반영 안 됨

```python 
class TimeStampedModel(models.Model):
    created_datetime = models.DateTimeField(auto_now_add = True)
    updated_datetiem = models.DateTimeField(auto_now = True)
    
    class Meta:
        abstract = True
```

```python
class Loan(TimeStampedModel):
    amount   = models.FloatField()
    interest = models.FloatField()
    
    class Meta:
        abstract = True
```

```python
class SecuredLoan(Loan):
    security = models.ForeignKey(Security)

class UnsecuredLoan(Loan):
    credit_grade = models.IntegerField()
```

- 예) 로우 데이터 요청받음
- 모델에 모두 `verbose_name` 입력
- `filed_name`으로 `verbose_name` 찾는 함수

```python
def get_field_name_by_verbose_name(verbose_name, model):
    for field in model_meta.fields:
        if field.verbose_name == verbose_name:
            return field.attname
        return None
```

### Model to Excel

- `verbose_name`에 해당하는 필드 엑셀로 만들기

```python
verbose_name = ('Amount', 'Interest', 'Credit Grade')
field_names = tuple(map(partial(get_field_names_by_verbose_name, model), verbose_names))
loans = models.objects.filter(field = condition)

with xlsxwriter.Workbook('/tmp/temp.xlsx') as workbook:
    worksheet = workbook.add_worksheet()
    
    def writh_row('args'):
        row_num, loan = args
        
        def get_value(field_name):
            return getattr(loan, field_name)
            
        row = tuple(map(get_value, field_names))
        worksheet.write_row(row_num, 0, row)
        
apply(map(write_row, evumerate(loans,0)))

```

## Link

- [PyCon KR 2018 Effective Tips for Django ORM in Pracrice](https://www.slideshare.net/iandmyhand/pycon-kr-2018-effective-tips-for-django-orm-in-practice-110522221)
