---
layout  : wiki
title   : 
summary : 
date    : 2020-05-11 20:19:47 +0900
updated : 2020-06-19 16:05:02 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## spotify api 

### Search

- [Search Guide](https://developer.spotify.com/documentation/web-api/reference/search/search/)
- [Authorization Guide](https://developer.spotify.com/documentation/general/guides/authorization-guide/)


```python
import sys
import requests
import base64
import json
import logging

client_id = "74cbd487458843f1ad3f5fa1e914c02f"
client_secret = "752e4ed11062473f9da9076c4499d51b"

def main():
    headers = get_headers(client_id, client_secret)

    ## Spotify Search API
    params = {  # 변수 용도는 api 문서 참고
        "q": "BTS",
        "type": "artist",
        "limit": "5"
    }

    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    print(r.status_code)
    print(r.text)
    print(r.headers)
    sys.exit(0) # 에러 없이 테스트하는 방법

def get_headers(client_id, client_secret): # 토큰 만료 대비 토큰 생성 함수
    endpoint = "https://accounts.spotify.com/api/token"
    encoded = base64.b64encode("{}:{}".format(client_id, client_secret).encode('utf-8')).decode('ascii')
    headers = {
        "Authorization": "Basic {}".format(encoded)
    } 
    payload = {
        "grant_type": "client_credentials"
    } 
    r = requests.post(endpoint, data=payload, headers=headers) 
    access_token = json.loads(r.text)['access_token'] # json 변환 전에는 str 
    headers = {
        "Authorization": "Bearer {}".format(access_token)
    } 
    return headers

if __name__=='__main__':
    main()
```

### 예외처리

```python
import sys
import requests
import base64
import json
import logging

client_id = "74cbd487458843f1ad3f5fa1e914c02f"
client_secret = "752e4ed11062473f9da9076c4499d51b"

def main():
    headers = get_headers(client_id, client_secret)
    
    ## Spotify Search API
    params = {
        "q": "BTS",
        "type": "artist",
        "limit": "5"
    }
    
    try:
        r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    except:
        logging.error(r.text)
        sys.exit(1)
    
    if r.status_code != 200:
        logging.error(r.text)
        if r.status_code == 429: # Too many requests
            retry_after = json.loads(r.headers)['Retry-After']
            time.sleep(int(retry_after))
            r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)

        ## access_token expired
        elif r.status_code == 401:
            headers = get_headers(client_id, client_secret)
            r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
        else:
            sys.exit(1)

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

### Pagination

```python
import sys
import requests
import base64
import json
import logging

client_id = "74cbd487458843f1ad3f5fa1e914c02f"
client_secret = "752e4ed11062473f9da9076c4499d51b"

def main():

    headers = get_headers(client_id, client_secret)

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

        albums.extend(raw['items']) # extend -> iterable 한 객체를 뒤에 붙이는 메소드? 
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

## Background

### aws cli 설정

```python
$ pip install awscli

# aws 홈페이 내 IAM → 사용자 → 사용자 추가
# 프로그래밍 방식 액세스 체크 ( CLI 허용여부)
# 정책 → AdministratorAccess 체크

$ aws configure

# 홈페이지에 나온 ID와 액세스 키 입력
# 데이터 리전 입력 - ap-norhest-2
# output format -> 엔터 (None)

$ aws help
```

- 확인

```python
# vim ~/.aws/credentials (Linux 및 Mac)
# vim ~/.aws/config (Linux 및 Mac)
```

### `deploy.sh`

- `>`, `>>` 문법
    - command > file  → 기존 지우고 저장
    - 예) $ python example.py > test.txt
    - command >> file  → 기존 안 지우고 이어서 저장

```python
$ vim run.sh  
# 자동 실행될 커맨드 명령어 작성

$ chmod +x run.sh  
# 권한 설정

$ ./run.sh
# 실행
```

### ERD

Entities: 하나의 개체 (사람)
Attributes: 엔터티의 속성 (성, 이름)

- Unique key, pk 차이

<img width="666" alt="44" src="https://user-images.githubusercontent.com/48748376/81815785-652e7380-9565-11ea-9666-5ff9715effe8.png">

- ERD 형식

<img width="1319" alt="11" src="https://user-images.githubusercontent.com/48748376/81815764-5f389280-9565-11ea-8511-3cd434f39ca4.png">

- 티켓마스터 ERD

<img width="839" alt="33티켓마스터" src="https://user-images.githubusercontent.com/48748376/81815781-6495dd00-9565-11ea-8c1b-4ae9152cae3d.png">

- Spotify ERD

<img width="1219" alt="55" src="https://user-images.githubusercontent.com/48748376/81815786-65c70a00-9565-11ea-846f-e21d65a60f3f.png">

## RDS에서 pymysql 사용해 DB 호출

### RDS에서 테이블 생성 

```python
$ mysql --help

# RDS 접속
$ mysql -h fastcampus.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -P 3306 -u yongjunlee -p

# DATABASE 생성
mysql> CREAE DATABASE production;

# DATABASE 연결
$ mysql -h fastcampus.c2i2ypp7xnkb.ap-northeast-2.rds.amazonaws.com -P 3306 -D production -u yongjunlee -p

# TABLE 생성
mysql> CREATE TABLE people (first_name VARCHAR(20), last_name VARCHAR(20), age INT);
```

### sql문으로 테이블 호출

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
        sys.exit(1) 

    cursor.execute("SHOW TABLES")
    print(cursor.fetchall())
    
    # table 출력 형태
    # (('artist_genres',),('artists',),('people,'))

    print("success")

if __name__=='__main__':
    main()
```

### SQL 명령어

#### create

```python
mysql> CREATE TABLE artists(id VARCHAR(255), name VARCHAR(255), followers INTEGER, popularity INTEGER, url VARCHAR(255), image_url VARCHAR(255), PRIMARY KEY(id)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

" 이모티콘포함 -> CHARSET='utf8mb4' COLLATE 'utf8mb4_unicode_ci'"

mysql> CREATE TABLE artist_genres (artist_id VARCHAR(255), genre VARCHAR(255)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

mysql> show create table artists;
```

#### insert

```python
mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');

mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');
```

#### delete

```python
mysql> DELETE FROM artist_genres;

mysql> DROP TABLE artist_genres;
```

#### update

- unique key 추가

```python
mysql> CREATE TABLE artist_genres (artist_id VARCHAR(255), genre VARCHAR(255), UNIQUE KEY(artist_id, genre)) ENGINE=InnoDB DEFAULT CHARSET='utf8';
```

- 같은 id의 값들을 넣어도 1개로 업데이트된다
- 그러나 모든 정보를 알고 있어야 해서 잘 안 쓴다?

```python
mysql>UPDATE artist_genres SET genre='pop' where artist_id = '1234';

mysql>UPDATE artist_genres SET genre='pop' where artist_id = '1234';
```

#### replace

- 컬럼 추가

```python
mysql>ALTER TABLE artist_genres ADD COLUMN country VARCHAR(255);
```

- 업데이트 시간 컬럼 추가
    - 디폴트가 현재 시간이고 업데이트될 떄마다 현재 시간으로 업데이트 한다

```python
mysql>ALTER TABLE artist_genres ADD COLUMN updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
```

- insert - 에러 
  
```python
INSERT INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'pop', 'UK');
```

- replace - 기존 데이터에 없으면 생성, 있으면 지운 다음 생성
    - 단점
        - 1지우고 2다시 생성 2스텝
        - id가 변할 수 있다

```python
mysql>REPLACE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'pop', 'UK');
```

#### insert ignore

- 기존 데이터에 있으면 무시

```python
mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'UK');

mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR');
```

#### duplicate key update

- replace와 비슷하지만 지우지 않고 업데이트
- 실제 코딩 시 거의 이것만 쓴다

```python
mysql>INSERT INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR') ON DUPLICATE KEY UPDATE artist_id='1234', genre='rock', country='FR';
```

#### id autoincrement

```python
mysql>id primary_key auto_increment
```

```python
mysql> alter table artist_genres drop column country;
```

### pymysql 데이터 핸들링

#### {}.format으로 데이터 입력

```python
...
def main():

    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1)

    cursor.execute("SHOW TABLES")
    print(cursor.fetchall())

    print("success")

    query = "INSERT INTO artist_genres (artist_id, genre, updated_at) VALUES ('{1}', '{0}', NOW())".format('1234', 'hip-hop')
    
    # query = "INSERT INTO artist_genres (artist_id, genre) VALUES ('%s', '%s') % (artist_id, genre)"
    
    cursor.execute(query)
    conn.commit()
    
    sys.exit(0)
```

### tip

- 키값을 모를 때

```python
raw = json.loads(r.text)
print(raw['artist'].keys()) # 키 출력해서 다음 뎁스로 넘어간다
```

- 에러나면 sys.exit(0) 놓고 위에 프린트 찍어가며 디버깅

### 하드코딩 

```python
...
def main():
    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1)

    headers = get_headers(client_id, client_secret)

    ## Spotify Search API
    params = {
        "q": "BTS",
        "type": "artist",
        "limit": "1"
    }

    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)

    raw = json.loads(r.text)
    print(type(raw))
    print(raw['artists']['items'][0].keys())

    artist = {}

    artist_raw = raw['artists']['items'][0]
    if artist_raw['name'] == params['q']:

        artist.update( # update -> 딕셔너리 한 번데 업데이트하는 함수
            {
                'id': artist_raw['id'],
                'name': artist_raw['name'],
                'followers': artist_raw['followers']['total'],
                'popularity': artist_raw['popularity'],
                'url': artist_raw['external_urls']['spotify'],
                'image_url': artist_raw['images'][0]['url']
            }
        )

    query = """
        INSERT INTO artists (id, name, followers, popularity, url, image_url)
        VALUES ('{}', '{}', {}, {}, '{}', '{}')
        ON DUPLICATE KEY UPDATE id='{}', name='{}', followers={}, popularity={}, url='{}', image_url='{}'
    """.format(
            artist['id'],
            artist['name'],
            artist['followers'],
            artist['popularity'],
            artist['url'],
            artist['image_url'],
            artist['id'],
            artist['name'],
            artist['followers'],
            artist['popularity'],
            artist['url'],
            artist['image_url']
    )
    cursor.execute(query)
    conn.commit()

    sys.exit(0)


    try:
        r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
