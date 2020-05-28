---
layout  : wiki
title   : data-structure 
summary : 
date    : 2020-03-12 16:13:09 +0900
updated : 2020-05-28 12:15:49 +0900
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
    - 요소를 자주 삭제하지 않아도 될 때

### Array 단점

- 삭제할 때 중간에 비면 안 되므로 순서가 바뀐다.
- 리사이징을 할 때도 중간에 차지한 메모리를 치울 수 없으므로 애초 생성 시 비교적 큰 메모리를 할당
- 이에 사이즈가 급격히 늘어날 확률이 있는 데이터는 Array에 적합하지 않음

## Tuple

- 리스트와 다르게 수정 불가능, 리스트보다 가볍고 적은 메모리 소모
- 보통 2~5개 소규모 요소를 저장하기 위해 사용
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
    - 셋의 총 길이 상관없이 해시 값 계산 후 해당 버킷만 확인하면 되므로 `0(1)`
    - 최상위 부모 객체의 주소가 해시값
    - 셋은 밑단에 어레이를 쓰고 있다.
    - 해시와 인덱스를 어떻게 연결?  나눈 나머지값을 인덱스로 사용
    - 중복이 나오면 '해시충돌'
    - 계속해서 충돌이 날 것 같으면 어레이 리사이징

## Dictionary

- 중복된 키는 저장 안 된다. 만일 중복된 키가 있으면 먼저 있던 키와 값을 대체한다.
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
- Stackoverflow - 함수에서 함수를 호출하는데 정해진 메모리를 초과할 때 나는 에러(대부분은 무한루프)
- 장점
    - 구조가 단순해서, 구현이 쉽다.
    - 데이터 저장/읽기 속도가 빠르다.
