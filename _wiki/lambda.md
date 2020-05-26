---
layout  : wiki
title   : 
summary : 
date    : 2020-05-25 09:21:17 +0900
updated : 2020-05-26 15:25:39 +0900
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
aws s3 cp ./top_tracks.zip s3://top-tracks-lambda/top_tracks.zip
aws lambda update-function-code --function-name top-tracks --s3-bucket top-tracks-lambda --s3-key top_tracks.zip
```

#### setup.cfg

```python
# filename setup.cfg

[install]
prefix=
```

### 설정

- create 
    - 환경변수 설정

```python
client_id = os.environ.get('client_id') # 하단 Enviroment Variables에서 client_id 등 계정 기입
```

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
