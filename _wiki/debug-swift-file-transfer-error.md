---
layout  : wiki
title   : 
summary : 
date    : 2022-10-15 21:46:13 +0900
updated : 2022-10-15 21:47:06 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# 오픈스택의 Object Storage인 Swift API를 사용할 때 간헐적으로 특정 파일 깨지는 버그

> 내가 직접 겪지 않았지만 귀동냥한 사례를 기록으로 남긴다.

## 상태 파악
- txt파일을 다운로드하면 Headers의 내용이 txt 파일에 담겨 나온다.
- image 파일을 다운로드하면 사이즈가 바뀌거나 깨진다.

## 문제 정의
- 원인은 모르겠지만 headers에 잘못된 설정이 들어간 것이 아닌가 싶다.
- 파일 전송하는 과정에서 오류가 발생할 여지가 있는가 싶다.

## 해결 방법
### `requests.text` -> `requests.content`로 변경
	
파이썬에서 문자열은 유니코드다. 유니코드는 디스크에 저장할 수 없으므로 바이트로 인코딩해야 한다. 파이썬 라이브러리 `request`의 응답을 받을 때 유니코드인 `request.text`를 raw data인 `request.content`로 변경한다.

```python
>>> type(requests.get("http://www.google.com").text)
<class 'str'>

>>> type(requests.get("http://www.google.com").content)
<class 'bytes'>
```

### openstack ACL(Access Control Lists)를 참조
 컨테이너 액세스 권한 부여를 위해 headers에 `X-Container-Write`, `X-Container-Read`를 추가한다.

## Source

- [Bytes not the same when converting from bytes to string to bytes](https://stackoverflow.com/questions/71888900/bytes-not-the-same-when-converting-from-bytes-to-string-to-bytes)
- [Request.content](https://requests.readthedocs.io/en/latest/user/advanced/?highlight=response.content#encodings)
- [Request.text](https://requests.readthedocs.io/en/latest/api/?highlight=response.text#requests.Response.text)
- [Access Control Lists](https://docs.openstack.org/swift/latest/overview_acl.html)