- 단점 (일반적인 스택 구현 시)
    - 데이터 최대 개수를 미리 정해야 한다.
        - 파이썬의 경우 재귀 함수는 1000번까지만 호출이 가능함
    -저장 공간의 낭비가 발생할 수 있음
        - 미리 최대 갯수만큼 저장 공간을 확보해야 하므로

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
- [Visualgo](https://visualgo.net/en/list) - 시연 웹사이트
- 참고: 어디에 큐가 많이 쓰일까?
    - 멀티 태스킹을 위한 프로세스 스케쥴링 방식을 구현하기 위해 많이 사용됨 (운영체제 참조)
    - 큐의 경우에는 장단점 보다는 (특별히 언급되는 장단점이 없음), 큐의 활용 예로 프로세스 스케쥴링 방식을 함께 이해해두는 것이 좋음

### queue 라이브러리 활용

- queue 라이브러리에는 다양한 큐 구조로 Queue(), LifoQueue(), PriorityQueue() 제공
    - Queue(): 일반적인 큐 자료 구조
    - LifoQueue(): 나중에 입력된 데이터가 먼저 출력되는 구조 (스택 구조라고 보면 됨)
    - PriorityQueue(): 데이터마다 우선순위를 넣어서, 우선순위가 높은 순으로 데이터 출력

#### Queue()

```python
import queue

data_queue = queue.Queue()

data_queue.put("funcoding")
data_queue.put(1)

data_queue.qsize()

data_queue.get()

data_queue.qsize()

data_queue.get()
```

#### LifoQueue()

```python
import queue
data_queue = queue.LifoQueue()

data_queue.put("funcoding")
data_queue.put(1)

data_queue.qsize()

data_queue.get()
```

#### PriorityQueue()

```python
import queue

data_queue = queue.PriorityQueue()

data_queue.put((10, "korea"))
data_queue.put((5, 1))
data_queue.put((15, "china"))

data_queue.qsize()

data_queue.get()
```

### enqueue, dequeue

```python
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

### 트리구조

- Node와 Branch를 이용해서, 사이클을 이루지 않도록 구성한 데이터 구조
- 이진 트리(Binary Tree) 형태 구조는 탐색(검색) 알고리즘 구현에 주로 사용됨

### 알아둘 용어

- Node : 트리에서 데이터를 저장하는 기본 요소(데이터와 다른 연결된 노드에 대한 브랜치 정보 포함)
- 레벨 : 최상위 노드를 Level 0으로 지정할 때 하위 Branch로 연결된 노드의 깊이
- Leaf Node(Terminal Node) : Child Node가 없는 노드

### 이진 트리와 이진 탐색 트리

- 이진트리 : 노드의 최대 브랜치가 2인 트리
- 이진 탐색 트리 - 이진 트리에 왼쪽 노드는 해당 노드보다 작은 값, 오른쪽 노드는 해당 노드보다 큰 값을 할당
    - 탐색속도 로그n회 (리스트는 n회)
    - tree -> bigOnotation ( logN ) 
    - cf) set ( 1 )
- [참고 이미지](https://blog.penjee.com/5-gifs-to-understand-binary-search-tree/#binary-search-tree-insertion-node)

#### 링크드 리스트로 이진 트리 구현

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
        
class NodeMgmt:
    def __init__(self, head):
        self.head = head
    
    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
                if self.current_node.left != None:  # 왼쪽 브랜치에 값이 있다면
                    self.current_node = self.current_node.left # 비교할 대상을 변경
                else:
                    self.current_node.left = Node(value)
                    break
            else:
                if self.current_node.right != None:
                    self.current_node = self.current_node.right
                else:
                    self.current_node.right = Node(value)
                    break
```

### 이진 탐색 트리

```python
class NodeMgmt:
    def __init__(self, head):
        self.head = head
    
    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
                if self.current_node.left != None:
                    self.current_node = self.current_node.left
                else:
                    self.current_node.left = Node(value)
                    break
            else:
                if self.current_node.right != None:
                    self.current_node = self.current_node.right
                else:
                    self.current_node.right = Node(value)
                    break
    
    def search(self, value):
        self.current_node = self.head
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.current_node = self.current_node.left
            else:
                self.current_node = self.current_node.right
        return False
        
head = Node(1)
BST = NodeMgmt(head)
BST.insert(2)
BST.insert(3)
BST.insert(0)
BST.insert(4)
BST.insert(8)

BST.search(-1)
```

### 이진 탐색 트리 삭제

#### 삭제 노드 탐색

```python
# def delete(self, value):
    searched = False
    self.current_node = self.head
    self.parent = self.head
    while self.current_node:
        if self.current_node.value == value:
            searched = True
            break
        elif value < self.current_node.value:
            self.parent = self.current_node
            self.current_node = self.current_node.left
        else:
            self.parent = self.current_node
            self.current_node = self.current_node.right
    
    if searched == False:
        return False
    
    ### 이후부터 Case들을 분리해서, 코드 작성
```

#### Case1. 삭제할 노드가 Leaf Node인 경우

<img width="714" alt="1" src="https://user-images.githubusercontent.com/48748376/81024025-13268780-8ead-11ea-85a0-c3e8c4538116.png">

```python
# self.current_node 가 삭제할 Node, self.parent는 삭제할 Node의 Parent Node인 상태
    if  self.current_node.left == None and self.current_node.right == None:
        if value < self.parent.value:
            self.parent.left = None
        else:
            self.parent.right = None
        del self.current_node
```

#### Case2: 삭제할 Node가 Child Node를 한 개 가지고 있을 경우

<img width="525" alt="2" src="https://user-images.githubusercontent.com/48748376/81024028-1752a500-8ead-11ea-9334-00144d1d35b9.png">

```python
    if self.current_node.left != None and self.current_node.right == None:
        if value < self.parent.value:
            self.parent.left = self.current_node.left
        else:
            self.parent.right = self.current_node.left
    elif self.current_node.left == None and self.current_node.right != None:
        if value < self.parent.value:
            self.parent.left = self.current_node.right
        else:
            self.parent.right = self.current_node.right
```

#### Case3-1: 삭제할 Node가 Child Node를 두 개 가지고 있을 경우 (삭제할 Node가 Parent Node 왼쪽에 있을 때)

- 기본 사용 가능 전략
    - 삭제할 Node의 오른쪽 자식 중, 가장 작은 값을 삭제할 Node의 Parent Node가 가리키도록 한다.
    - 삭제할 Node의 왼쪽 자식 중, 가장 큰 값을 삭제할 Node의 Parent Node가 가리키도록 한다.
- 기본 사용 가능 전략 중, 1번 전략을 사용하여 코드를 구현하기로 함
    - Case3-1-1: 삭제할 Node가 Parent Node의 왼쪽에 있고, 삭제할 Node의 오른쪽 자식 중, 가장 작은 값을 가진 Node의 Child Node가 없을 때
    - Case3-1-2: 삭제할 Node가 Parent Node의 왼쪽에 있고, 삭제할 Node의 오른쪽 자식 중, 가장 작은 값을 가진 Node의 오른쪽에 Child Node가 있을 때
가장 작은 값을 가진 Node의 Child Node가 왼쪽에 있을 경우는 없음, 왜냐하면 왼쪽 Node가 있다는 것은 해당 Node보다 더 작은 값을 가진 Node가 있다는 뜻이기 때문임

<img width="656" alt="3-1" src="https://user-images.githubusercontent.com/48748376/81024031-1883d200-8ead-11ea-9a82-91b84083190f.png">

```python
    if self.current_node.left != None and self.current_node.right != None: # case3
        if value < self.parent.value: # case3-1
            self.change_node = self.current_node.right
            self.change_node_parent = self.current_node.right
            while self.change_node.left != None:
                self.change_node_parent = self.change_node
                self.change_node = self.change_node.left
            if self.change_node.right != None:
                self.change_node_parent.left = self.change_node.right
            else:
                self.change_node_parent.left = None
            self.parent.left = self.change_node
            self.change_node.right = self.current_node.right
            self.change_node.left = self.change_node.left
```

#### Case3-2: 삭제할 Node가 Child Node를 두 개 가지고 있을 경우 (삭제할 Node가 Parent Node 오른쪽에 있을 때)

<img width="635" alt="3-2" src="https://user-images.githubusercontent.com/48748376/81024032-191c6880-8ead-11ea-82b1-1cfb18193fb4.png">

```python
        else:
            self.change_node = self.current_node.right
            self.change_node_parent = self.current_node.right
            while self.change_node.left != None:
                self.change_node_parent = self.change_node
                self.change_node = self.change_node.left
            if self.change_node.right != None:
                self.change_node_parent.left = self.change_node.right
            else:
                self.change_node_parent.left = None
            self.parent.right = self.change_node
            self.change_node.left = self.current_node.left
            self.change_node.right = self.current_node.right
```

#### 전체 코드

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

        
class NodeMgmt:
    def __init__(self, head):
        self.head = head
    
    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
                if self.current_node.left != None:
                    self.current_node = self.current_node.left
                else:
                    self.current_node.left = Node(value)
                    break
            else:
                if self.current_node.right != None:
                    self.current_node = self.current_node.right
                else:
                    self.current_node.right = Node(value)
                    break
    
    def search(self, value):
        self.current_node = self.head
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.current_node = self.current_node.left
            else:
                self.current_node = self.current_node.right
        return False        
    
    def delete(self, value):
        # 삭제할 노드 탐색
        searched = False
        self.current_node = self.head
        self.parent = self.head
        while self.current_node:
            if self.current_node.value == value:
                searched = True
                break
            elif value < self.current_node.value:
                self.parent = self.current_node
                self.current_node = self.current_node.left
            else:
                self.parent = self.current_node
                self.current_node = self.current_node.right

        if searched == False:
            return False    

        # case1
        if  self.current_node.left == None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = None
            else:
                self.parent.right = None
        
        # case2
        elif self.current_node.left != None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = self.current_node.left
            else:
                self.parent.right = self.current_node.left
        elif self.current_node.left == None and self.current_node.right != None:
            if value < self.parent.value:
                self.parent.left = self.current_node.right
            else:
                self.parent.right = self.current_node.right        
        
        # case 3
        elif self.current_node.left != None and self.current_node.right != None:
            # case3-1
            if value < self.parent.value:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left
                if self.change_node.right != None:
                    self.change_node_parent.left = self.change_node.right
                else:
                    self.change_node_parent.left = None
                self.parent.left = self.change_node
                self.change_node.right = self.current_node.right
                self.change_node.left = self.change_node.left
            # case 3-2
            else:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left
                if self.change_node.right != None:
                    self.change_node_parent.left = self.change_node.right
                else:
                    self.change_node_parent.left = None
                self.parent.right = self.change_node
                self.change_node.right = self.current_node.right
                self.change_node.left = self.current_node.left

        return True
```

## Linked list

- 이전메모리가 다음 메모리를 참조
- 메모리 리사이징 문제 해결 가능
- 클래스로 표현하면서 많은 메모리를 소모
    - 노드 : 데이터 저장 단위(데이터값, 포인터)로 구성
    - 포인터 : 각 노드 안에서, 다음이나 이전 노드와의 연결 정보를 가지고 있는 공간

### 장단점

- 장점
    - 미리 데이터 공간을 미리 할당하지 않아도 됨
- 단점
    - 연결을 위한 별도 데이터 공간이 필요하므로, 저장공간 효율이 높지 않음
    - 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느림
    - 중간 데이터 삭제시, 앞뒤 데이터의 연결을 재구성해야 하는 부가적인 작업 필요

### 노드 추가

```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
    
class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        
    def add(self, data):
        if self.head == '':
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
        
    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
```

### 노드 삭제

```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
    
class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        
    def add(self, data):
        if self.head == '':
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
        
    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
    
    def delete(self, data):
        if self.head == '':
            print ("해당 값을 가진 노드가 없습니다.")
            return
        
        if self.head.data == data:
            temp = self.head
            self.head = self.head.next
            del temp
        else:
            node = self.head
            while node.next:
                if node.next.data == data:
                    temp = node.next
                    node.next = node.next.next
                    del temp
                    return
                else:
                    node = node.next
```

### 더블 링크드 리스트

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.prev = prev
        self.data = data
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new

    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
```

```python
double_linked_list = NodeMgmt(0)
for data in range(1, 10):
    double_linked_list.insert(data)
double_linked_list.desc()
```

- 위 코드에서 노드 데이터가 특정 숫자인 노드 앞에 데이터를 추가하는 함수를 만들고, 테스트해보기
    - 더블 링크드 리스트의 tail 에서부터 뒤로 이동하며, 특정 숫자인 노드를 찾는 방식으로 함수를 구현하기
    - 테스트: 임의로 0 ~ 9까지 데이터를 링크드 리스트에 넣어보고, 데이터 값이 2인 노드 앞에 1.5 데이터 값을 가진 노드를 추가해보기

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.prev = prev
        self.data = data
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new

    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
    
    def search_from_head(self, data):
        if self.head == None:
            return False
    
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        return False
    
    def search_from_tail(self, data):
        if self.head == None:
            return False
    
        node = self.tail
        while node:
            if node.data == data:
                return node
            else:
                node = node.prev
        return False
    
    def insert_before(self, data, before_data):
        if self.head == None:
            self.head = Node(data)
            return True
        else:
            node = self.tail
            while node.data != before_data:
                node = node.prev
                if node == None:
                    return False
            new = Node(data)
            before_new = node.prev
            before_new.next = new
            new.prev = before_new
            new.next = node
            node.prev = new
            return True
```

```python
double_linked_list = NodeMgmt(0)
for data in range(1, 10):
    double_linked_list.insert(data)
double_linked_list.desc()
```

- 위 코드에서 노드 데이터가 특정 숫자인 노드 뒤에 데이터를 추가하는 함수를 만들고, 테스트해보기
    - 더블 링크드 리스트의 head 에서부터 다음으로 이동하며, 특정 숫자인 노드를 찾는 방식으로 함수를 구현하기
    - 테스트: 임의로 0 ~ 9까지 데이터를 링크드 리스트에 넣어보고, 데이터 값이 1인 노드 다음에 1.7 데이터 값을 가진 노드를 추가해보기

```python
class Node:
    def __init__(self, data, prev=None, next=None):
        self.prev = prev
        self.data = data
        self.next = next

class NodeMgmt:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head
    
    def insert_before(self, data, before_data):
        if self.head == None:
            self.head = Node(data)
            return True            
        else:
            node = self.tail
            while node.data != before_data:
                node = node.prev
                if node == None:
                    return False
            new = Node(data)
            before_new = node.prev
            before_new.next = new
            new.next = node
            return True

    def insert_after(self, data, after_data):
        if self.head == None:
            self.head = Node(data)
            return True            
        else:
            node = self.head
            while node.data != after_data:
                node = node.next
                if node == None:
                    return False
            new = Node(data)
            after_new = node.next
            new.next = after_new
            new.prev = node
            node.next = new
            if new.next == None:
                self.tail = new
            return True

    def insert(self, data):
        if self.head == None:
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new

    def desc(self):
        node = self.head
        while node:
            print (node.data)
            node = node.next
```

```python
node_mgmt = NodeMgmt(0)
for data in range(1, 10):
    node_mgmt.insert(data)

node_mgmt.desc()

node_mgmt.insert_after(1.5, 1)
node_mgmt.desc()
```

## Hash Table

### 해시 구조

- Hash Table: 키에 데이터를 저장하는 데이터 구조
    - 키를 통해 바로 데이터를 받아올 수 있으므로 속도가 획기적으로 빨라짐
    - 예: 파이썬 딕셔너리 타입의 해시 테이블
    - 보통 배열로 미리 Hash Table 사이즈만큼 생성 후에 사용(공간과 탐색 시간을 맞바꾸는 기법)
    - 단, 파이썬에서는 해시를 별도 구현할 이유가 없음 - 딕셔너리 타입을 사용하면 됨

### 알아둘 용어

- 해시 : 임의 값을 고정 길이로 변환하는 것
- 해시 테이블 : 키 값의 연산에 의해 직접 접근이 가능한 데이터 구조
- 해싱 함수 : 키에 대해 산술 연산을 이용해 데이터 위치를 찾을 수 있는 함수
- 해시 값 또는 해시 주소 : 키를 해싱 함수로 연산해서, 해시 값을 알아내고, 이를 기반으로 해시 테이블에서 해당 키에 대한 데이터 위치를 일관성있게 찾을 수 있음
- 슬롯 : 한 개의 데이터를 저장할 수 있는 공간
- 저장할 데이터에 대해 키를 추출할 수 있는 별도 함수도 존재할 수 있음

### 간단한 해시 예

```python
hash_table = list([i for i in range(10)])

def hash_func(key): # 다양한 해시함수 기법들이 있다. 이 케이스는 간단한 Division 방법
    return key % 5

data1 = 'Andy'
print(ord(data1[0]), hash_func(ord(data1[0]))) 


def storage_data(data, value):
    key = ord(data[0])
    hash_address = hash_func(key) # 키가 문자열 값의 아스키 코드, 값은 아스키 코드를 5로 나눈 나머지
    hash_table[hash_address] = value # 해시주소를 키로 가진 값들의 집합이 해시 테이블

storage_data('Andy', '01055553333')

def get_data(data):
    key = ord(data[0])
    hash_address = hash_func(key)
    return hash_table[hash_address]

get_data('Andy')
```

### 해시 테이블 장단점과 주요 용도

- 장점
    - 데이터 저장/읽기 속도가 빠르다.
    - 키에 대한 데이터 중복 확인이 쉽다.
- 단점
    - 일반적으로 저장공간이 많이 필요하다.
    - 여러 키에 해당하는 주소가 동일한 경우 충돌을 해결하기 위한 별도 자료구조가 필요하다.
- 주요 용도
    - 검색이 많이 필요한 경우
    - 저장, 삭제, 읽기가 빈번한 경우
    - 캐시 구현 시(중복 확인이 쉽기 때문)

### 충돌 해결 알고리즘

- 키값이 중복되는 등의 충돌 가능성이 높다.

#### Chaining 기법

- 개방 해싱 또는 Open Hashing 기법 중 하나: 해시 테이블 저장공간 외의 공간을 활용하는 기법
- 링크드 리스트를 이용해 데이터를 추가로 연결하는 기법
- 예시에서는 링크드 리스트가 아닌 배열로 구현

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                hash_table[hash_address][index][1] = value
                return
        hash_table[hash_address].append([index_key, value])
    else:
        hash_table[hash_address] = [[index_key, value]]
    
def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)

    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                return hash_table[hash_address][index][1]
        return None
    else:
        return None


save_data('Dd', '1201023010')
save_data('Data', '3301023010')
read_data('Dd')

print(hash_table)
```

#### Linear Probing 기법

- 폐쇄 해싱 또는 클로징 해싱 기법 중 하나 - 해시 테이블 저장공간 바깥이 아닌 내부에서 충돌 문제를 해결하는 기법
- 충돌이 일어나면, 해당 Hash address의 다음 address부터 맨 처음 나오는 빈 공간에 저장하는 기법
    - 저장공간 활용도를 높일 수 있다.

```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                hash_table[index] = [index_key, value]
                return
            elif hash_table[index][0] == index_key:
                hash_table[index][1] = value
                return
    else:
        hash_table[hash_address] = [index_key, value]

def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                return None
            elif hash_table[index][0] == index_key:
                return hash_table[index][1]
    else:
        return None
        
print (hash('dk') % 8)
print (hash('da') % 8)
print (hash('dc') % 8)

save_data('dk', '01200123123')
save_data('da', '3333333333')
read_data('dc')
```

### 빈번한 충돌을 개선하는 방법

#### 해시 함수를 재정의 또는 해시 테이블 저장공간을 확대

```python
hash_table = list([None for i in range(16)])

def hash_function(key):
    return key % 16
```

#### SHA(Secure Hash Algorithm)

- 어떤 데이터도 유일한 고정값을 리턴

##### SHA-1

```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha1()
hash_object.updade(data)
hex_dig = hash_objects.hexdigest()
print(hex_dig)
```

##### SHA-256

```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha256()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print(hex_dig)
```

```python
import hashlib

hash_table = list([0 for i in range(8)])

def get_key(data):
        hash_object = hashlib.sha256()
        hash_object.update(data.encode())
        hex_dig = hash_object.hexdigest()
        return int(hex_dig, 16)
```

### 시간 복잡도

- 일반적인 경우(충돌이 없으면) O(1)
- 최악의 경우(모두 충돌이 발생하면) O(n)
- 검색 예
    - 배열에 데이터를 저장하고, 검색할 떄 O(n) 
    - 데이터 저장곤간을 가진 해시 테이블을 저장하고 난 다음 검색할 때 O(1)

## Heap

### 힙이란?

- 데이터에서 최댓값과 최솟값을 빠르게 찾기 위해 고안된 완전 이진 트리(Complete Binary Tree)
    - 완전 이진 트리: 노드를 삽입할 때 최하단 왼쪽 노드부터 차례대로 삽입하는 트리
- 힙을 사용하는 이유
    - 배열에서 최댓값과 최솟값을 찾으려면 O(n)이 걸림
    - 이에 반해, 힙에서는 O(logn)이 걸림
    - 우선순위 큐와 같이 최댓값 또는 최솟값을 빠르게 찾아야 하는 자료구조에서 활용

### 힙 구조

- 최댓값을 구하는 구조(최대 힙, Max Heap)와 최솟값을 구하는 구조(최소 힙, Min Heap)로 나뉜다.
- 힙의 2가지 조건
    - 각 노드 값은 해당 노드의 자식 노드가 가진 값보다 크거나 같다.(최대 힙)
    - 완전 이진 트리 형태를 가짐
- 힙과 이진 탐색 트리 공통점과 차이
    - 공통점 - 힙과 이진 탐색 트리는 모두 이진 트리
    - 차이점
        - 힙은 각 노드의 값이 자식 노드보다 크거나 같음(최대 힙)
        - 이진 탐색 트리는 왼쪽 자식 노드의 값이 가장 작고, 그 다음 부모 노드, 그 다음 오른쪽 자식 노드 값이 가장 크다
        - 힙은 이진 탐색 트리의 조건인 자식 노드에서 작은 값은 왼쪽, 큰 값은 오른쪽이라는 조건은 없다
    - 이진 탐색 트리는 탐색을 위한 구조, 힙은 최대/최솟값 검색을 위한 구조

<img width="765" alt="1" src="https://user-images.githubusercontent.com/48748376/81132722-5b67a780-8f8a-11ea-8ca7-961cd2d75ba2.png">

### 힙 동작

#### 데이터 삽입(기본 동작)

- 삽입할 노드는 왼쪽 하단부 노드부터 채워지는 형태

<img width="770" alt="2" src="https://user-images.githubusercontent.com/48748376/81132727-5efb2e80-8f8a-11ea-9a26-48a05faab3e9.png">

#### 힙에 데이터 삽입(삽입할 데이터가 힙의 데이터보다 클 경우)

- 채워진 노드 위치에서 부모 노드보다 값이 클 경우, 부모 노드와 위치를 바꿔주는 작업을 반복(Swap)

<img width="764" alt="3" src="https://user-images.githubusercontent.com/48748376/81132728-5f93c500-8f8a-11ea-901a-310fb9cf591f.png">

#### 힙의 데이터 삭제(Max Heap의 예)

- 보통 삭제는 최상단 노드(루트 노드)를 삭제하는 것이 일반적
    - 루트 노드에서 최댓값, 최솟값을 바로 꺼내 쓰는 것이 힙의 용도
- 상단의 데이터 삭제 시, 최하단부 왼쪽에 위치한 노드(일반적으로 가장 마지막에 추가한 노드)를 루트 노드로 이동
- 루트 노드의 값이 차일드 노드보다 작을 경우, 루트 노드의 차일드 노드 중 가장 큰 값을 가진 토드와 루트 노드 위치를 바꿔주는 작업을 반복(Swap)

<img width="809" alt="4" src="https://user-images.githubusercontent.com/48748376/81132729-60c4f200-8f8a-11ea-8954-d8d81b0a96b7.png">

#### 힙 구현

- 힙과 배열

- 일반적으로 힙 구현 시 배열 자료구조를 활용, 루트 노드 인덱스 1로 지정(힙 구현 편의를 위해)
- 부모 노드 인덱스 = 자식 노드 인덱스 // 2
- 왼쪽 자식 노드 인덱스 = 부모 노드 인덱스 * 2
- 오른쪽 자식 노드 인덱스 = 부모 노드 인덱스 * 2 + 1

<img width="415" alt="5" src="https://user-images.githubusercontent.com/48748376/81132732-615d8880-8f8a-11ea-918a-42ead17502c9.png">

```python
# 10 노드의 부모 노드 인덱스
2 // 2 -> 1

# 15 노드의 왼쪽 자식 노드 인덱스
1 * 2 -> 2

# 15 노드의 오른쪽 자식 인덱스
2 * 2 + 1 -> 5
```

#### 힙에 데이터 삽입 구현(Max Heap)

##### 힙 클래스 구현1

```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        self.heap_array.append(data)
        
heap = Heap(1)
headp.heap_array
-> [None, 1]
```

##### 힙 클래스 구현2 - insert1

- 인덱스 1부터 시작
- 그냥 append로 뒤에 붙여주면 끝

<img width="780" alt="6" src="https://user-images.githubusercontent.com/48748376/81132734-615d8880-8f8a-11ea-8acf-34b8cd5baa9b.png">

```python
    def insert(self, data):
        if len(self.heap_array) == 0:
            self.heap_array.append(None)
            self.heap_array.append(data)
            return True
            
        self.heap_array.append(data)
        return True
```

##### 힙 클래스 구현3 - insert2

- 삽입한 노드가 부모 노드의 값보다 클 경우, 부모 노드와 삽입한 노드 위치 바꿈
- 삽입한 노드가 루트 노드가 되거나, 부모 노드보다 값이 작거나 같을 경우까지 반복

- 부모 노드 인덱스 = 자식 노드 인덱스 // 2
- 왼쪽 자식 노드 인덱스 = 부모 노드 인덱스 * 2
- 오른쪽 자식 노드 인덱스 = 부모 노드 인덱스 * 2 + 1

<img width="782" alt="7" src="https://user-images.githubusercontent.com/48748376/81132737-61f61f00-8f8a-11ea-8d52-182ad2e17b24.png">

```python
heap = Heap(15)
heap.insert(10)
heap.insert(8)
heap.insert(5)
heap.insert(4)
heap.insert(20)
heap.heap_array
-> [None, 20, 10, 15, 5, 4, 8]
```

```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        slef.heap_array.append(data)
        
    def move_up(self, inserted_idx):
        if inserted_idx <= 1:
            return False
        
        parent_idx = inserted_idx // 2
        if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
            return True
        else:
            return False
            
    def insert(self, data):
        if len(self.heap_array) == 0:
            self.heap_array.append(None)
            self.heap_array.append(data)
            return True
        
        self.heap_array.append(data)
        
        inserted_idx = len(self.heap_array) - 1
        
        while self.move_up(inserted_idx): # move_up 메소드가 True인 동안 순회
            parent_idx = inserted_idx // 2 # 부모 노드와 자식 노드를 바꿈
            self.heap_array[inserted_idx], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_idx]
            inserted_idx = parent_idx
        
        return True
        
```

##### 힙에 데이터 삭제 구현(Max Heap)

...



## Link

- [wecode](https://stackoverflow.com/c/wecode/questions/192)
- [잔재미코딩](https://www.fun-coding.org/Chapter05-queue-live.html)
