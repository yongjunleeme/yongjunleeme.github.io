---
layout  : wiki
title   : 
summary : 
date    : 2020-05-19 18:49:27 +0900
updated : 2020-05-20 22:33:03 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Apache Spark

- 다양한 프로그래밍 언어 API 제공
- 머신러닝 패키지 등 다양한 패키지
- Map Reduce 
    - 여러 노드에 태스크를 분배하는 방법(병렬분산)
    - 데이터 규모가 커지면 요구되는 효율 상승을 위한 매핑방법
        - 예) 큰 규모의 텍스트를 64MB 단위로 나누고 특정 단어가 몇 개 있는지 계산
        - [맵리듀스 이해하기](https://12bme.tistory.com/154)

<img width="655" alt="스크린샷 2020-05-19 오후 1 32 58" src="https://user-images.githubusercontent.com/48748376/82285373-46e9cd00-99d6-11ea-8d20-f98a6d515d79.png">

### AWS EMR(Elastic Map Reducer)

- 스파크나 하둡 등의 클러스터(서버들)
- 제플린 - 스파크 기반 웹 UI

#### 사용방법

- IAM(Identity and Access Management) 권한 설정(aws cli 참고)
    - 액세스 관리
- Create
    - 애플리케이션 - Spark
    - 인스턴스 유형 - c4.large
- Security Group
    - 마스터 보안 그룹 - 선택 후 하단에 Inbound 규칙
        - SSH 추가 -> 내 IP 추가 
        - or SSH 추가 -> 0.0.0.0/0
- 연결: Enable Web Connection(웹 연결 활성화)

    - 활성화 코드

```python
$ ssh -i ~/key.pem -ND 8157 hadoop@ec2-3-34-136-247.ap-northeast-2.compute.amazonaws.com 
```

- 크롬 웹스토어 -foxy proxy standard 추가
- 로컬 폴더 내 foxyproxy-settings.xml 파일 추가
    - 코드 복붙

- foxy proxy 아이콘 클릭
    - options - ImportExport - Choose File - foxyproxy-settings.xml 선택
    - 아이콘 다시 클릭 - Use porxy emr

- Public DNS 카 - 웹주소로 이동
- 설정 창에서 제플린 클릭

### 제플린

#### 예제

- 파이썬

```python
%pyspark

row = '{"name": "Drake"}', '{"name": "Travis Scott"}', '{"name": "Post Malone"}'

import json
print(json.loads(raw)['name'])
```

- 스파크
- 데이터를 쪼개서 RDB로 저장
- for loop 아니라 한 번에 매핑

```python
%pyspark

rdd = sc.parallelize(raw)
rdd.count()

rdd.map(json.loads).map(lambda entry: entry['name']).count()
```

#### S3에서 로드

```python
from datetime import datetime # 나중에는 날짜 하드코딩 말고 .format으로 연결

raw = sqlContext.read.format('parquet').load('s3://spotify-artists/top-tracks/dt=2019-10-27/top-tracks.parquet')
# data = sc.textFile('text.json') 곧장 리드하는 방법


raw.printSchema()

df = raw.toDF('id', 'artist_id', 'name', 'popularity', 'external_url')
df.show() # 리스트
```

#### select, filter

```python
raw = sqlContext.read.format('parquet').load('s3://spotify-artists/audio-features/dt=2019-10-27/top-tracks.parquet')
raw.printSchema()
df1 = raw.toDF('danceability', 'energy', 'key', 'loudness', 'mode', 'speechiness', 'acousticness', 'instrumentalness', 'liveness', 'valence', 'tempo', 'type', 'id', 'url', 'track_href', 'analysis_url', 'duration_ms', 'time_signature')
df1.show()

# subset - select -> 칼럼
df2 = df1.select(df1['danceability'], df1['id'], df1['acousticness'])
df2.show()

# filter -> 로우 - 아웃라이어 쳐내는 작업
df3 = df2.filter(df2['danceability'] >= 0.30 & df2['acousticness'] >= 0.1).distinct()
df.show()
```

#### functions, udf 

```python
import pyspark.sql.functions as F

df_new = df1.select(df1['danceability'], df1['accousticness'], df1['liveness']).agg(F.avg(df1['danceability']).alias('avg_danceability'), F.max(df1['acousticness']))
df_new.show()
```

```python
# UDF - User Define Function
import pyspark.sql.functions import udf
import pyspark.sql.type import *

udf1 = udf(lambda e: e.upper())

@def(returnType=BooleanType())
def udf2(e):
    if e >= 0.06:
        return True
    else:
        return False

df.filtered = df1.filter(udf2(df1['danceability']))
df_filtered.show()
```

#### Join

##### artist s3에 저장

```python
def main():
    try:
        conn = qymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
        cursor = conn.cursor() 
    except:
        logging.error('could not connect to rds')
        sys.exit(1)
    
    cursor.execute('SELECT * FROM artists')
    colnames = [d[0] for d in cursor.description]
    artists = [dict(zip(colnames, row)) for row in cursor.fetchall()]
    artists = pd.DataFrame(artists)
    
    artists.to_parquet('artists.parquet', engine='pyarrow', compression='snappy')
    
    dt = datetime.utcnow().strftime('%Y-%m-%d')
    
    s3 = boto3.resource('s3')
    object = s3.Object('spotify-artists', 'artists/dt={}/artists.parquet'.format(dt))
    object.put(Body=data)

if __name__ == '__main__':
    main()
```

- EMR 서버 접속
    - -ND 8157 삭제

```python
$ ssh -i fastcampus.pem hadoop@ec2-13-125-9-238.ap-northeast-2.compute.amazonaws.com

$ sudo pip install pandas
$ sudo pip install pymysql
``` 

##### Spark code

```python
%pyspark

artists = sqlContext.read.format('parquet').load('s3://spotify-artists/artists/dt=2019-11-10/artists.parquet')
artists.toDF('id', 'name', 'followers', 'popularity', 'url', 'image_url')

import pandas as pd
import pymysql

host = "fastcampus.cxxbo4jh5ykm.ap-northeast-2.rds.amazonaws.com"
port = 3306
username = "sean"
database = "production"
password = "fastcampus"

try:
    conn = qymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
    cursor = conn.cursor() 
except:
    logging.error('could not connect to rds')
    sys.exit(1)
    
cursor.execute('SELECT * FROM artists')
colnames = [d[0] for d in cursor.description]
artists = [dict(zip(columns, row)) for row in cusor.fetchall()]
artists = pd.DataFrame(artists)

artists.head()

artists = sqlContext.createDataFrame(artists)
artists.show()

top_tracks = sqlContext.read.format('parquet').load('s3://spotify-artists/top-tracks/db=2019-10-27/top-tracks.parquet')
top_tracks = top_tracks.toDF('id', 'artist_id', 'name', 'popularity', 'external_url')

joined = artist.join(df, df['artist_id'] == artist['id']) # 기본이 inner join
joined = artist.join(df, df['artist_id'] == artist['id'], 'left_outer') # 인자로 조인 종류 선택 가능

joined.show()
```

#### SQL

```python
%pyspark

artists = sqlContext.read.format('parquet').load('s3://spotify-artists/artists/dt=2019-11-10/artists.parquet')
artists.toDF('id', 'name', 'followers', 'popularity', 'url', 'image_url')

import pandas as pd
import pymysql

host = "fastcampus.cxxbo4jh5ykm.ap-northeast-2.rds.amazonaws.com"
port = 3306
username = "sean"
database = "production"
password = "fastcampus"

try:
    conn = qymysql.connect(host, user=username, passwd=password, db=database, port=port, use_unicode=True, charset='utf8')
    cursor = conn.cursor() 
except:
    logging.error('could not connect to rds')
    sys.exit(1)
    
cursor.execute('SELECT * FROM artists')
colnames = [d[0] for d in cursor.description]
artists = [dict(zip(columns, row)) for row in cusor.fetchall()]
artists = pd.DataFrame(artists)

artists.head()

artists = sqlContext.createDataFrame(artists)
artists.show()

top_tracks = sqlContext.read.format('parquet').load('s3://spotify-artists/top-tracks/db=2019-10-27/top-tracks.parquet')
top_tracks = top_tracks.toDF('id', 'artist_id', 'name', 'popularity', 'external_url')
top_tracks = top_tracks.withColumnRenamed('id', 'track_id').withColumnRenamed('name', 'track_name')


joined = artist.join(df, df['artist_id'] == artist['id']) # 기본이 inner join
joined = artist.join(df, df['artist_id'] == artist['id'], 'left_outer') # 인자로 조인 종류 선택 가능
joined.show()



joined.registerTempTable('joined') # 조인된 데이터프레임을 템프테이블로 등록
```

```python
%sql

SELECT id, name, track_name FROM joiend LIMIT 10
```

#### 데이터 분석















## Link

- [올인원 데이터엔지니어링](https://www.fastcampus.co.kr/data_online_engineering)
