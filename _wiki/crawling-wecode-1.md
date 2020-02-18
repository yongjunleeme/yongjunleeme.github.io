---
layout  : wiki
title   : crawling-wecode-1
summary : 
date    : 2020-02-11 20:57:20 +0900
updated : 2020-02-17 13:33:11 +0900
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

정식명칭은 'Web Scraping'이며 단순히 웹에 있는 정보를 Get해오는 것뿐만 아니라 Action을 거친 동적 정보 추출(?)까지도 가능하다고 한다.
    

도구 목록
- [Scrapy](https://scrapy.org/) - 크롤링 가능한 프레임워크
- [Urllib](https://docs.python.org/ko/3/library/urllib.html) - 요청을 변수에 담아 다루는 등 URL 작업을 위한 여러 모듈을 모은 패키지
- [Selenium](https://selenium.dev/) - 정적인 통신 이외에 자바스크립트와 같은 동적인 정보도 가져올 수 있는 프레임워크
- [Beautifulsoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) - 파서 역할의 패키지


## 개발환경

```shell
$ conda create -n scrap python=3.8		# 가상환경

$ pip install bs4

$ pip install sqlalchemy

$ pip install selenium
```

## billboard 크롤링 (sql 저장)

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

- 프린트를 찍어 무슨 역할이지 파악

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

req = requests.get('https://api.kurly.com/v1/categories/907?page_limit=99&page_no=1&delivery_type=0&sort_type=0&ver=1581403977942')

data = req.json()

products = data['data']['products']

result = []

result = [{
    'no'   : product['no'],
    'name' : product['name'],
    'price': product['price'],
    'desc' : product['shortdesc']
    } for product in products]

for value in result:
    print(value)
```

## 네이버 실검 크롤링 (Header 접근)

```python
import requests
from bs4 import BeautifulSoup

main_url = 'https://www.naver.com/srchrank?frm=main&ag=all&gr=1&ma=-2&si=0&en=0&sp=0'

request_header = {
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36',
    'accept-language': 'ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7',
}

html = requests.get(main_url, headers=request_header)

print(html)

soup = BeautifulSoup(html.text, 'html.parser')

print(soup)
```

- mail_url - 개발자도구의 네트워크 탭에서 콘트롤 에프를 누르면 서치창이 나온다. 새로고침 버튼을 누른 후  크롤링하고 싶은 텍스트를 검색한다. 검색 되는 요소를 더블클릭하고 나오는 url을 복사해서 붙여넣는다.


> 이 방법은 위코드 동기이신 작두님의 삽질 끝에 알아낸 꿀팁입니다. 때마침 질문을 드려서 나도 알게 되었는데 다시 한 번 감사드립니다.

    
## Festa 크롤링 (json 저장) 
```python
import requets
import json
import os

## pytohn 파일 위치
BASE_DIR = os.path.dirname(os.path.abspath(__file__))

req = requests.get('https://festa.io/api/v1/events?page=1&pageSize=8&order=createdAt&excludeExternalEvents=true')

data = req.json()

dic = data['rows']
result = []

result = [{
	'name'		: dic[i]['name'],
	'startdate' : dic[i]['startDate'],
	'enddate'	: dic[i]['endDate']
} for i in range(0, len(dic))]

## json 파일로 저장
with open(os.path.join(BASE_DIR, 'result.json'), 'w+') as json_file: json.dump(result, json_file)
```

## 인스타 자동로그인 후 이미지 크롤링 (Selenium)

>아래 내용은 [나만의 웹 크롤러 만들기](https://beomi.github.io/gb-crawling/posts/2017-02-27-HowToMakeWebCrawler-With-Selenium.html) 에서 가져왔습니다. 메번 느끼지만 양질의 자료를 무료로 공유해주시는 분들에게는 고개가 숙여집니다. 말끔하게 설명이 잘 되어 있습니다. 직접 들어가서 보시길 추천드립니다.

- 기본 설정

```python
## parser.py
import requests

## HTTP GET Request
req = requests.get('url주소')

## HTML 소스 가져오기
html = req.text

## HTTP Header 가져오기
header = req.headers

## HTTP Status 가져오기 (200: 정상)
status = req.status_code

## HTTP가 정상적으로 되었는지 (True/False)
is_ok = req.ok

```

- 크롬 드라이버
	- [다운로드](https://sites.google.com/a/chromium.org/chromedriver/downloads)
	- 저장 위치 `/Users/유저이름/bin`
	- 코딩 시 경로 설정 안 하려면 `zshrc` 설정 가장 밑에 아래 명령어 추가

```shell
export PATH=${PATH}:~/bin
```

- driver 객체 통해 제공하는 메소드

URL에 접근 api

```python
get('[http://url.com](http://url.com/)')
```

- 페이지의 단일 element에 접근하는 api

```python
find_element_by_name('HTML_name')
find_element_by_id('HTML_id')
find_element_by_xpath('/html/body/some/xpath')
```

- 페이지의 여러 elements에 접근하는 api

```python
find_element_by_css_selector('#css > div.selector')
find_element_by_class_name('some_class_name')
find_element_by_tag_name('h1')
```

위 메소드들을 활용시 HTML을 브라우저에서 파싱해주기 때문에 굳이 Python와 BeautifulSoup을 사용하지 않아도 된다.

하지만 Selenium에 내장된 함수만 사용가능하기 때문에 좀더 사용이 편리한 soup객체를 이용하려면 `driver.page_source` API를 이용해 현재 렌더링 된 페이지의 Elements를 모두 가져올 수 있다.

```python
from selenium import webdriver
import urllib.requets as urllib

## 크롬드라이버 창 안 띄우기
options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument('disable-gpu')

driver = webdriver.Chrome('chromedriver', Chrome_options = options)
driver.get('https://www.instargram.com/accounts/login/')

driver.implicitly_wait(3)

## 자동 로그인
element_id = driver.find_element_by_name("username")

element_password = driver.find_element_by_name("password")

element_id.send_keys("아이디")

element_password.send_keys("비번")

element_password.submit()

## 사진 가져오기
img = driver.find_element_by_class_name('FFVAD')
src = img.get_attribute('src')
urllib.urlretrieve(src)
```

- `submit()`이 안 되면 `click()`을 상황에 따라 사용할 수도 있다. 

## CSV 파일로 저장

```python
import csv
import FinanceDataReader as fdr

df = fdr.DataReader('AAPL', '2019')

df.to_csv('./apple_fdr.csv')
```

- 금융 데이터를 손쉽게 쓸 수 있는 라이브러리

     
- 텔레그램 알림
- 멀티프로세싱 
- headless 방법 추가해야




## Links

[빌보드](https://www.billboard.com/chart)    

[마켓컬리](https://www.kurly.com/shop/goods/goods_list.php?category=029)

[작두님](https://velog.io/@ash3767/%EA%B8%B0%EC%B4%88gmarket-%ED%81%AC%EB%A1%A4%EB%A7%81)

[나만의 웹 크롤러 만들기](https://beomi.github.io/gb-crawling/posts/2017-02-27-HowToMakeWebCrawler-With-Selenium.html)

[고등학생이 직접 개발한 파이썬 프로젝트 사례](https://www.youtube.com/watch?v=8UvakTLkDis&t=1310s)
