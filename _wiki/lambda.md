---
layout  : wiki
title   : 
summary : 
date    : 2020-05-25 09:21:17 +0900
updated : 2020-06-02 15:11:05 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

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

### 설정


- Basic settings
    - 간단명료한 잡들을 병렬처리하는 것이 서버리스에 적합

```pyhon
$ pip install requirements.txt -t ./libs # t -> target
# setup.cfg 생성(하단 참조) - 포인터 이슈
```

```python
# libs 폴더 내 필요없는 파일 삭제
$ rm -r *.dist-info
```

```python
$ chmod +x deploy.sh
$ ./deploy.sh
```


## 

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

