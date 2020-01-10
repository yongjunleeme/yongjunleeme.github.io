---
layout  : category
title   : 프로그래밍 언어
summary :
date    : 
updated : 
tag     : python django
toc     : true
public  : true
parent  : index
latex   : false
---
* TOC
{:toc}

> [Moziila djangdo tutorial](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Introduction) 읽으며 정리한 내용입니다. 원본을 보시길 추천드립니다. 코린이라 틀린 내용이 다수 포함될 가능성이 다분합니다. 넓은 아량을 베풀어 틀린 부분 지적해주시면 큰 도움이 되겠습니다.


[모질라 장고 튜토리얼 3: 모델 사용]([https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Models](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Models))

# 설명

## 모델의 정의

- django.db.models.Model의 서브 클래스로 구현
- 필드, 메소드 그리고 메타데이터를 포함할 수 있음

**modols.py**

    ```
    from django.db import models
    
    class MyModelName(models.Model):
        """A typical class defining a model, derived from the Model class."""
    
        # Fields
        my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')
        ...
    
        # Metadata
        class Meta: 
            ordering = ['-my_field_name']
    
        # Methods
        def get_absolute_url(self):
            """Returns the url to access a particular instance of MyModelName."""
            return reverse('model-detail-view', args=[str(self.id)])
        
        def __str__(self):
            """String for representing the MyModelName object (in Admin site etc.)."""
            return self.field_name
    ```

### 필드(Fields)

- 각각의 필드는 우리의 데이터베이스 목록(table)에 저장하길 원하는 데이터 열(column)

```
    my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')
```

- 필드 이름은 쿼리 및 탬플릿에서 이를 참조하는 데 쓰임
- 인수로 지정된 라벨(verbose_name)을 가지고 있거나
- 필드 변수 이름의 첫자를 대문자로 바꾸고 밑줄을 공백으로 바꿔서 기본 라벨을 추정할 수 있음 (예를 들어 my_field_name은 My field name이 기본 라벨)

#### 일반적(common) 필드 인수

- **verbose_name**: 필드 라벨 안에서 사용되는 인간이 읽을 수 있는 필드 이름. 지정되지 않았다면, 장고가 기본 verbose_name을 필드 이름으로부터 유추.
- **default**: 필드를 위한 기본값. 이것은 값 또는 호출 가능한 객체일 수 있다. 객체는 새로운 레코드가 생성될 때 마다 호출됨.
- **primary_key**: 만약 True라면, 현재 필드를 모델의 primary key로 설정(primary key는 모든 다른 테이블 레코드들을 고유하게 확인하도록 지정된 특별한 데이터베이스 열). primary key로 지정된 필드가 없다면 장고가 자동적으로 이 목적의 필드를 추가.

[더 많은 옵션]([https://docs.djangoproject.com/en/3.0/ref/models/fields/](https://docs.djangoproject.com/en/3.0/ref/models/fields/))

#### 일반적인(common) 필드 타입

- **DateField, DateTimeField**: 각각 파이썬 datetime.date 그리고 datetime.datetime 객체. **1 auto_now=True**(모델이 저장될 때 마다 필드를 현재 날짜로 설정하기 위해) 2 auto_now_add(모델이 처음 생성되었을 때만 날짜를 설정하기 위해) 3 default(사용자에 의해 변경될 수 있는 기본 날짜를 설정하기 위해) 매개 변수를 선언 가능
- **AutoField**: 자동적으로 증가하는 IntegerField 의 특별한 타입. 이 타입의 primary key는 명시적으로 지정하지 않는 이상 모델에 자동적으로 추가.
- **ForeignKey**: 다른 데이터베이스 모델과 일-대-다 관계를 지정하기 위해 사용 (예시: 차는 하나의 제조사를 갖고 있지만 제조사는 많은 차들을 만들 수 있다). 일대다에서 "일"쪽이 key를 포함하는 모델.
- **ManyToManyField**: (예시: 책은 여러 장르를 가질 수 있고, 각각의 장르에도 많은 책들이 있다)

[더 많은 옵션]([https://docs.djangoproject.com/en/3.0/ref/models/fields/#field-types](https://docs.djangoproject.com/en/3.0/ref/models/fields/#field-types))

### 메타데이터
```
    class Meta:
        ordering = ['-my_field_name']
```

- 유용한 기능들 중 하나는 모델 타입을 쿼리(query)할 때 반환되는 기본 레코드 순서를 제어하는 것.
- 순서는 필드의 타입에 따라 달라질 것(문자 필드는 알파벳 순서에 따라 정렬될 것이고, 반면 날짜 필드는 날짜순으로 정렬될 것). 위와 같이, 반대로 정렬하고 싶다면 마이너스 기호(-) 사용

```
    verbose_name = 'BetterName'
```

- 다른 일반적인(common) 속성은 verbose_name 이며, 단일 및 복수 형식(form)의 클래스를 위한 자세한(verbose) 이름

[더 많은 옵션]([https://docs.djangoproject.com/en/3.0/ref/models/options/](https://docs.djangoproject.com/en/3.0/ref/models/options/))

### 메소드(Methods)
```
    def __str__(self):
        return self.field_name
```

- object가 사람이 읽을 수 있는 문자열을 반환
```
    def get_absolute_url(self):
        """Returns the url to access a particular instance of the model."""
        return reverse('model-detail-view', args=[str(self.id)])
```

- 관리자 사이트 안의 모델 레코드 수정 화면에 "View on Site" 버튼을 자동적으로 추가
- 위의 reverse() 함수는 알맞은 포맷의 URL을 생성하기 위해서 URL 매퍼를(위 경우에선 'model-detail-view'라고 명명됨) "반전" 시킬 수 있음
- 물론 이것이 작동하기 위해선 URL 매핑, 뷰, 그리고 탬플릿을 작성해야 함

## 모델 관리(management)

- 모델 클래스들을 정의한 이후 클래스들을 사용해서 레코드들을 생성, 업데이트, 또는 삭제할 수 있음
- 레코드 또는 레코드의 특정 하위 집합을 가져오기 위해 쿼리를 실행할 수 있음

### 레코드의 생성과 수정

```
# Create a new record using the model's constructor.
record = MyModelName(my_field_name="Instance #1")

# Save the object into the database.
record.save()
```

- 어떤 필드도 primary_key를 선언하지 않았다면, 새로운 레코드는 자동적으로 id라는 필드 이름을 가진 primary_key가 주어짐
- 위의 레코드를 저장한 후 이 id 필드를 쿼리할 수 있는데, 1의 값을 가짐

```
# Access model field values using Python attributes.
print(record.id) # should return 1 for the first record. 
print(record.my_field_name) # should print 'Instance #1'

# Change record by modifying the fields, then calling save().
record.my_field_name = "New Instance Name"
record.save()
```

- 필드에 점 구문(.)을 사용해서 접근하여 값을 변경할 수 있음
- 수정된 값들을 데이터베이스에 저장하기 위해 save() 를 호출해야 함

### 레코드 검색하기