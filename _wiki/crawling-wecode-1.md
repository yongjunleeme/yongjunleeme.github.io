---
layout  : wiki
title   : crawling-wecode-1
summary : 
date    : 2020-02-11 20:57:20 +0900
updated : 2020-02-11 21:40:04 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

> [위코드](https://wecode.co.kr/) 에서 배운 크롤링 과정의 기록입니다.

## Why

데이터 파이프라인 구축에 필요한 추출 과정에 도움이 될 수도 있기에

## What

정식명칭은 'Web Scraping'이며 단순히 웹에 있는 정보를 Get해오는 것뿐만 아니라 Action을 거친 동적 정보 추출(?)도 가능하다고 한다.
    
연습 시 참고할 웹사이트
- [서울시 공공데이터 포털](data.seoul.go.kr)
- [공공 데이터 포털](data.go.kr)
- [일별 박스 오피스](kobis.or.kr)

도구 목록
- [Scrapy](https://scrapy.org/) - 프레임워크
- [Urllib](https://docs.python.org/ko/3/library/urllib.html) - Requests. Http통신에서 요청을 변수에 담아 다루는 등 URL 작업을 위한 여러 모듈을 모은 패키지
- [Selenium](https://selenium.dev/) - 정적인 통신 이외에 자바스크립트와 같은 동적인 정보도 가져올 수 있는 프레임워크
- [Beautifulsoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) - 파서 역할의 패키지
-

## 개발환경

```shell
$ conda create -n scrap python=3.8		# 가상환경

$ pip install bs4 requests

$ pip install sqlalchemy
```

## billboard 크롤링

```python
import requests
from bs4 import BeautifulSoup

req = requests.get('https://www.billboard.com/charts/hot-100')

html = req.text

soup = BeautifulSoup(html, 'html.parser')

rank = soup.select(
    'li > button > span.chart-element__rank.flex--column.flex--xy-center.flex--no-shrink > span.chart-element__rank__number'
)
```

각각 무슨 뜻인지 프린트를 찍어보면 감이 온다.

```python
import requests
from bs4 import BeautifulSoup

req = requests.get('https://www.billboard.com/charts/hot-100')
print(req)

html = req.text
print(html)

soup = BeautifulSoup(html, 'html.parser')
print(soup)

rank = soup.select(
     'li > button > span.chart-element__rank.flex--column.flex--xy-center.flex--no-shrink > span.chart-element__rank__number'
)
print(rank)
```

- html을 text로 받아 soup에 담는다. 태그를 객체화시키는 과정

```python
import requests
from bs4 import BeautifulSoup

req = requests.get('https://www.billboard.com/charts/hot-100')

html = req.text

soup = BeautifulSoup(html, 'html.parser')


rank = soup.select(
  #   'li > button > span.chart-element__rank.flex--column.flex--xy-center.flex--no-shrink > span.chart-element__rank__number'
'li > button > span.chart-element__rank.flex--column.flex--xy-center.flex--no-shrink > span.chart-element__rank__number'
)

song = soup.select(
   'li > button > span.chart-element__information > span.chart-element__information__song.text--truncate.color--primary'
)
singer = soup.select(
   'li > button > span.chart-element__information > span.chart-element__information__artist.text--truncate.color--secondary'
)

music_chart = []
for item in zip(rank, song, singer):
    music_chart.append(
        {
            'rank'  : item[0].text,
            'song'  : item[1].text,
            'singer': item[2].text,
        }
    )

for i in music_chart:
		print(i)
```

- `zip`은 리스트 길이가 같은 각 변수들을 하나의 딕셔너리에 담아준다.
- `zip_longest`는 길이가 다른 자료형의 변수들을 가장 긴 변수를 기준으로 모아준다.


```python
import requests
from bs4 import BeautifulSoup
from sqlalchemy import *
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship, sessionmaker
from sqlalchemy.sql import *

engine = create_engine('sqlite:///music.db')
Base = declarative_base()

class Music(Base):
    __tablename__ = 'musics'
    id = Column(Integer, primary_key=True)
    rank = Column(String(50))
    song = Column(String(50))
    singer = Column(String(50))

Music.__table__.create(bind=engine, checkfirst=True)

Session = sessionmaker(bind=engine)
session = Session()

req = requests.get('https://www.billboard.com/charts/hot-100')

html = req.text

soup = BeautifulSoup(html, 'html.parser')

rank = soup.select(
    'li > button > span.chart-element__rank.flex--column.flex--xy-center.flex--no-shrink > span.chart-element__rank__number'
)

song = soup.select(
   'li > button > span.chart-element__information > span.chart-element__information__song.text--truncate.color--primary'
)

singer = soup.select(
   'li > button > span.chart-element__information > span.chart-element__information__artist.text--truncate.color--secondary'
)
music_chart = []
for item in zip(rank, song, singer):
    music_chart.append(
        {
            'rank'  : item[0].text,
            'song'  : item[1].text,
            'singer': item[2].text,
        }
    )

for element in music_chart:
    result = Music(rank = element['rank'],
                   song = element['song'],
                   singer = element['singer'],
    )
    session.add(result)
    session.commit()

request = session.query(Music).all()

for row in request:
    print(row.rank,'|', row.song,'|' ,row.singer)
```

- 중요한 점은 프린트를 엄청 찍으면서 soup에 경로를 잘 담는 것...


## 마켓컬리 크롤링 (Open api)

```python
import requests
from bs4 import BeautifulSoup

req = requests.get('https://api.kurly.com/v1/categories/907?page_limit=99&page_no=1&delivery_type=0&sort_type=0&ver=1581403977942')

data = req.json()

products = data['data']['products']

result = []

for product in products:
    result.append({
        'no'   : product['no'],
        'name' : product['name'],
        'price': product['price'],
        'desc' : product['shortdesc']
    })

result = [{
    'no'   : product['no'],
    'name' : product['name'],
    'price': product['price'],
    'desc' : product['shortdesc']
    } for product in products]

for value in result:
    print(value)
```

## Links

[빌보드](https://www.billboard.com/chart)    

[마켓컬리](https://www.kurly.com/shop/goods/goods_list.php?category=029)
