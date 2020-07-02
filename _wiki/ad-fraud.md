---
layout  : wiki
title   : 
summary : 
date    : 2020-07-02 22:16:54 +0900
updated : 2020-07-02 22:59:07 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Click Spamming

- 가짜로 대량의 클릭 시그널을 만들어서 MMP에 보내는 방식
- 가짜로 보낸 많은 디바이스 ID 중 실제 그 디바이스 주인이 설치하는 경우 발생
- 트래커는 실제 ID가 아닌 가짜 ID가 인스톨한 것으로 처리
- 자신들이 보낸 클릭 시그널이 라스트 터치 모델의 라스트 클릭이 돼 오가닉이나 타 채널 인스톨 어트리뷰선을 가져온다고 기대함
- 클릭시그널 방법 두 가지
    - Clicks-on-Impressions
        - 광고가 발생할 때마다 클릭했다고 보내는 방식
    - Clicks without Impressions
        - 유저가 광고를 보지도 않았는데 클릭 시그널만 대량으로 보내는 방식

## Click Injection

- Install Broadcast
    - 안드로이드에서는 디바이스에 앱이 설치될 때 설치가 시작되었다고 다른 앱들에게 시그널을 보냄
        - 특정 앱이 다른 앱의 UX에 변경을 주는 케이스가 있기 때문
- 인스톨 브로드캐스트를 사용해서 안드로이드 기반에서 발생하는 광고 사기
    - 앱이 다운로드되는 것을 인스톨 브로드캐스트 기능을 통해 알게 되면 가짜 클릭 시그널을 보내서 인스톨을 가로챈다


## Link

- [올인원 패키지:퍼포먼스 마케팅](https://www.fastcampus.co.kr/)
