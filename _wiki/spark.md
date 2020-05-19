---
layout  : wiki
title   : 
summary : 
date    : 2020-05-19 18:49:27 +0900
updated : 2020-05-19 18:52:57 +0900
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


## Link

- [올인원 데이터엔지니어링](https://www.fastcampus.co.kr/data_online_engineering)
