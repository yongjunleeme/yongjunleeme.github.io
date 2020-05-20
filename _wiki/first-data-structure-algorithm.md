---
layout  : wiki
title   : first-data-structure-algorithm
summary : 
date    : 2020-03-31 21:15:47 +0900
updated : 2020-05-20 17:04:27 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 선형 배열

- 정렬된 리스트에 원소 삽입

```python
def solution(L, x):
    if x > max(L): 
        L.append(x)
    elif x < min(L): 
        L.insert(0, x)
    else:
        for i in range(len(L)):
            if L[i] >= x:
                t = i
                break
        L.insert(t, x) 

    return L

def solution(L, x):
    for idx, num in enumerate(L):
        if num > x:
            L.insert(idx,x)
            break

        if L[-1] < x:
            L.append(x)
        else:
            pass

     return L
```

- 리스트에서 원소 찾아내기

```python
def solution(L, x):
    answer = []
    if x not in L:
        answer.append(-1)
    else:
        for i in range(len(L)):
            if L[i] == x:
                answer.append(i)        
    return answer

def solution(L, x):
    answer = []
    if x not in L:
        answer.append(-1)
    else:
        for i, v in enumerate(L):
            if v == x:
                answer.append(i)
    return answer
```

## 탐색

```python
def solution(L, x):
    lower = 0
    upper = len(L) - 1
    idx = -1
    while lower <= upper:
        middle = (lower + upper) // 2
        if L[middle] == x:
            idx = middle
            break
        elif L[middle] < x:
            lower = middle + 1
        else:
            upper = middle - 1
    return idx
```

### 탐색 (search) 이란?
복수의 원소로 이루어진 데이터에서 특정 원소를 찾아내는 작업입니다. 탐색에도 여러 가지 방법이 있지만, 아주 기본적인 두 가지를 소개합니다.

선형 탐색 (linear search), 또는 순차 탐색 (sequential search): 순차적으로 모든 요소들을 탐색하여 원하는 값을 찾아냅니다. 배열의 길이에 비례하는 시간이 걸리므로, 최악의 경우에는 배열에 있는 모든 원소를 다 검사해야 할 수 있습니다. (이런 일은 어떤 원소를 찾으려 할 때 벌어질까요?)

이진 탐색 (binary search): 탐색하려는 배열이 이미 정렬되어 있는 경우에만 적용할 수 있습니다. 배열의 가운데 원소와 찾으려 하는 값을 비교하면, (크기 순으로 정렬되어 있다는 성질을 이용하면) 왼쪽에 있을지 오른쪽에 있을지를 알 수 있습니다 (만약 있긴 있다면). 그러면, 적어도 반대쪽에 없는 것은 확실하므로, 배열의 반을 탐색하지 않고 버릴 수 있습니다. 이 과정을 반복하면 원하는 값을 찾아낼 수 있습니다. 한 번에 절반씩 배열을 잘라나간다면...몇 번이나 이 과정을 반복하게 될까요? (약간의 수학이네요?)

그래서 이진 탐색이 선형 탐색보다 빠른 방법이기는 합니다. 그러나, 뭔가를 찾으려 한다고 할 때, 항상 이진 탐색 방법을 적용하는 것이 답인가요? 그러려면 우선 배열을 정렬해야 한다던데, 크기 순으로 정렬하는 것은 금방 되나요? 한 번만 탐색하고 말 거라면, 굳이 크기 순으로 늘어놓느라 시간을 소모하는 것보다 한번씩 다 뒤지는 것이 낫지 않나요? (물론입니다.)

- 이진 탐색 O(log n)

- sorted()
    - 내장 함수
    - 정렬된 새로운 리스트 얻어냄(오름차순)

```python
>>> sorted([5, 2, 3, 1, 4])
[1, 2, 3, 4, 5]
```

- sort()
    - 리스트의 메서드
    - 해당 리스트를 정렬함

```python
>>> a = [5, 2, 3, 1, 4]
>>> a.sort()
>>> a
[1, 2, 3, 4, 5]
```

- reverse = True 정렬 순서 반대로

```python
L2 = sorted(L, reverse = True)
```

- 길이순

```python
L = ['abcd', 'xyz', 'spam']
sorted(L, key=lambda x: len(x))
```

```python
L = [{'name': 'John', 'score': 83},.. ]

L.sort(key = lambda x:x['name'])
# 알파벳 순으로 정렬
```

