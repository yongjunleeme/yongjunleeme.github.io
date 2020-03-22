---
layout  : wiki
title   : django-ORM-3
summary : 
date    : 2020-03-22 19:36:24 +0900
updated : 2020-03-22 23:41:09 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Manager

### Predefined Filters

- django.db.models.manager.Manager

- 남아 있는 원금, 이자를 기준으로 계산
    - 이번달 이 채권에서 이자를 얼마나 내야 해요?
    - 이 채권에서 오늘부터 얼마정도 벌 수 있어요?
    - 이 론에 원금이 얼마나 남아 있는 거예요?

```python
.filter(
    loan=loan,
    status__in=REPAYMENT_STATUS.COMPLETED
)

.filter(
    loan=loan,
).exclude(
    status__in=REPAYMENT_STATUS.COMPLETED    
)
```

- 완료된 것과 완료되지 않은 것을 거르는 필터


```python
class RepaymentManager(models.Manager):

    def completed(self, loan):
        loan=loan,
        status__in=REPAYMENT_STATUS.COMPLETED
    )
    
    def not_completed(self, loan):
        return self.filter(
            loan=loan
        ).exclude(
            status__in=REPAYMENT_STATUS.COMPLETED
        )
```

```python
claass Repayment(models.Model):
    objects = RepaymentManger()
```

- custom manager of a model
- 매니저를 사용해서 함수를 정의

```python
remaining_principal = Repayment.objects.not_completed(
    loan=loan
).aggregate(
    remaing_principal=Coalesce(Sum('principal', 0)
)['remaining_principal']
```

- 채권 정보만 넘겨주면 미리 정의된 쿼리셋이 나온다.
- 쿼리세시므로 닷(.)으로 연결해서 다른 함수를 사용할 수 있다.

## Aggregation & Annotation

### Group By

- 상환을 받을 스케쥴을 상태별로 보여주고 싶다.
- 상환예정된 데이터 상태를 기준

```python
schedules = Schdule.objects.filter(
    user_id = user_id,
    planned_data__gte = start_date,
    planned_date__lt = end_date
)
```

```python
schedules = schedules.values('status').annotate(
    cnt = Count('loan_id', distinct =True),
    sum_principal = AbsoluteSum('principal'),
    sum_interest = Sum('interest'),
    sum_commision = Sum('commission'),
    sum_tax = Sum('tax')
)
```

- 일반적`values`는 필드를 가리키지만 `aggregate`나 `annotate`에 쓰이면 `group by`로 동작


```python
[
    {'cnt': 5, 'sum_principal': 3000000, 'sum_interest': 1234, ...},
    {'cnt': 3, 'sum_principal': 2000000m 'sum_interest', 123, ...}
]

### Conditional Aggregation

- 조건이 들어가는 집계
- 추상화된 상태값이 필요한 상황

```python
custom_status_annotaion = Case(
    When(status__in=(PLANNED, SETTING), then=Value(PLANNED)),
    When(status__in=(DELAYED, OVERDUE,), then=Value(DELAYED)),
    When(status__in=(LONG_OVERDUE,), then=Value(LONG_OVERDUE)),
    When(status__in(SOLD), then=Value(SOLD)),
    default=Value(COMPLETED),
    output_filed=CharField(),
)
```

- 상환예정, 정산중일 때는 상황예정으로

```python
schedules_by_status =schedues.annotate(
    custom_status = custom_status_annotaion
).values(
    'custom_status'
).annotate(
    cnt = Count('loan_id', distinct = True),
    sum_principal = Coalesce(AbsoluteSum('principal'), 0),
    sum_interest = Coalesce(Sum('interest'), 0),
    sum_commission = Coalesce(Sum('commission'), 0),
    sum_tax = Coalesce(Sum('tax'), 0)
).values(
    'custom_status',
    'cnt',
    'sum_principal',
    'sum_interest',
    'sum_commission',
    'sum_tax'
)
```

- `Case`문을 `annotate`에 적용

## Link

- [Django ORM 조금 더 깊게 살펴보기](https://www.slideshare.net/iandmyhand/pycon-kr-2018-effective-tips-for-django-orm-in-practice-110522221)
