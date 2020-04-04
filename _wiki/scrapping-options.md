---
layout  : wiki
title   : 
summary : 
date    : 2020-02-25 19:12:44 +0900
updated : 2020-03-15 18:26:44 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Requests

### 선택자

```python

soup.find_all("p", "title")


# a태그와 b태그 찾기
soup.find_all(["a", "b"])


# 속성 값 가져오기
soup.p['class']
soup.p['id']

# 보기 좋게 출력
soup.b.prettify()

# 간단한 검색
soup.body.b # body 태그 아래의 첫번째 b 태그
soup.a # 첫번째 a 태그

# 속성 값 모두 출력
tag.attrs

# class는 파이썬에서 예약어이므로 class_ 로 쓴다.
soup.find_all("a", class_="sister")

# find
find()
find_next()
find_all()

# find 할 때 확인
if soup.find("div", title=True) is not None:
i = soup.find("div", title=True)

# data-로 시작하는 속성 find
soup.find("div", attrs={"data-value": True})

# 태그명 얻기
soup.find("div").name

# 속성 얻기
soup.find("div")['class'] # 만약 속성 값이 없다면 에러
soup.find("div").get('class') # 속성 값이 없다면 None 반환

# 속성이 있는지 확인
tag.has_attr('class')
tag.has_attr('id')
# 있으면 True, 없으면 False

# 태그 삭제
a_tag.img.unwrap()

# 태그 추가
soup.p.string.wrap(soup.new_tag("b"))
soup.p.wrap(soup.new_tag("div")
``` 



## Selenium

### 선택자 종류

#### Elements

- HTML 페이지 소스 중 첫 번째 요소 선택
- 단일 선택

```python
find_element_by_name('HTML_name')
find_element_by_id('HTML_id')
find_element_by_xpath('/html/body/some/xpath')

find_element_by_link_text
find_element_by_partial_link_text
find_element_by_tag_name
find_element_by_class_name
find_element_by_css_selector
```

- 다중 선택

```python
find_element_by_css_selector('#css > div.selector')
find_element_by_class_name('some_class_name')
find_element_by_tag_name('h1')

find_elements_by_xpath
find_elements_by_link_text
find_elements_by_partial_link_text
find_elements_by_tag_name
find_elements_by_class_name
find_elements_by_css_selector
```

#### find_element By

```python
from selenium.webdriver.common.by import By

driver.find_element(By.XPATH, '//button[text()="Some text"]')
driver.find_element(By.XPATH, '//button')
driver.find_element(By.ID, 'loginForm')
driver.find_element(By.LINK_TEXT, 'Continue')
driver.find_element(By.PARTIAL_LINK_TEXT, 'Conti')
driver.find_element(By.NAME, 'username')
driver.find_element(By.TAG_NAME, 'h1')
driver.find_element(By.CLASS_NAME, 'content')
driver.find_element(By.CSS_SELECTOR, 'p.content')

driver.find_elements(By.ID, 'loginForm')
driver.find_elements(By.CLASS_NAME, 'content')
```

### Headless

```python
from selenium import webdriver

TEST_URL = 'https://intoli.com/blog/making-chrome-headless-undetectable/chrome-headless-test.html'

options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument('window-size=1920x1080')
options.add_argument("disable-gpu")
options.add_argument("user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36")
options.add_argument("proxy-server=localhost:8080")
driver = webdriver.Chrome('chromedriver', chrome_options=options)

driver.get(TEST_URL)
print(driver.page_source)

user_agent = driver.find_element_by_css_selector('#user-agent').text
plugins_length = driver.find_element_by_css_selector('#plugins-length').text
languages = driver.find_element_by_css_selector('#languages').text
webgl_vendor = driver.find_element_by_css_selector('#webgl-vendor').text
webgl_renderer = driver.find_element_by_css_selector('#webgl-renderer').text

print('User-Agent: ', user_agent)
print('Plugin length: ', plugins_length)
print('languages: ', languages)
print('WebGL Vendor: ', webgl_vendor)
print('WebGL Renderer: ', webgl_renderer)
driver.quit()
```

### 중첩조건

```python
driver.find_element_by_xpath('//*[@id="detail1"]/div[2]/table/tbody/tr[2]/td[contains(@class, "active")]')
```

인덱스를 없애면 셀레늄은 기본적으로 내가 입력한 패턴에 해당하는 데이터를 모두 가져온다.
find_element_by_xpath를 사용하면 그중에 처음것 하나만, find_elements_by_xpath를 사용하면 여러개를 리스트로 가져오게된다.

이렇게 긁어온 후 .text를 추가하여 텍스트로 변환시켜보면, 누리끼리한 색의 데이터만 들어오는것을 알 수 있다.
(find_elements_by_xpath를 사용한 경우는 selenium이 리스트 자료형으로 데이터를 가져다 주기 때문에 for loop이나 list comprehension을 사용하여 각 데이터에 .text를 붙여줘야만 변환이 된다.)


## Links

- [Selenium](https://selenium-python.readthedocs.io/api.html)
- [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Tongchun](https://dejavuqa.tistory.com/109)
- [Beomi](https://beomi.github.io/2017/09/28/HowToMakeWebCrawler-Headless-Chrome/)
- [kykevin](https://velog.io/@kykevin/20191126-TIL-Selenium-find-element%EC%97%90-%EC%A1%B0%EA%B1%B4%EA%B1%B8%EA%B8%B0-zpk3fsxmzm)
- [zeroplus1](http://zeroplus1.zc.bz/jh/web/main.php?id=132&category=ETC)
