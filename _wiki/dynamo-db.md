---
layout  : wiki
title   : 
summary : 
date    : 2020-05-16 17:13:03 +0900
updated : 2020-05-19 18:52:57 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

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

## Link

- [올인원 데이터엔지니어링](https://www.fastcampus.co.kr/data_online_engineering)