## 재귀

### 설명

알고리즘들 중에는, 재귀 알고리즘 (recursive algorithm) 이라고 불리는 것들이 있습니다. 이것은 알고리즘의 이름이 아니라 성질입니다. 주어진 문제가 있을 때, 이것을 같은 종류의 보다 쉬운 문제의 답을 이용해서 풀 수 있는 성질을 이용해서, 같은 알고리즘을 반복적으로 적용함으로써 풀어내는 방법입니다.

예를 들면, 1 부터 n 까지 모든 자연수의 합을 구하는 문제 (sum(n))는, 1 부터 n - 1 까지의 모든 자연수의 합을 구하는 문제 (sum(n - 1))를 풀고, 여기에 n 을 더해서 그 답을 찾을 수 있습니다.

```python
sum(n) = sum(n - 1) + n
```

### 복잡도

- O(n)
- recusive version vs iterative version
- 복잡도는 같지만 효율적 측면에서 recursive가 떨어짐. 함수 호출 등의 과정 때문?


### 예제

- n!

```python
def what(n):
    if n <= 1:
        return 1
    else:
        return n * what(n - 1)
```

- fibonacci

```python
# recursive version
def solution(x):
    if x == 0:
        return 0;
    elif x == 1:
        return 1;
    elif x < 0:
        return -1;
    else:
        return solution(x-1) + solution(x-2)

# iterative version
def solution(x):
    answer = 0
    fa = 0
    fb = 1
    while x > 0:
        x -= 1
        fa, fb = fb, fa+fb
        answer = fa
    return answer
```

## 재귀 응용

### 설명

문제의 종류에 따라서는 재귀 알고리즘을 적용함으로써 알고리즘을 간단하고 이해하기 쉽게 서술할 수 있다는 장점이 있습니다. 그런데, 문제를 풀어 내는 데 걸리는 시간 측면에서는 어떨까요?

하나의 재귀 알고리즘이 주어질 때, 이것을 재귀적이지 않은 (non-recursive) 방법으로 동일하게 풀어내는 알고리즘이 존재한다는 것을 수학적으로 증명할 수 있습니다. 보통은 순환문 (loop) 을 이용해서 정해진 연산을 반복함으로써 문제의 답을 구하는데, 따라서 반복적 (iterative) 알고리즘이라고 부르기도 합니다. 일반적으로, 주어진 문제에 대해서 반복적인 알고리즘이 재귀적인 알고리즘보다 문제 풀이의 (시간적) 효율이 높습니다.

그럼에도 불구하고, 재귀 알고리즘이 가지는 특성 때문에 트리 (tree) 와 같은 - 제 17 강에서 등장하게 됩니다 - 자료 구조를 이용하는 알고리즘에는 매우 직관적으로 적용할 수 있는 경우가 많습니다. 이 강의에서는 이진 탐색 (binary search) 알고리즘을 재귀적으로 구현하는 빈 칸 채우기 코딩 연습문제를 풀어보기로 합시다.

### 예제

- 조합의 수 계산
    - n개의 서로 다른 원소에서 m개를 택하는 경우의 수

```python
from math import factorial as f

def combi(n, m):
    return f(n) / (f(m) * f(n - m))
```

```python
def combi(n, m):
    if n == m:
        return 1
    elif m == 0:
        return 1
    else:
        return combi(n - 1, m) + combi(n - 1, m - 1)

```

- 반복문보다 효율이 떨어지는 재귀. 그러나 사람이 직관적으로 이해하므로 쓸모 있을 떄가 있다.
    - 하노이의 탑
    - 트리구조


- 재귀적 이진탐색

```python
def solution(L, x, l, u):
    if l > u:
        return -1
    mid = (l + u) // 2
    if x == L[mid]:
        return mid
    elif x < L[mid]
        return solution(L, x, l, mid - 1)
    else:
        return solution(L, x, mid + 1, u)
```

## 알고리즘의 복잡도

### 설명

- 시간 복잡도
    - 문제의 크기와 이를 해결하는 데 걸리는 시간 사이의 관계
- 공간 복잡도
    - 문제의 크기와 이를 해결하는 데 필요한 메모리 공간 사이의 관계