...
```

### 함수로 코드 최적화 

```python
def main():
    ...
    if artist_raw['name'] == params['q']:

        artist.update(
            {
                'id': artist_raw['id'],
                'name': artist_raw['name'],
                'followers': artist_raw['followers']['total'],
                'popularity': artist_raw['popularity'],
                'url': artist_raw['external_urls']['spotify'],
                'image_url': artist_raw['images'][0]['url']
            }
        )
        
    # query 하드코딩 삭제

    insert_row(cursor, artist, 'artists')
    conn.commit()

    sys.exit(0)
    ...
...
def insert_row(cursor, data, table):

    placeholders = ', '.join(['%s'] * len(data)) # data -> artist -> %s, %s, %s ...
    columns = ', '.join(data.keys())
    key_placeholders = ', '.join(['{0}=%s'.format(k) for k in data.keys()]) # id=%s, name=%s...
    sql = "INSERT INTO %s ( %s ) VALUES ( %s ) ON DUPLICATE KEY UPDATE %s" % (table, columns, placeholders, key_placeholders)
    # print(sql)
    # sys.exit(0)
    cursor.execute(sql, list(data.values())*2) # insert into values, duplicate key update values 2개 넣어야 해서 *2

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

### csv 데이터 삽입

- csv파일의 artist name 토대로 이후 artist_id 저장

```python
...
def main():

    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1)

    headers = get_headers(client_id, client_secret)

    ## Spotify Search API

    artists = []
    with open('artist_list.csv') as f:
        raw = csv.reader(f)
        for row in raw:
            artists.append(row[0])

    for a in artists:
        params = {
            "q": a,
            "type": "artist",
            "limit": "1"
        }

        r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)

        raw = json.loads(r.text)

        artist = {}
        try:
            artist_raw = raw['artists']['items'][0]
            if artist_raw['name'] == params['q']: # 아티스트가 데이터에 있으면

                artist.update(
                    {
                        'id': artist_raw['id'],
                        'name': artist_raw['name'],
                        'followers': artist_raw['followers']['total'],
                        'popularity': artist_raw['popularity'],
                        'url': artist_raw['external_urls']['spotify'],
                        'image_url': artist_raw['images'][0]['url']
                    }
                )
                insert_row(cursor, artist, 'artists')
        except:
            logging.error('something wrong') # 에러 로그
            continue # 에러 뜨더라도 프로세스 지속

    conn.commit()
    sys.exit(0)
...

```
- mysql에서 확인

```python
mysql> SELECT COUNT(*) FROM artists;
```

### Batch 

- RDS에서 SQL 통해 id를 가져온 다음 0~49번쨰, 50~99번째,... 50개씩 batch

