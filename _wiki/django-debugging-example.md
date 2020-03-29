---
layout  : wiki
title   : django-debugging-example
summary : 
date    : 2020-03-15 18:14:49 +0900
updated : 2020-03-29 19:23:43 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## db_upload

### DateField에 빈스트링이 안 들어갈 때

> value has an invalid date format. It must be in YYYY-MM-DD format

```python
#artist
print("artist")
def add_artist():
    with open('./upload/artist.csv', mode='r') as artist_lists:
        reader = csv.reader(artist_lists, delimiter=',')
        for artist in list(reader)[1:]:
            debut = artist[2]
            birth = artist[5]
            if artist[2] == "":
                debut = date(1900,1,1)
            if artist[5] == "":
                birth = date(1900,1,1)
            print(artist)
```

## 데이터 타입

### 날짜 long type 변환


```python
from datetime improt datetime

'date' : datetime.fromtimestamp(ranking['regDate'] / 10000)

```

- [Link](https://stackoverflow.com/questions/35473950/resolving-validationerror-u-value-has-an-invalid-date-format-it-must-be-in)