- Big-O Notation
    - 점근 표기법(asymptotic notation)의 하나
    - 상수는 중요하지 않음
    - 어떤 함수의 증가 양사을 다른 함수와의 비교로 표현(알고리즘의 복잡도를 표현할 때 흔히 쓰임)
    - 예) 선형 시간 알고리즘 - O(n) 
    - 로그 시간 알고리즘 - O(log n)
        - n개의 크기 순으로 정렬된 수에서 특정 값을 찾기 위해 이진 탐색 알고리즘을 적용
    - 이차 시간 알고리즘 - O(n제곱)
        - 삽입 정렬
    - 병합 정렬 - O(nlog n) (merge sort)

## 연결 리스트

### 설명

- 장점
    - 삽입, 삭제, 합치기 유연
- 단점
    - 메모리 소모 크다
    - k번째 찾아가는 데 배열보다 오래 걸린다

- Linked List Node 구성
    - Data
    - Link(next)
        - 노드 내 데이터는 다른 구조로 이뤄질 수 있음 예) 문제열, 레코드, 다른 연결 리스트 등

- 기본적 연결 리스트
    - Head (노드의 처음 값)
    - Tail (노드의 마지막 값)
    - of nodes: 3 (노드수)

### 시간 복잡도

- 특정 원소 지칭 - 배열 O(1), 연결 리스트 O(n)


### 예제

#### 순회

```python
class Node:
    def __init__(self, item):
        self.data = item
        self.next = None

class LinkedList:  # 연결 리스트 요소의 맨 앞이 헤드, 맨 끝은 테일
    def __init__(self):
        self.nodeCount = 0
        self.head = None
        self.tail = None

    def getAt(self, pos): # pos번째 노드를 찾아서 리턴
        if pos < 1 or pos > self.nodeCount:
            return Node
        i = 1 # 인덱스 1부터 시작

        curr = self.head
        while i < pos:
            curr = curr.next
            i += 1
        return curr
    
    def traverse(self):
        answer = []
        curr = self.head
        while curr != None:
            answer.append(curr.data)
            curr = curr.next
        return answer
```

#### 삽입

- 원소 삽입
    - pos가 가리키는 위치에 (1 <= pos <= nodeCount + 1)
    - newNode를 삽입하고 성공/실패에 따라 True/False 리턴
    - 복잡도
        - 맨 앞 삽입: O(1)
        - 중간 삽입: O(n)
        - 맨 끝 삽입 O(1) - Tail 덕분에
- 두 리스트의 연결
    - concat

```python
...
    def insertAt(self, pos, newNode):
        if pos < 1 or pos > self.nodeCount + 1:
            return False

        if pos == 1:
            newNode.next = self.head
            self.head = newNode

        else:
            if pos == self.nodeCount + 1:
                prev = self.tail
            else:
                prev = self.getAt(pos - 1)
            newNode.next = prev.next
            prev.next = newNode

        if pos == self.nodeCount + 1:
            self.tail = newNode

        self.nodeCount += 1
        return True


    def getLength(self):
        return self.nodeCount


    def traverse(self):
        result = []
        curr = self.head
        while curr is not None:
            result.append(curr.data)
            curr = curr.next
        return result


    def concat(self, L):
        self.tail.next = L.head
        if L.tail:
            self.tail = L.tail
        self.nodeCount += L.nodeCount
```

#### 삭제

- 원소의 삭제
    - pos가 가리키는 위치의 (1 <= pos <= nodeCount)
    - node를 삭제하고
    - 그 node의 데이터를 리턴
    - 복잡도
        - 맨 앞 삭제: O(1)
        - 중간 삭제: O(n)
        - 맨 끝 삭제: O(n)

```python
...
    def popAt(self, pos):
        if 1 > pos or pos > self.nodeCount:
            raise IndexError
        
        curr = self.getAt(pos)

        if pos == 1 and pos == self.nodeCount:
            self.head = None
            self.tail = None
        elif pos == 1:
            self.head = self.getAt(pos + 1)
        else:
            prev = self.getAt(pos - 1)
            
            if self.nodeCount != pos:
                prev.next = self.getAt(pos + 1)
            else:
                prev.next = None
                self.tail = prev
        
        self.nodeCount = self.nodeCount - 1
        return curr.data
...
```

#### insertAfter, insertAt

- 삽입과 삭제 유연 장점 설려서
    - 포지션을 수가 아니라 노드로, 주어진 노드 뒤에 삽입
        - 맨 앞에 dummy node 추가
            - `self.head = Node(None)` 
        - index를 1이 아닌 0부터 시작