```python
...
main():
    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1)

    headers = get_headers(client_id, client_secret)
    
    cursor.execute("SELECT id FROM artists")
    artists = []

    for (id, ) in cursor.fetchall():
        artists.append(id)

    artist_batch = [artists[i: i+50] for i in range(0, len(artists), 50)]

    for i in artist_batch:

        ids = ','.join(i)
        URL = "https://api.spotify.com/v1/artists/?ids={}".format(ids)

        r = requests.get(URL, headers=headers)
        raw = json.loads(r.text)
        print(raw)
        print(len(raw['artists']))

        sys.exit(0)
...
```

### batch2

```python
def main():
    cursor.execute("SELECT id FROM artists")
    artists = []

    for (id, ) in cursor.fetchall():
        artists.append(id)

    artist_batch = [artists[i: i+50] for i in range(0, len(artists), 50)]

    artist_genres = []
    for i in artist_batch:
        ids = ','.join(i)
        URL = "https://api.spotify.com/v1/artists/?ids={}".format(ids)

        r = requests.get(URL, headers=headers)
        raw = json.loads(r.text)

        for artist in raw['artists']:
            for genre in artist['genres']:

                artist_genres.append(
                    {
                        'artist_id': artist['id'],
                        'genre': genre
                    }
                )

    for data in artist_genres:
        insert_row(cursor, data, 'artist_genres')

    conn.commit()
    cursor.close()

    sys.exit(0)
```

```python
mysql> SELECT genre, COUNT(*) FROM artists t1 JOIN artist_genres t2 ON t2.artist_id = t1.id WHERE t1.poularity > 80 GROUP BY 1 ORDER BY 2 DESC LIMIT 20;
```

## Background 

- 파티션
    - 데이터 매니지먼트, 퍼포먼스 등의 이유로 데이터를 나누는 일
    - Vertical - Going beyond normalization
        - 더 적은 테이블들로 나누는 잡업으로써 노멀라이제션 후에도 경우에 따라 컬럼을 나누는 파티션 작업을 한다
    - Horizontal
        - 스키마 / 구조 자체를 카피해 데이터 자체를 Sharded Key로 분리
        - Sharded Key키로 id를 찾아서 빠르게 액세스하기 위해
        - nosql에서는 자주 쓰인다


### 연결 

