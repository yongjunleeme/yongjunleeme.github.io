---
layout  : wiki
title   : 
summary : 
date    : 2020-05-13 22:04:38 +0900
updated : 2020-05-13 22:13:39 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## aws cli

```python
$ pip install awscli

"aws 홈페이 내 IAM → 사용자 → 사용자 추가
"프로그래밍 방식 액세스 체크 ( CLI 허용여부)
"정책 → AdministratorAccess 체크

$ aws configure

"홈페이지에 나온 ID와 액세스 키 입력
"데이터 리전 입력 - ap-norhest-2
"output format -> 엔터 (None)

$ aws —-help
```

### deploy.sh

```python
"command > file  → 기존 지우고 저장
"예) $ python example.py > test.txt
"command >> file  → 기존 안 지우고 이어서 저장

$ vim run.sh  
"자동 실행될 커맨드 명령어 작성

$ chmod +x run.sh  
"권한 설정

$ ./run.sh
"실행
```

## RDS

### command

```python
$ mysql --help

"RDS 접속
$ mysql -h fastcampus.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -P 3306 -u yongjunlee -p

"DATABASE 생성
mysql> CREAE DATABASE production;

"DATABASE 연결
$ mysql -h fastcampus.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -P 3306 -D production -u yongjunlee -p

"TABLE 생성
mysql> CREATE TABLE people (first_name VARCHAR(20), last_name VARCHAR(20), age INT);
```

### ERD

Entities: 하나의 개체 (사람)
Attributes: 엔터티의 속성 (성, 이름)

<img width="666" alt="44" src="https://user-images.githubusercontent.com/48748376/81815785-652e7380-9565-11ea-9666-5ff9715effe8.png">

- Unique key, pk 차이

<img width="1319" alt="11" src="https://user-images.githubusercontent.com/48748376/81815764-5f389280-9565-11ea-8511-3cd434f39ca4.png">

<img width="510" alt="22" src="https://user-images.githubusercontent.com/48748376/81815778-63fd4680-9565-11ea-9e99-47224b962db4.png">

- ERD 예시

<img width="839" alt="33티켓마스터" src="https://user-images.githubusercontent.com/48748376/81815781-6495dd00-9565-11ea-8c1b-4ae9152cae3d.png">

- 티켓마스터 ERD 예시

#### Spotify ERD

<img width="1219" alt="55" src="https://user-images.githubusercontent.com/48748376/81815786-65c70a00-9565-11ea-846f-e21d65a60f3f.png">

### code

```python
$ pip install pymysql --user
```

```python
import sys
import requests
import base64
import json
import logging
import pymysql

client_id = "74cbd487458843f1ad3f5fa1e914c02f"
client_secret = "752e4ed11062473f9da9076c4499d51b"

host = "fastcampus.cxxbo4jh5ykm.ap-northeast-2.rds.amazonaws.com"
port = 3306
username = "sean"
database = "production"
password = "fastcampus"

def main():
    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1) # 1은 실패 0은 성공

    cursor.execute("SHOW TABLES")
    print(cursor.fetchall())

    print("success")
    sys.exit(0)

    headers = get_headers(client_id, client_secret)

    ## Spotify Search API
    params = {
        "q": "BTS",
        "type": "artist",
        "limit": "5"
    }

    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    try:
        r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    except:
        logging.error(r.text)
        sys.exit(1)

    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    if r.status_code != 200:
        logging.error(r.text)

        if r.status_code == 429:

            retry_after = json.loads(r.headers)['Retry-After']
            time.sleep(int(retry_after))

            r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)

        ## access_token expired
        elif r.status_code == 401:

            headers = get_headers(client_id, client_secret)
            r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)

        else:
            sys.exit(1)


    # Get BTS' Albums

    r = requests.get("https://api.spotify.com/v1/artists/3Nrfpe0tUJi4K4DXYWgMUX/albums", headers=headers)

    raw = json.loads(r.text)

    total = raw['total']
    offset = raw['offset']
    limit = raw['limit']
    next = raw['next']

    albums = []
    albums.extend(raw['items'])

    ## 난 100개만 뽑아 오겠다
    while next:

        r = requests.get(raw['next'], headers=headers)
        raw = json.loads(r.text)
        next = raw['next']
        print(next)

        albums.extend(raw['items'])
        count = len(albums)
    print(len(albums))

def get_headers(client_id, client_secret):

    endpoint = "https://accounts.spotify.com/api/token"
    encoded = base64.b64encode("{}:{}".format(client_id, client_secret).encode('utf-8')).decode('ascii')

    headers = {
        "Authorization": "Basic {}".format(encoded)
    }

    payload = {
        "grant_type": "client_credentials"
    }

    r = requests.post(endpoint, data=payload, headers=headers)

    access_token = json.loads(r.text)['access_token']

    headers = {
        "Authorization": "Bearer {}".format(access_token)
    }

    return headers

if __name__=='__main__':
    main()
```


