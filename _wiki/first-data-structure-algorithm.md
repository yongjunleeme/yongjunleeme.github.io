---
layout  : wiki
title   : first-data-structure-algorithm
summary : 
date    : 2020-03-31 21:15:47 +0900
updated : 2020-04-05 08:33:15 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 선형 배열

```python
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

- 이진 탐색 O(log n)

- sorted()
    - 내장 함수
    - 정렬된 새로운 리스트 얻어냄
- sort()
    - 리스트의 메서드
    - 해당 리스트를 정렬함

reverse = True 정렬 순서 반대로

```python
L2 = sorted(L, reverse = True)
```

```python
L = ['abcd', 'xyz', 'spam']
sorted(L, key=lambda x: len(x))
```

```python
L = [{'name': 'John', 'score': 83},.. ]

L.sort(key = lambd x:x['name'])
# 알파벳 순으로 정렬

```

## 재귀

### 설명

알고리즘들 중에는, 재귀 알고리즘 (recursive algorithm) 이라고 불리는 것들이 있습니다. 이것은 알고리즘의 이름이 아니라 성질입니다. 주어진 문제가 있을 때, 이것을 같은 종류의 보다 쉬운 문제의 답을 이용해서 풀 수 있는 성질을 이용해서, 같은 알고리즘을 반복적으로 적용함으로써 풀어내는 방법입니다.

예를 들면, 1 부터 n 까지 모든 자연수의 합을 구하는 문제 (sum(n))는, 1 부터 n - 1 까지의 모든 자연수의 합을 구하는 문제 (sum(n - 1))를 풀고, 여기에 n 을 더해서 그 답을 찾을 수 있습니다. 즉,

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
    - 병합 정렬 - nlong n (merge sort)

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

- Node
    - Data
    - Link(next)
    - 노드 내 데이터는 다른 구조로 이뤄질 수 있음 예) 문제열, 레코드, 다른 연결 리스트 등

## 예제

- K번째 수 

```python
def get(self, pos):
    if <= 0 or pos > self.nodeCount:
        return None
        
    i = 1
    curr = self.head
    shile i < pos:
        curr = curr.next
        i += 1
```

9분

## Link 

- [어서와, 자료구조와 알고리즘은 처음이지?](https://programmers.co.kr/learn/courses/57)