- boto3 - aws가 제공하는 파이썬 SDK
    - [aws cli confing](https://yongjunleeme.github.io/wiki/spotify-rds/#aws-cli)

```python
$ pip install boto3 --user
```

### 데이터 삽입

- RDS에 저장한 artist_id 불러와서 top_tracks api 통해 요청하고 데이터는 dynamodb에 저장

```python
import sys
import os
import boto3
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
        dynamodb = boto3.resource('dynamodb', region_name='ap-northeast-2', endpoint_url='http://dynamodb.ap-northeast-2.amazonaws.com')
    except:
        logging.error('could not connect to dynamodb')
        sys.exit(1)

    try:
        conn = pymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor()
    except:
        logging.error("could not connect to rds")
        sys.exit(1)

    headers = get_headers(client_id, client_secret)

    table = dynamodb.Table('top_tracks')

    cursor.execute('SELECT id FROM artists')

    countries = ['US', 'CA']
    for country in countries:
        for (artist_id, ) in cursor.fetchall():

            URL = "https://api.spotify.com/v1/artists/{}/top-tracks".format(artist_id)
            params = {
                'country': 'US'
            }

            r = requests.get(URL, params=params, headers=headers)

            raw = json.loads(r.text)

            for track in raw['tracks']:

                data = {
                    'artist_id': artist_id, # track에 없는 데이터러 가져 옴
                    'country': country
                }

                data.update(track) # data뿐만 아니라 정의하지 않은 모든 데이터들도 업데이트

                table.put_item( # 데이터 많으면 Batch writing 사용
                    Item=data
                )

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

### query, filter

```python
import sys
import os
import boto3

from boto3.dynamodb.conditions import Key, Attr # query 쓰려면 임포트

def main():

    try:
        dynamodb = boto3.resource('dynamodb', region_name='ap-northeast-2', endpoint_url='http://dynamodb.ap-northeast-2.amazonaws.com')
    except:
        logging.error('could not connect to dynamodb')
        sys.exit(1)

    table = dynamodb.Table('top_tracks')

    response = table.query(
        FilterExpression=Attr('popularity').gt(90) # 애트리뷰트를 사용, 공식문서 참조, gt -> greater than
    )
    %% response = table.get_item(  # 키가 2개면 2개 모두 필요(파티션 키, 소트 키)
    %%     key={
    %%             'artist_id': '',
    %%             'id': ''
    %%     }
    
    %% reponse = table.query( # 키를 사용
    %%     KeyConditionExpression=Key('artist_id').eq('') # eq -> equal
    %% )
    %% reponse = table.scan( # 키를 모를 때 스캔 사용, 그러나 모든 데이터를 돌아야 하므로 큰 규모에서는 지양해야
    %%     FilterExpression=Attt('popularity').gt(90)
    %% )
    )
    
    print(response['Items'])
    print(len(response['Items']))
    
if __name__=='__main__':
    main()

```

## S3(Simple Storage System)

- ETL(Extaract, Transform, Load) -> ELT(Extract, Load, Transform)
- AWS Glue
    - 다양한 데이터 스토어 간 간단한 이동, 데이터 추출 변환 로드 
    - 크롤러 -> 테이블 변형 자동 감지


### 코드

- 필기한 참조용도
- `vim ~/.aws/config`에서 region설정을 `ap-northeast-2`가 아니라 `us-east-1`로 바꿔야 작동

```python
import pandas as pd
import jsonpath

def main():
    ...
    cursor.execute("SELECT id FROM artists")
    
    dt = datetime.utcnow().strftime("%Y-%m-%d")
    
    # with open('top_tracks.json', 'w') as f:
    #     for i in top_tracks:
    #         json.dump(i, f)
    #         f.write(os.linesep) # 라인별 저장
    
    top_track_keys = {
        "id": "id",
        "name": "name",
        "popularity": "popularity",
        "external_url": "external_urls.spotify"
    }
    
    top_tracks = []
    for (id, ) in cursor.fetchall():
        URL = "https://api.spotify.com/v1/artists/{}/top-tracks".format(id)
        params = {
            'country': 'US'
        }
        r = requests.get(URL, params=params, headers=headers)
        raw = json.loads(r.text)
        # top_tracks.extend(raw['tracks']) 
        
        for i in raw['tracks']:
            top_track = {}
            for k, v in top_track_keys.items():
                top_track.update({k: jsonpath.jsonpath(i, v)}) # i의 카값 중 v에 해당하는 것만 값으로 지정한다는 뜻 같은데.. 
                top_track.update({'artist_id': id})
                top_tracks.append(top_track)    
    
    track_ids = [i['id'][0] for i in top_tracks]
    
    top_tracks = pd.DataFrame(raw)
    top_tracks.to_parquet('top-tracks.parquet', engine='pyarrow', compressions='snappy') # 스파크에 쓸 데이터형식 - parquet을 판다스 통해 만든다, compressions -> 압축 형식
    
    s3 = boto3.resource('s3')
    object = s3.Object('spo-artists', 'dt={}/top-tracks.parquet'.format(dt)) # 첫번째 인자 - s3 버킷 이름, 두번째 인자 - 파티션으로 쓰일 키?, dt -> datetime
    data = open('top-tracks.parquet', 'rb')
    object.put(Body=data)
    
    # audio-features -> api 문서 several로 옵션 -> batch 가능
    track_batch = [track_id[i: i+100] for i in range(0, len(track_ids), 100)]
    
    audio_features = [] # audio-features는 nested 데이터가 아니므로
    for i in tracks_batch:
        ids = ','.join(i)
        URL = 'https://api.spotify.com/v1/audio-features/?ids={}'.format(ids)
        
        r = requests.get(URL, headers=headers)
        raw = json.loads(r.text)
        
        audio.features.extend(raw['audio_features'])
    
    audio_features = pd.DataFarme(audio_features)
    audio_features.to_parquet('audio-features.parquet', engine='pyarrow', compresssion='snappy')
    
    s3 = boto3.resource('s3')
    object = s3.Object('spo-artists', 'audio-features/dt={}/audio-features.parquet'.format(dt))
    data = open('audio-features.parquet', 'rb')
    object.put(Body=data)  
    
```

- 실행용도

```python
import sys
import os
import logging
import boto3
import requests
import base64
import json
import pymysql
from datetime import datetime
import pandas as pd
import jsonpath  # pip3 install jsonpath --user

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
        sys.exit(1)

    headers = get_headers(client_id, client_secret)

    # RDS - 아티스트 ID를 가져오고
    cursor.execute("SELECT id FROM artists LIMIT 10")

    top_track_keys = {
        "id": "id",
        "name": "name",
        "popularity": "popularity",
        "external_url": "external_urls.spotify"
    }
    # Top Tracks Spotify 가져오고
    top_tracks = []
    for (id, ) in cursor.fetchall():

        URL = "https://api.spotify.com/v1/artists/{}/top-tracks".format(id)
        params = {
            'country': 'US'
        }
        r = requests.get(URL, params=params, headers=headers)
        raw = json.loads(r.text)

        for i in raw['tracks']:
            top_track = {}
            for k, v in top_track_keys.items():
                top_track.update({k: jsonpath.jsonpath(i, v)})
                top_track.update({'artist_id': id})
                top_tracks.append(top_track)

    # track_ids
    track_ids = [i['id'][0] for i in top_tracks]

    top_tracks = pd.DataFrame(top_tracks)
    top_tracks.to_parquet('top-tracks.parquet', engine='pyarrow', compression='snappy')

    dt = datetime.utcnow().strftime("%Y-%m-%d") # utc -> unix time

    s3 = boto3.resource('s3')
    object = s3.Object('spotify-artists', 'top-tracks/dt={}/top-tracks.parquet'.format(dt))
    data = open('top-tracks.parquet', 'rb')
    object.put(Body=data)
    # S3 import

    tracks_batch = [track_ids[i: i+100] for i in range(0, len(track_ids), 100)]

    audio_features = []
    for i in tracks_batch:

        ids = ','.join(i)
        URL = "https://api.spotify.com/v1/audio-features/?ids={}".format(ids)

        r = requests.get(URL, headers=headers)
        raw = json.loads(r.text)

        audio_features.extend(raw['audio_features'])

    audio_features = pd.DataFrame(audio_features)
    audio_features.to_parquet('audio-features.parquet', engine='pyarrow', compression='snappy')

    s3 = boto3.resource('s3')
    object = s3.Object('spotify-artists', 'audio-features/dt={}/top-tracks.parquet'.format(dt))
    data = open('audio-features.parquet', 'rb')
    object.put(Body=data)



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

## Athena 

### query

- S3에 넣은 데이터를 Athena에서 쿼리
- 쿼리를 쓴 만큼 비용 청구
    - 파티셔닝을 해야 비용 절감
    - 예) 날짜 기준으로 파티셔닝
