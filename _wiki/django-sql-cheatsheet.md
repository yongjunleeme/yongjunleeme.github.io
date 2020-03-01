---
layout  : wiki
title   : 
summary : 
date    : 2020-03-01 19:28:07 +0900
updated : 2020-03-01 19:50:39 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Django ORM과 SQL 관계 Cheatsheet(족보/ 컨닝 페이퍼) 

### CREATE TABLE

```SQL
CREATE TABLE Person (
    id int,
    name varchar(50),
    age int NOT NULL,
    gender varchar(10),
);
```

```python
class Person(models.Model):
    name = models.CharField(max_length=50, blank=True)
    age = models.IntegerField()
    gender = models.CharField(max_length=10, blank=True)
```

### Django ORM Field <> SQL Data Type

<img width="408" alt="django-sql" src="https://user-images.githubusercontent.com/48748376/75623934-cd221e80-5bf2-11ea-8f16-b379b1150cd0.png">

### SELECT

#### Select all rows

```SQL
SELECT *
FROM Person;
```

```python
persons = Person.objects.all()
for person in persons:
    print(person.name)
    print(person.gender)
    print(person.age)
```

#### Select 특정 column들

```SQL
SELECT name, age
FROM Person;
```

```python
Person.objects.only('name', 'age')
```

#### Fetch distinct rows

```SQL
SELECT DISTINCT name, age
FROM Person;
```

```python
Person.objects.values('name', 'age').distinct()
```

#### Fetch specific number of rows

```SQL
SELECT *
FROM Person
LIMIT 10;
```

```python
Person.objects.all()[:10]
```

#### LIMIT AND OFFSET keywords

```SQL
SELECT *
FROM Person
OFFSET 5
LIMIT 5;
```

```python
Person.objects.all()[5:10]
```
### WHERE Clause

#### Filter by single column

```SQL
SELECT *
FROM Person
WHERE id = 1;
```

```python
Person.objects.filter(id=1)
```
#### Filter by comparison operators

```SQL
WHERE age > 18;
WHERE age >= 18;
WHERE age < 18;
WHERE age <= 18;
WHERE age != 18;
```

```python
Person.objects.filter(age__gt=18)
Person.objects.filter(age__gte=18)
Person.objects.filter(age__lt=18)
Person.objects.filter(age__lte=18)
Person.objects.exclude(age=18)
```

#### BETWEEN Clause

```SQL
SELECT *
FROM Person 
WHERE age BETWEEN 10 AND 20;
```

```python
Person.objects.filter(age__range=(10, 20))
```

#### LIKE operator

```SQL
WHERE name like '%A%';
WHERE name like binary '%A%';
WHERE name like 'A%';
WHERE name like binary 'A%';
WHERE name like '%A';
WHERE name like binary '%A';
```

```python
Person.objects.filter(name__icontains='A')
Person.objects.filter(name__contains='A')
Person.objects.filter(name__istartswith='A')
Person.objects.filter(name__startswith='A')
Person.objects.filter(name__iendswith='A')
Person.objects.filter(name__endswith='A')
```

#### IN operator

```SQL
WHERE id in (1, 2);
```

```python
Person.objects.filter(id__in=[1, 2])
```

#### AND, OR and NOT Operators

```SQL
WHERE gender='male' AND age > 25;
```

```python
Person.objects.filter(gender='male', age__gt=25)
```

```SQL
WHERE gender='male' OR age > 25;
```

```python
from django.db.models import Q
Person.objects.filter(Q(gender='male') | Q(age__gt=25))
```

```SQL
WHERE NOT gender='male';
```

```python
Person.objects.exclude(gender='male')
```

#### NULL Values

```SQL
WHERE age is NULL;
WHERE age is NOT NULL;
```

```python
Person.objects.filter(age__isnull=True)
Person.objects.filter(age__isnull=False)
```

```python
# Alternate approach
Person.objects.filter(age=None)
Person.objects.exclude(age=None)
```

### ORDER BY Keyword

#### Ascending Order

```SQL
SELECT *
FROM Person
order by age;
```

```python
Person.objects.order_by('age')
```
#### Descending Order

```SQL
SELECT *
FROM Person
ORDER BY age DESC;
```

```python
Person.objects.order_by('-age')
```

#### INSERT INTO Statement

```SQL
INSERT INTO Person
VALUES ('Jack', '23', 'male');
```

```python
Person.objects.create(name='jack', age=23, gender='male)
```

### UPDATE Statement

#### Update single row

```SQL
UPDATE Person
SET age = 20
WHERE id = 1;
```

```python
person = Person.objects.get(id=1)
person.age = 20
person.save()
```

#### Update multiple rows

```SQL
UPDATE Person
SET age = age * 1.5;
```

```python
from django.db.models import F

Person.objects.update(age=F('age')*1.5)
```

### DELETE Statement

#### Delete all rows

```SQL
DELETE FROM Person;
```

```python
Person.objects.all().delete()
```

#### Delete specific rows

```SQL
DELETE FROM Person
WHERE age < 10;
```

```python
Person.objects.filter(age__lt=10).delete()
```

### Aggregation

#### MIN Function

```SQL
SELECT MIN(age)
FROM Person;
```

```shell
>>> from django.db.models import Min
>>> Person.objects.all().aggregate(Min('age'))
{'age__min': 0}
```

#### MAX Function

```SQL
SELECT MAX(age)
FROM Person;
```

```shell
>>> from django.db.models import Max
>>> Person.objects.all().aggregate(Max('age'))
{'age__max': 100}
```

#### AVG Function

```SQL
SELECT AVG(age)
FROM Person;
```

```shell
>>> from django.db.models import Avg
>>> Person.objects.all().aggregate(Avg('age'))
{'age__avg': 50}
```

#### SUM Function

```SQL
SELECT SUM(age)
FROM Person;
```

```shell
>>> from django.db.models import Sum
>>> Person.objects.all().aggregate(Sum('age'))
{'age__sum': 5050}
```

#### COUNT Function

```SQL
SELECT COUNT(*)
FROM Person;
```

```python
Person.objects.count()
```

### GROUP BY Statement

#### Count of Person by gender

```SQL
SELECT gender, COUNT(*) as count
FROM Person
GROUP BY gender;
```

```python
Person.objects.values('gender').annotate(count=Count('gender'))
```

### HAVING Clause

#### Count of Person by gender if number of person is greater than 1

```SQL
SELECT gender, COUNT('gender') as count
FROM Person
GROUP BY gender
HAVING count > 1;
```

```python
Person.objects.annotate(count=Count('gender'))
.values('gender', 'count')
.filter(count__gt=1)
```

### JOINS

#### Consider a foreign key relationship between books and publisher
```python
class Publisher(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
```

#### Fetch publisher name for a book

```SQL
SELECT name
FROM Book
LEFT JOIN Publisher
ON Book.publisher_id = Publisher.id
WHERE Book.id=1;
```

```python
book = Book.objects.select_related('publisher').get(id=1)
book.publisher.name
```

#### Fetch books which have specific publisher

```SQL
SELECT *
FROM Book
WHERE Book.publisher_id = 1;
```

```python
publisher = Publisher.objects.prefetch_related('book_set').get(id=1)
books = publisher.book_set.all()
```

## Link

- [위코드](https://stackoverflow.com/c/wecode/questions/149)
