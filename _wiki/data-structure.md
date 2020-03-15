---
layout  : wiki
title   : data-structure 
summary : 
date    : 2020-03-12 16:13:09 +0900
updated : 2020-03-15 19:19:00 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 자료구조 분류

- Primitive Data Structure(단순구조): 프로그래밍에서 사용되는 기본 데이터 타입
- Non-Primtive Data Sturcture(비단순 구조): 단순한 데이터를 저장하는 구조가 아니라 여러 데이터를 목적에 맞게 효과적으로 저장하는 자료 구조.
    - Linear Data Structure(선형구조): 저장되는 자료의 전후관계가 1:1 (리스트, 스택, 큐 등)
    - Non-Linear Data Structure(비선형구조): 데이터 항목 사이의 관계가 1:n 또는 n:m (트리, 그래프 등)

## Array(list)

- 순서가 있는 구조. 메모리에서 물리적으로도 순차적으로 저장
- 순서가 있으므로 인덱스가 가능
- When to use array
    - 특정 요소를 빠르게 읽어야 할 때
    - 데이터 사이즈가 급격히 변하지 않을 때
    - 요소를 자주 삭제하지 않을아도 될 때

### Array 단점

- 삭제할 때 중간에 비면 안 되므로 순서가 바뀐다.
- 리사이징을 할 때도 중간에 차지한 메모리를 치울 수 없으므로 애초 생성 시 비교적 큰 메모리를 할당
- 이에 사이즈가 급격히 늘어날 확률이 있는 데이터는 Array에 적합하지 않음


## Tuple

- 리스트와 다르게 수정 불가능, 리스트보다 가볍고 적은 메모리 소모
- 보통 2~5개 소규모요소를 저장하기 위해 사용
- 간단한 값을 빠르게 표현하기 위해
- 예) 좌표

## Set

- 데이터를 비순차적(unordered)으로 저장할 수 있는 순열 자료구조(collection)
- 삽입 순서대로 저장되지 않는다, 수정 가능하다.
- 저장할 요소 해시값을 구하고 해시값에 해당하는 공간(bucket)에 값을 저장
    - 순차적이 아닌 이유다.
    - 해시값 기반 버킷에 저장하므로 중복값 저장이 불가한 것
- 예) Fast Lookup
    - 특정 값 포함 여부 확인 `5 in my_set`
    - 셋의 총 길이 상관ㅅ없이 해시 값 ㄱㅖ산 후 해당 버킷만 확인하면 되므로 `0(1)`
    - 최상위 부모 객체의 주소가 해시값
    - 셋은 밑단에 어레이를 쓰고 있다.
    - 해시와 인덱스를 어떻게 연결?  나눈 나머지값을 인덱스로 사용
    - 중복이 나오면 '해시충돌'
    - 계속해서 충돌이 날 것 같으면 어레이 리사이징

## Dictionary

- 중복된 키는 저장 안 된다. 만일 중복도니 키가 있으면 먼저 있던 키와 값을 대체한다.
- 수정 가능
- `cord(1,2) == cord(1,2)` false가 나온다. 
    - 최상위 객체는 단순히 메모리 주소를 비교한다.
    - 개발자가 정의할 수 있다.
- set과 비슷하게 키값의 해시값을 구한 후 그 값에 속한 bucket에 값을 저장
    - set과 마찬가지로 순서가 없고 중복된 키 값은 허용되지 않는다.
- 예) 실제 데이터베이스에서 로드한 값을 딕셔너리로 변환해서 자주 사용

## Stack

- 기억포인트 - 접시쌓기, 브라우저에서 뒤로 가기
- LIFO(Last In First Out)
- 밑단에 어레이를 쓴다. 메모리를 곧바로 엑세스하므로 빠르다.
- Stackoverflow - 함수에서 함수를 호출하는데 정해진 메모리를 초과할 때 나는 에러 (대부분은 무한루프)

```python
 class Stack:
   def __init__(self):
     self._stack = []

   def push(self, data):
     self._stack.append(data)

   def pop(self):
     if len(self._stack) == 0:
       return None

     data = self._stack[-1]
     del self._stack[-1]

     return data

   def peek(self):
     if len(self._stack) == 0:
       return None

     data = self._stack[-1]

     return data
```

## Queue

- 큐는 스택의 반대
- FIFO(First In First Out)
- 기억포인드 - 맛집, 먼저 줄서는대로 먼저 나온다. 
- 프라이어티큐 - 우선순위가 있는 순서대로 예) os 멀티코어 실행, 예외처리

```python
class Queue:
    def __init__(self):
        self._queue = []

    def push(self, data):
        return self._queue.append(data)

    def pop(self)
        if len(self._queue) == 0:
            return None

        return self._queue.pop()

    def peek(self):
        if len(self._queue) == 0:
            return None

        return self[0]
```

## Tree
- 이진트리
    - 탐색속도 로그n회 (리스트는 n회)
    - tree -> bigOnotation ( logN ) 
    - cf) set ( 1 )

## Linked list
- 이전메모리가 다음 메모리를 참조
- 메모리 리사이징 문제 해결 가능
- 클래스로 표현하면서 많은 메모리를 소모


## Link

- [wecode](https://stackoverflow.com/c/wecode/questions/192)
