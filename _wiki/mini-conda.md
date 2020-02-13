---
layout  : wiki
title   : mini-conda 
summary : 
date    : 2020-02-13 19:13:25 +0900
updated : 2020-02-13 19:27:39 +0900
tags    : 
toc     : true
public  : true
parent  : tool
latex   : false
---
* TOC
{:toc}

## 기본 명령어

* conda 가상환경 목록을 보는 명령어

```shell
$ conda env list
```

* conda 가상환경 만들기

```shell 
$ conda create -n "가상환경이름" python=3.8
```

* conda 내가 만든 가상환경으로 활성화하기

```shell
$ conda activate "가상환경이름"
```

* conda 실행된 가상환경 비활성화하기

```shell
$ conda deactivate
```

* conda 가상환경 삭제하기
```shell
$ conda env remove -n "가상환경이름"
```

* conda 가상환경 익스포트하기(배포용 yaml만들기)

```shell
$ conda env export> "가상환경이름.yaml"
```

* conda 익스포트한 가상환경 임포트하기
```shell
$ conda env create -f "가상환경이름.yaml"
```

가상환경명은 프로젝트명으로 통일하는 것으로 권장합니다.
