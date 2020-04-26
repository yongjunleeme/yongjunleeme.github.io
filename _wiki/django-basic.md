---
layout  : wiki
title   : django-basic 
summary : 
date    : 2020-04-20 19:50:09 +0900
updated : 2020-04-26 17:43:16 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## django-basic

```python
from django.db import models

# Create your models here.

class Fcuser(models.Model):
    username = models.CharField(max_length=32,  # verbose_name -> 어드민에 보이는 제목
                                verbose_name='사용자명')
    useremail = models.EmailField(max_length=128,
                                  verbose_name='사용자이메일')
    password = models.CharField(max_length=64,
                                verbose_name='비밀번호')
    registered_dttm = models.DateTimeField(auto_now_add=True, # -> auto_now_add -> 등록된 시간 기록
                                           verbose_name='등록시간') # dttm -> datetime

    def __str__(self):
        return self.username

    class Meta:
        db_table = 'fastcampus_fcuser'
        verbose_name = '패스트캠퍼스 사용자'
        verbose_name_plural = '패스트캠퍼스 사용자'
```


## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