- presto functions (검색) 사용 가능

```python
CREATE EXTERNAL TABLE IF NOT EXISTS top_tracks(
    id string,
    artist_id string,
    name string,
    popularity int,
    external_url string  # 키값을 넣은 데이터만 가져오고 안 넣을 수도 있다
) PARTITIONED BY (dt string)
STORED AS PARQUET LOCATION 's3://spotify-artists/top-tracks/' tblproperties('parquet.compress'='SNAPPY')  # tbl -> table

MSCK REPAIR TABLE top_tracks # partitions
WHERE CAST(dt AS date) = CURRENT_DATE # dt가 스트링이라 CAST
# WHERE CAST(dt AS date) >= CURRENT_DATE - INTERVAL '7' DAY # 최근 7일간
LIMIT 10
```

```python
CREATE EXTERNAL TABLE IF NOT EXISTS audio_features(
    id string,
    danceability DOUBLE,
    energy DOUBLE,
    key int,
    loudness DOUBLE,
    mode int,
    speechiness DOUBLE,
    acousticness DOUBLE,
    instrumentalness DOUBLE
) PARTITIONED BY (dt string)
STORED AS PARQUET LOCATION 's3://spotify-artists/audio-features/' tblproperties('parquet.compress'='SNAPPY')  

MSCK REPAIR TABLE audio_features # 테이블 복원 
```

## [크론탭](https://www.adminschoice.com/crontab-quick-reference)

### EC2 리눅스 환경설정

- Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type 생성

- 서버접속

```python
$ ssh -i key.pem ec2-user@퍼블릭 DNS주소
```

- S3 저장 파일 복사 
    - `scp` -> 서버복사

```python
scp -i key.pem sporify_14.2.py ec2-user@퍼블릭 DNS주소:~/
```

- python, pip 설치

```python
$ sudo yum list | grep python3
$ sudo yum install python36

$ curl -O http -O https://bootstrap.pypa.io/get-pip.py
$ sudo python3 get-pip.py
$ pip3 install --upgrade pip
```

- 도커로 이미지 설정

### 크론탭

```python
# 설치
$ sudo service crond start

# 폴더위치 확인
$ pwd
$ which python

# 스크립트 창 오픈
$ crontab -e
```

