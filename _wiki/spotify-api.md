---
layout  : wiki
title   : 
summary : 
date    : 2020-05-11 20:19:47 +0900
updated : 2020-05-19 18:52:57 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Api

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
    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    try:
        r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
    except:
        logging.error(r.text)
        sys.exit(1)
    r = requests.get("https://api.spotify.com/v1/search", params=params, headers=headers)
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

        albums.extend(raw['items']) # extend -> 저장
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

## Link

- [올인원 데이터엔지니어링](https://www.fastcampus.co.kr/data_online_engineering)
