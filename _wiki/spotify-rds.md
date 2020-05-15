---
layout  : wiki
title   : 
summary : 
date    : 2020-05-13 22:04:38 +0900
updated : 2020-05-15 20:44:52 +0900
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
        sys.exit(1) 

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

## database 

### create

```python
mysql> CREATE TABLE artists(id VARCHAR(255), name VARCHAR(255), followers INTEGER, popularity INTEGER, url VARCHAR(255), image_url VARCHAR(255), PRIMARY KEY(id)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

" 이모티콘포함 -> CHARSET='utf8mb4' COLLATE 'utf8mb4_unicode_ci'"

mysql> CREATE TABLE artist_genres (artist_id VARCHAR(255), genre VARCHAR(255)) ENGINE=InnoDB DEFAULT CHARSET='utf8';

mysql> show create table artists;
```

### insert

```python
mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');

mysql> INSERT INTO artist_genres (artist_id, genre) VALUES ('1234', 'pop');
```

### delete

```python
mysql> DELETE FROM artist_genres;

mysql> DROP TABLE artist_genres;
```

### update

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

### replace

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

### insert ignore

- 기존 데이터에 있으면 무시

```python
mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'UK');

mysql>INSERT IGNORE INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR');
```

### duplicate key update

- replace와 비슷하지만 지우지 않고 업데이트
- 실제 코딩 시 거의 이것만 쓴다

```python
mysql>INSERT INTO artist_genres (artist_id, genre, country) VALUES ('1234', 'rock', 'FR') ON DUPLICATE KEY UPDATE artist_id='1234', genre='rock', country='FR';
```

### id autoincrement

```python
mysql>id primary_key auto_increment
```

```python
mysql> alter table artist_genres drop column country;
```

## pymysql 데이터 핸들링

### {}.format

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

    headers = get_headers(client_id, client_secret)

    ## Spotify Search API
    params = {
        "q": "BTS",
        "type": "artist",
        "limit": "5"
    }
...
```

### Dictionary, json

```python
raw = json.loads(r.text)
print(raw['artist'].keys()) # 키 출력해서 다음 뎁스로 넘어간다
```

### duplicate record 핸들링

- 에러나면 sys.exit(0) 놓고 위에 프린트 찍어가며 디버깅

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


if __name__=='__main__':
    main()
```

### csv 데이터 삽입

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
