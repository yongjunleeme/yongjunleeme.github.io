---
layout  : wiki
title   : first-data-structure-algorithm
summary : 
date    : 2020-03-31 21:15:47 +0900
updated : 2020-04-11 15:28:19 +0900
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

데이터 원소들을 순서를 지어 늘어놓는다는 점에서 연결 리스트 (linked list) 는 선형 배열 (linear array) 과 비슷한 면이 있지만, 데이터 원소들을 늘어놓는 방식에서 이 두 가지는 큰 차이가 있습니다. 구체적으로는, 선형 배열이 번호가 붙여진 칸에 원소들을 채워넣는 방식이라고 한다면, 연결 리스트는 각 원소들을 줄줄이 엮어서 관리하는 방식입니다. 그렇다면, 선형 배열에 비해서 연결 리스트가 가지는 이점은 무엇일까요?

연결 리스트에서는 원소들이 링크 (link) 라고 부르는 고리로 연결되어 있으므로, 가운데에서 끊어 하나를 삭제하거나, 아니면 가운데를 끊고 그 자리에 다른 원소를 (원소들을) 삽입하는 것이 선형 배열의 경우보다 쉽습니다. 여기에서 쉽다 라고 표현한 것은, 빠른 시간 내에 처리할 수 있다는 뜻입니다. 이러한 이점 때문에, 원소의 삽입/삭제가 빈번히 일어나는 응용에서는 연결 리스트가 많이 이용됩니다. 컴퓨터 시스템을 구성하는 중요한 요소인 운영체제 (operating system) 의 내부에서도 이러한 연결 리스트가 여러 곳에서 이용되고 있습니다.

그렇다면, 연결 리스트가 선형 배열에 비해서 가지는 단점은 없을까요? 물론 있습니다. 세상에 공짜는 없는 법이어서, 위에서 말한 바와 같이 원소의 삽입과 삭제가 용이하다는 장점은 거저 얻어지지 않습니다. 생각하기 쉬운 하나의 단점은, 선형 배열에 비해서 데이터 구조 표현에 소요되는 저장 공간 (메모리) 소요가 크다는 점입니다. 링크 또한 메모리에 저장되어 있어야 하므로, 연결 리스트를 표현하기 위해서는 동일한 데이터 원소들을 담기 위하여 사용하는 메모리 요구량이 더 큽니다. 그보다 더 우리의 관심을 끄는 단점은, k 번째의 원소 를 찾아가는 데에는 선형 배열의 경우보다 시간이 오래 걸린다는 점입니다. 선형 배열에서는 데이터 원소들이 번호가 붙여진 칸들에 들어 있으므로 그 번호를 이용하여 대번에 특정 번째의 원소를 찾아갈 수 있는 데 비하여, 연결 리스트에서는 단지 원소들이 고리로 연결된 모습을 하고 있으므로 특정 번째의 원소를 접근하려면 앞에서부터 하나씩 링크를 따라가면서 찾아가야 합니다.

- 추상적 자료구조
    - Data
        - 예: 정수, 문자열, 레코드, ...
    - A set of operations
        - 삽입, 삭제, 순회, ...
        - 정렬, 탐색, ...

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

- 연결 리스트 순회

```python
class Node:
    def __init__(self, item):
        self.data = item
        self.next = None
        
class LinkedList:
    def __init__(self):
        self.nodeCount = 0
        self.head = None
        self.tail = None
        
    def getAt(self, pos)
        if pos < 1 or pos > self.nodeCount:
            return None
        i = 1
        curr = self.head
        while i < pos:
            curr = curr.next
            i += 1
        return curr
    
    def traverse(self):
        answer = []
        curr = self.head
        while = curr != None:
            answer.append(curr.data)
            curr = curr.next
        return answer

def solution(x):
    return 0        
```

## 연결 리스트 응용

### 설명

여기에서는, 연결 리스트의 핵심 연산으로서 아래와 같은 것들이 등장합니다.

- 원소의 삽입 (insertion)
- 원소의 삭제 (deletion)
- 두 리스트 합치기 (concatenation)

이러한 연산이 쉽게 (빠르게) 이루어질 수 있다는 점이 연결 리스트가 선형 배열에 비하여 가지는 특장점입니다. 조금 다르게 표현하면, 이런 연산들이 빨라야 하는 응용처에 적용하기 위함이 연결 리스트의 존재 이유입니다.

