---
layout  : wiki
title   : data-structure 
summary : 
date    : 2020-03-12 16:13:09 +0900
updated : 2020-04-22 12:14:46 +0900
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
- 장점
    - 구조가 단순해서, 구현이 쉽다.
    - 데이터 저장/읽기 속도가 빠르다.
- 단점 (일반적인 스택 구현시)
    - 데이터 최대 갯수를 미리 정해야 한다.
        - 파이썬의 경우 재귀 함수는 1000번까지만 호출이 가능함
    -저장 공간의 낭비가 발생할 수 있음
        - 미리 최대 갯수만큼 저장 공간을 확보해야 함


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

```python
yellow_el = [1, 2, 3, 4, 5, 6]
yellow = Stack()
print(yellow._stack)
for i in yellow_el:
  yellow.push(i)
print(yellow._stack)
```

## Queue

- 큐는 스택의 반대
- FIFO(First In First Out)
- 기억포인드 - 맛집, 먼저 줄서는대로 먼저 나온다. 
- 프라이어티큐 - 우선순위가 있는 순서대로 예) os 멀티코어 실행, 예외처리
- [Visualgo](https://visualgo.net/en/list) - 시연 웹사이트
- 참고: 어디에 큐가 많이 쓰일까?
    - 멀티 태스킹을 위한 프로세스 스케쥴링 방식을 구현하기 위해 많이 사용됨 (운영체제 참조)
    - 큐의 경우에는 장단점 보다는 (특별히 언급되는 장단점이 없음), 큐의 활용 예로 프로세스 스케쥴링 방식을 함께 이해해두는 것이 좋음

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

### 용어

- Enqueue: 큐에 데이터를 넣는 기능
- Dequeue: 큐에서 데이터를 꺼내는 기능

### 파이썬 라이브러리

- Queue(): 가장 일반적인 큐 자료 구조
- LifoQueue(): 나중에 입력된 데이터가 먼저 출력되는 구조 (스택 구조라고 보면 됨)
- PriorityQueue(): 데이터마다 우선순위를 넣어서, 우선순위가 높은 순으로 데이터 출

```python
import queue

data_queue = queue.Queue()

ata_queue.put("funcoding")
data_queue.put(1)

data_queue.qsize()
data_queue.get()
data_queue.qsize()
```

```python
# LifoQueue()로 큐 만들기 (LIFO(Last-In, First-Out))

import queue

data_queue = queue.LifoQueue()

# PriorityQueue()로 큐 만들기

import queue

data_queue = queue.PriorityQueue()

data_queue.put((10, "korea"))
data_queue.put((5, 1))
data_queue.put((15, "china"))

# 리스트 변수로 큐를 다루는 enqueue, dequeue 기능 구현해보기

queue_list = list()

def enqueue(data):
    queue_list.append(data)
    
def dequeue():
    data = queue_list[0]
    del queue_list[0]
    return data
    
for index in range(10):
    enqueue(index)

len(queue_list)
dequeue()
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
- [잔재미코딩](https://www.fun-coding.org/Chapter05-queue-live.html)