- MALITO - 메일로 보내기
- * * * * *  분, 시간, 일, 월, 요일  
    - [크론탭](https://www.adminschoice.com/crontab-quick-reference)

```python
# 크론 탭 내 스크립트
MAILTO=chesidyong@gmail.com

# 실행할 언어?(파이썬 위치), 실행 파일 명시
30 18 * * * /user/bin/python3 /home/ec2-user/spotify_14.2.py
```

- 확인

```python
$ crontab -l
```

- UTC 고려해서 날짜 설정 - 한국보다 9시간 빠르다

```python
$ date
```

## lambda

### top_tracks

#### lambda_function.py

```python
import sys
sys.path.append('./libs')
import os
import boto3
import requests
import base64
import json
import logging


client_id = "74cbd487458843f1ad3f5fa1e914c02f"
client_secret = "752e4ed11062473f9da9076c4499d51b"

try:
    dynamodb = boto3.resource('dynamodb', region_name='ap-northeast-2', endpoint_url='http://dynamodb.ap-northeast-2.amazonaws.com')
except:
    logging.error('could not connect to dynamodb')
    sys.exit(1)


def lambda_handler(event, context):

    headers = get_headers(client_id, client_secret)

    table = dynamodb.Table('top_tracks')

    artist_id = event['artist_id']

    URL = "https://api.spotify.com/v1/artists/{}/top-tracks".format(artist_id)
    params = {
        'country': 'US'
    }
    r = requests.get(URL, params=params, headers=headers)

    raw = json.loads(r.text)

    for track in raw['tracks']:

        data = {
            'artist_id': artist_id
        }

        data.update(track)

        table.put_item(
            Item=data
        )

    return "SUCCESS"



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

#### deploy.sh

```python
#!/bin/bash

rm -rf ./libs 
pip3 install -r requirements.txt -t ./libs

rm *.zip
zip top_tracks.zip -r *

aws s3 rm s3://버킷이름/top_tracks.zip  # 버킷이름
aws s3 cp ./top_tracks.zip s3://버킷이름/top_tracks.zip
aws lambda update-function-code --function-name top-tracks --s3-bucket 버킷이름 --s3-key top_tracks.zip
```

#### setup.cfg

```python
# filename setup.cfg

[install]
prefix=
```

#### 실행

```python
$ chmod +x deploy.sh
$ ./deploy.sh
```


## ahena

```
athena data load -> artist avf metrics 
mertrics : max $ min
normalization
euclidean distance
mysql
```

```python
mysql> create table related_artists (artist_id VARCHAR(255), y_artist VARCHAR(255), distance FLOAT, PRIMARY KEY(artist_id, y_artist)) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

```python
import sys
import os
import logging
import pymysql
import boto3
import time
import math

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
        sys.exit(1)

    athena = boto3.client('athena')

    query = """
        SELECT
         artist_id,
         AVG(danceability) AS danceability,
         AVG(energy) AS energy,
         AVG(loudness) AS loudness,
         AVG(speechiness) AS speechiness,
         AVG(acousticness) AS acousticness,
         AVG(instrumentalness) AS instrumentalness
        FROM
         top_tracks t1
        JOIN
         audio_features t2 ON t2.id = t1.id AND CAST(t1.dt AS DATE) = DATE('2019-11-18') AND CAST(t2.dt AS DATE) = DATE('2019-11-18')
        GROUP BY t1.artist_id
        LIMIT 100
    """

    r = query_athena(query, athena)
    results = get_query_result(r['QueryExecutionId'], athena)
    artists = process_data(results)

    query = """
        SELECT
         MIN(danceability) AS danceability_min,
         MAX(danceability) AS danceability_max,
         MIN(energy) AS energy_min,
         MAX(energy) AS energy_max,
         MIN(loudness) AS loudness_min,
         MAX(loudness) AS loudness_max,
         MIN(speechiness) AS speechiness_min,
         MAX(speechiness) AS speechiness_max,
         ROUND(MIN(acousticness),4) AS acousticness_min,
         MAX(acousticness) AS acousticness_max,
         MIN(instrumentalness) AS instrumentalness_min,
         MAX(instrumentalness) AS instrumentalness_max
        FROM
         audio_features
    """
    r = query_athena(query, athena)
    results = get_query_result(r['QueryExecutionId'], athena)
    avgs = process_data(results)[0]

    metrics = ['danceability', 'energy', 'loudness', 'speechiness', 'acousticness', 'instrumentalness']

    for i in artists:
        for j in artists:
            dist = 0
            for k in metrics:
                x = float(i[k])
                x_norm = normalize(x, float(avgs[k+'_min']), float(avgs[k+'_max']))
                y = float(j[k])
                y_norm = normalize(y, float(avgs[k+'_min']), float(avgs[k+'_max']))
                dist += (x_norm-y_norm)**2

            dist = math.sqrt(dist) ## euclidean distance

            data = {
                'artist_id': i['artist_id'],
                'y_artist': j['artist_id'],
                'distance': dist
            }

            insert_row(cursor, data, 'related_artists')


    conn.commit()
    cursor.close()


def normalize(x, x_min, x_max):

    normalized = (x-x_min) / (x_max-x_min)

    return normalized


def query_athena(query, athena): # 프레스토 기반 ahena 빅데이터에 적합, mysql보다 쿼리 속도 느리다
    response = athena.start_query_execution(
        QueryString=query,
        QueryExecutionContext={
            'Database': 'production'
        },
        ResultConfiguration={
            'OutputLocation': "s3://athena-panomix-tables/repair/",
            'EncryptionConfiguration': {
                'EncryptionOption': 'SSE_S3'
            }
        }
    )

    return response


def get_query_result(query_id, athena):

    response = athena.get_query_execution(
        QueryExecutionId=str(query_id)
    )

    while response['QueryExecution']['Status']['State'] != 'SUCCEEDED':
        if response['QueryExecution']['Status']['State'] == 'FAILED':
            logging.error('QUERY FAILED')
            break
        time.sleep(5)
        response = athena.get_query_execution(
            QueryExecutionId=str(query_id)
        )

    response = athena.get_query_results(
        QueryExecutionId=str(query_id),
        MaxResults=1000
    )

    return response


def process_data(results):

    columns = [col['Label'] for col in results['ResultSet']['ResultSetMetadata']['ColumnInfo']]

    listed_results = []
    for res in results['ResultSet']['Rows'][1:]:
        values = []
        for field in res['Data']:
            try:
                values.append(list(field.values())[0])
            except:
                values.append(list(' '))
        listed_results.append(dict(zip(columns, values)))

    return listed_results


def insert_row(cursor, data, table):

    placeholders = ', '.join(['%s'] * len(data))
    columns = ', '.join(data.keys())
    key_placeholders = ', '.join(['{0}=%s'.format(k) for k in data.keys()])
    sql = "INSERT INTO %s ( %s ) VALUES ( %s ) ON DUPLICATE KEY UPDATE %s" % (table, columns, placeholders, key_placeholders)
    cursor.execute(sql, list(data.values())*2)

if __name__=='__main__':
    main()

```

## 챗봇

- 람다 function 생성
- api gateway - rest로 생성
    - 작업(action) - get 생생
        - lambda 함수 선택, lambda 함수명 기입
            - Integration request(통합 요청) 클릭
                - Mapping Templates > Content-Type -> application/json 추가 -> 아니오 선택
                - Generate template -> Method Request passthrough 선택
    - 작업(action) - post 선택
        - lambda 함수 선택, lambda 함수명 기입
    - 작업(action) - 배포 선택
        - deployment stage - New Stage 선택
        - stage name - prod
- invoke url 복사
    - [페이스북 개발자 웹사이트](https://developers.facebook.com/) > 마이 앱 > Messenger > Webhooks > Add Callback URL -> 여기에 넣어야 하는데 일단 보류
    - 액세스 토큰 -> 토큰 생성 복사
        - AWS 콘솔상 lambda > function code > PAGE_TOKEN에 붙여넣기

```python
import sys
import logging


PAGE_TOKEN = ''
VERIFY_TOKEN = 'verify_123'

def lambda_handler(event, context):
    if 'params' in event.keys():
        
        if event['params']['querystring']['hub.verify_token'] == VERIFY_TOKEN:
            return int(event['params']['querystring']['hub.chanllenge'])
        else:
            logging.error('wrong validation token')
            raise SystemExit
    else:
        return None
```

- lambda > api gateway > api endpoint 주소 복사 -> 페북개발자사이트 > Edit Callback URL -> callback URL 붙여넣기
    - Verify Token -> verify_123

## Link

- [올인원 데이터엔지니어링](https://www.fastcampus.co.kr/data_online_engineering)