```python
class Node: 
    def __init__(self, item):
        self.data = item
        self.next = None 

class LinkedList: 
    def __init__(self):
        self.nodeCount = 0
        self.head = Node(None)
        self.tail = None
        self.head.next = self.tail

    def traverse(self):
        result = []
        curr = self.head
        while curr.next:
            curr = curr.next
            result.append(curr.data)
        return result 

    def getAt(self, pos):
        if pos < 0 or pos > self.nodeCount:
            return None 
        i = 0
        curr = self.head
        while i < pos:
            curr = curr.next
            i += 1 
        return curr 

    def insertAfter(self, prev, newNode):
        newNode.next = prev.next
        if prev.next is None:
            self.tail = newNode
        prev.next = newNode
        self.nodeCount += 1
        return True 

    def insertAt(self, pos, newNode):
        if pos < 1 or pos > self.nodeCount + 1:
            return False 
        if pos != 1 and pos == self.nodeCount + 1:
            prev = self.tail
        else:
            prev = self.getAt(pos - 1)
        return self.insertAfter(prev, newNode) 

    def popAfter(self, prev):
        if prev == self.tail:
            return None
        curr = prev.next
        prev.next = curr.next
        if curr.next == None:
            self.tail = prev
        self.nodeCount -= 1
        return curr.data 

    def popAt(self, pos):
        if pos < 1 or pos > self.nodeCount:
            raise IndexError
        return self.popAfter(self.getAt(pos-1))        

def solution(x):
    return 0 
```

#### Doubly Linked List

- 앞으로뿐만 아니라 뒤로도 진행 가능
    - Node 구조 확장
    - 리스트 처음과 끝에 dummy node 추가

```python
class Node:

    def __init__(self, item):
        self.data = item
        self.prev = None
        self.next = None


class DoublyLinkedList:

    def __init__(self):
        self.nodeCount = 0
        self.head = Node(None)
        self.tail = Node(None)
        self.head.prev = None
        self.head.next = self.tail
        self.tail.prev = self.head
        self.tail.next = None


    def __repr__(self):
        if self.nodeCount == 0:
            return 'LinkedList: empty'

        s = ''
        curr = self.head
        while curr.next.next:
            curr = curr.next
            s += repr(curr.data)
            if curr.next.next is not None:
                s += ' -> '
        return s


    def getLength(self):
        return self.nodeCount


    def traverse(self):
        result = []
        curr = self.head
        while curr.next.next:
            curr = curr.next
            result.append(curr.data)
        return result


    def getAt(self, pos):
        if pos < 0 or pos > self.nodeCount:
            return None

        if pos > self.nodeCount // 2:
            i = 0
            curr = self.tail
            while i < self.nodeCount - pos + 1:
                curr = curr.prev
                i += 1
        else:
            i = 0
            curr = self.head
            while i < pos:
                curr = curr.next
                i += 1

        return curr


    def insertAfter(self, prev, newNode):
        next = prev.next
        newNode.prev = prev
        newNode.next = next
        prev.next = newNode
        next.prev = newNode
        self.nodeCount += 1
        return True


    def insertAt(self, pos, newNode):
        if pos < 1 or pos > self.nodeCount + 1:
            return False

        prev = self.getAt(pos - 1)
        return self.insertAfter(prev, newNode) 
```

## Stack

### 배열 기반

```python
class ArrayStack:

	def __init__(self):
		self.data = []

	def size(self):
		return len(self.data)

	def isEmpty(self):
		return self.size() == 0

	def push(self, item):
		self.data.append(item)

	def pop(self):
		return self.data.pop()

	def peek(self):
		return self.data[-1]
```

### 연결리스트 기반

```python
from doublylinkedlist import Node
from doublylinkedlist import DoublyLinkedList

class LinkedListStack:

	def __init__(self):
		self.data = DoublyLinkedList()

	def size(self):
		return self.data.getLength()

	def isEmpty(self):
		return self.size() == 0

	def push(self, item):
		node = Node(item)
		self.data.insertAt(self.size() + 1, node)

	def pop(self):
		return self.data.popAt(self.size())

	def peek(self):
		return self.data.getAt(self.size()).data
```

### 예제

#### 


## Link 

- [어서와, 자료구조와 알고리즘은 처음이지?](https://programmers.co.kr/learn/courses/57)