그런데, 나열된 데이터 원소들의 사이에 새로운 데이터 원소를 삽입하려면, 앞/뒤의 원소들을 연결하고 있는 링크를 끊어 내고, 그 자리에 새로운 원소를 집어넣기 위해서 링크들을 조정해 주어야 하는, 프로그래머로서는 아주 조금 귀찮은 일들이 수반됩니다. 이 강의에서는 그러한 작업들을 Python 코드로 작성하는 연습을 함으로써 자료 구조를 다루는 프로그래밍 기법의 기초를 익힙니다. 이 기법들은 이후에 조금씩 더 복잡해지는 자료 구조를 프로그램으로 구현하는 데 바탕이 될 것입니다. 늘 그렇듯, 프로그래밍에서 어려운 점은 특이한 경우들에 대해서도 고려해야 한다는 점인데요, 원소의 삽입에 있어서는 맨 앞 또는 맨 뒤에 새로운 원소를 삽입하는 경우가 조금 생각할 꺼리가 되는 일입니다.

마찬가지로, 원소의 삭제에 있어서도 맨 앞 또는 맨 뒤의 원소를 삭제하는 경우가 조금 신경써서 처리할 일들이 생기는 경우들입니다.

마지막으로, 두 리스트를 합치는 일은 생각보다 훨씬 쉽습니다. 눈치가 빠른 분들은 이것이 매우 간단하리라는 것을 (제 7 강을 공부하셨다면) 쉽게 알아차리셨을 것 같네요.

연결 리스트를 다루는 프로그래밍 연습을 해보면서, 두 가지를 염두에 두시기를 바랍니다. 첫째, 이것이 무엇을 위함이냐? 즉, 연결 리스트라는 자료 구조를 착안함으로써 어떤 목적을 이루고자 했는지, 다시 말하면 연결 리스트가 가지는 장점이 어떤 곳에서 발휘되는지, 알고리즘의 복잡도 (제 6 강을 참고하세요) 측면에서 생각해보시기 바랍니다. 둘째, 링크를 조정하는 등의 코딩은 앞으로 나타나게 될 트리 (tree) 라든지, 이 강의 시리즈에서 다루지는 않지만 그래프 (graph) 등을 프로그래밍할 때를 대비한 연습이 된다는 점을 떠올리시고, 이런 종류의 코딩에 익숙해지려 노력해 보시기 바랍니다.


```python
class Node:
    def __init__(self, item):
        self.data = item
        self.next = None

class LinkedList:
    def __init__(self):
        self.nodeCount = 0
        self.head = None
        self.tail = None

    def __repr__(self):
        if self.nodeCount == 0:
            return 'LinkedList: empty'

        s = ''
        curr = self.head
        while curr is not None:
            s += repr(curr.data)
            if curr.next is not None:
                s += ' -> '
            curr = curr.next
        return s


    def getAt(self, pos):
        if pos < 1 or pos > self.nodeCount:
            return None

        i = 1
        curr = self.head
        while i < pos:
            curr = curr.next
            i += 1

        return curr

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

- 원소 삽입
    - pos가 가리키는 위치에 (1 <= pos <= nodeCount + 1)
    - newNode를 삽입하고 성공/실패에 따라 True/False 리턴
    - 복잡도
        - 맨 앞 삽입: O(1)
        - 중간 삽입: O(n)
        - 맨 끝 삽입 O(1) - Tail 덕분에


- 원소의 삭제
    - pos가 가리키는 위치의 (1 <= pos <= nodeCount)
    - node를 삭제하고
    - 그 node의 데이터를 리턴
    - 복잡도
        - 맨 앞 삭제: O(1)
        - 중간 삭제: O(n)
        - 맨 끝 삭제: O(n)

- 두 리스트의 연결
    - concat



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
            self.head = Node(data) # head가 없으니add할 data만 삽
        else:
            node = self.head
            while node.next:
                node = node.next  # Node.next == None까지 전진
            node.next = Node(data) # 생성할 노드를 연결
    
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
    
    # a, b, c 순서대로 링크드리스트 있다는 가정
    # 1. a 데이터 삭제하려면 head를 b로 바뀌어야
    # 2. c 데이터 삭제하려면 b 데이터 주소값 None으로
    # 3. b 데이터 삭제하려면 a 주소값을 c데이터로 변경
    def delete(self, data):
        if self.head = '':
            print("해당 값을 가진 노드가 없습니다.")
            return
        
        if self.head.data == data: # data는 인자로 받은 삭제할 데이터
            temp = self.head
            self.head = self.head.next
            def temp
        else:
            node = self.head
            while node.next:
                if node.next.data == data:
                    temp = node.next
                    node.next = node.next.next # Node.next.next는 None이니 node.next에 넣고 마무리
                    del temp
                    return
                else:
                    node = node.next
                    
    
    
```

## Link 

- [어서와, 자료구조와 알고리즘은 처음이지?](https://programmers.co.kr/learn/courses/57)

