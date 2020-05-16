---
layout  : wiki
title   : concurrency 
summary : 
date    : 2020-05-11 12:56:15 +0900
updated : 2020-05-16 13:41:26 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Generator

```python
# 흐름제어, 병행처리(Concurrency)
# 제네레이터, 반복형
# Generator

# 파이썬 반복형 종류
# for, collections, text file, List, Dict, Set, Tuple, unpacking, *args
# 공부할 것 : 반복형 객체 내부적으로 iter 함수 내용, 제네레이터 동작 원리, yield from

# 반복 가능한 이유? -> iter(x) 함수 호출

t = 'ABCDEF'

# for 사용
for c in t:
    print('EX1-1 -', c)

# while 사용

w = iter(t)

while True:
    try:
        print('EX1-2 -', next(w))
    except StopIteration: # 더이상 next 없으면 예외처리
        break

```

```python
from collections import abc

# 반복형 확인
print('EX1-3 -', hasattr(t, '__iter__')) # hasattr -> 속성 존재여부 확인
print('EX1-4 -', isinstance(t, abc.Iterable)) # t가 iterable 클래스와 같은지 확인
```

```python
# next 사용

class WordSplitIter:
    def __init__(self, text):
        self._idx = 0
        self._text = text.split(' ')
    
    def __next__(self):
        # print('Called __next__')
        try:
            word = self._text[self._idx]
        except IndexError:
            raise StopIteration('Stop! Stop!')
        self._idx += 1
        return word
    
    def __iter__(self):
        print('Called __iter__')
        return self
    
    def __repr__(self):
        return 'WordSplit(%s)' % (self._text)


wi = WordSplitIter('Who says the nights are for sleeping')

print('EX2-1 -', wi)
print('EX2-2 -', next(wi))
print('EX2-3 -', next(wi))
print('EX2-4 -', next(wi))
print('EX2-5 -', next(wi))
print('EX2-6 -', next(wi))
print('EX2-7 -', next(wi))
print('EX2-8 -', next(wi))
# print('EX2-9 -', next(wi))
```

### Generator 패턴

```python
# 1.지능형 리스트, 딕셔너리, 집합 -> 데이터 셋이 증가 될 경우 메모리 사용량 증가 -> 제네레이터 완화
# 2.단위 실행 가능한 코루틴(Coroutine) 구현에 아주 중요
# 3.딕셔너리, 리스트 한 번 호출 할 때 마다 하나의 값만 리턴 -> 아주 작은 메모리 양을 필요로 함

class WordSplitGenerator:
    def __init__(self, text):
        self._text = text.split(' ')
    
    def __iter__(self):
        for word in self._text:
           yield word # 제네레이터
        return
    
    def __repr__(self):
        return 'WordSplit(%s)' % (self._text)


wg = WordSplitGenerator('Who says the nights are for sleeping')

wt = iter(wg)

print('EX3-1 -', wt)
print('EX3-2 -', next(wt))
print('EX3-3 -', next(wt))
print('EX3-4 -', next(wt))
print('EX3-5 -', next(wt))
print('EX3-6 -', next(wt))
print('EX3-7 -', next(wt))
print('EX3-8 -', next(wt))
# print('EX3-9 -', next(wt))
```

### 예제 1

```python
def generator_ex1():
    print('start')
    yield 'AAA'
    print('continue')
    yield 'BBB'
    print('end')

temp = iter(generator_ex1())

# print('EX4-1 -', next(temp))
# print('EX4-2 -', next(temp))
# print('EX4-1 -', next(temp))

for v in generator_ex1():
    pass
    # print('EX4-3 -', v)
```

### 예제 2(리스트, 제너레이터 차이)

```python
temp2 = [x * 3 for x in generator_ex1()]
temp3 = (x * 3 for x in generator_ex1())

print('EX5-1 -',temp2)
print('EX5-2 -',temp3)

for i in temp2:
    print('EX5-3 -', i)

for i in temp3:
    print('EX5-4 -', i)
```

### 예제 3(itertool)

```python
import itertools

gen1 = itertools.count(1, 2.5)

print('EX6-1 -', next(gen1))
print('EX6-2 -', next(gen1))
print('EX6-3 -', next(gen1))
print('EX6-4 -', next(gen1))
# ... 무한
```

#### 조건

```python
gen2 = itertools.takewhile(lambda n : n < 1000, itertools.count(1, 2.5))

for v in gen2:
    print('ex6-5 -', v)
```

#### 필터 반대

```python
gen3 = itertools.filterfalse(lambda n : n < 3, [1,2,3,4,5])

for v in gen3:
    print('EX6-6 -', v)
```

#### 누적 합계

```python
gen4 = itertools.accumulate([x for x in range(1, 101)])

for v in gen4:
    print('EX6-7 -', v)
```

#### 연결1

```python
gen5 = itertools.chain('ABCDE', range(1,11,2))

print('EX6-8 -', list(gen5))
```

#### 연결 2 - 벡터에 사용

```python
gen6 = itertools.chain(enumerate('ABCDE'))

print('EX6-9 -', list(gen6))
```

#### 개별

```python
gen7 = itertools.product('ABCDE')

print('EX6-10 -', list(gen7))
```

#### 연산(경우의 수)

```python
gen8 = itertools.product('ABCDE', repeat=2)

print('EX6-11 -', list(gen8))
```

#### 그룹화

```python
gen9 = itertools.groupby('AAABBCCCCDDEEE')

# print('EX6-12 -', list(gen9))

for chr, group in gen9:
    print('EX6-12 -', chr, ' : ', list(group))
```

## Coroutine

### 코루틴 설명

- 흐름제어, 병행처리(Concurrency)
- yeild: 메인루틴 <-> 서브루틴
- 코루틴 제어, 코루틴 상태, 양방향 값 전송

- 서브루틴 : 메인루틴에서 리턴에 의해 호출 부분으로 돌아와 다시 프로세스로
- 코루틴 : 루틴 실행 중 멈추면 특정 위치로 갔다가 다시 원래 위치로 돌아와 프로세스 수행 -> 동시성 프로그래밍 가능
    - 코루틴 스케쥴링은 오버헤드가 적다.
- 쓰레드 : 싱글쓰레드, 멀티쓰레드는 복잡하다. 공유되는 자원이 교착 상태를 일으킬 가능성이 있어서. 컨텍스트 스위칭 비용 발생, 자원 소비 가능성 증가
- [코딩도장 코루틴](https://dojang.io/mod/page/view.php?id=2418)

### 코루틴 예제1

```python
def coroutine1():
    print('>>> coroutine started.')
    i = yield  # yield 'aaa'가 아니라 할당을 해서
    print('>>> coroutine received : {}'.format(i))

c1 = coroutine1()
print('EX1-1 -', c1, type(c1))

# yield 실행 전까지 진행
# next(c1)

# 기본으로 None 전달
# next(c1)

# 값 전송하면 다음 값 출력 가능
# c1.send(100)

# 잘못된 사용

c2 = coroutine1() # 제너레이터를 실행한 다음 값을 보내야 한다.
# c2.send(100) # 예외 발생
```

### 코루틴 예제2

```python
# GEN_CREATED : 처음 대기 상태
# GEN_RUNNING : 실행 상태
# GEN_SUSPENDED : yield 대기 상태
# GEN_CLOSED : 실행 완료 상태

def coroutine2(x):
    print('>>> coroutine started : {}'.format(x))
    y = yield x # x는 메인루틴에 전달할 값으로 next메소드로 호출, y는 메인루틴이 코루틴에게 send로 보내야 하는 값
    print('>>> coroutine received : {}'.format(y))
    z = yield x + y 
    print('>>> coroutine received : {}'.format(z))

c3 = coroutine2(10)

from inspect import getgeneratorstate

print('EX1-2 -', getgeneratorstate(c3))

print(next(c3))

print('EX1-3 -', getgeneratorstate(c3)) # 대기 상태에서 값을 보내주거나 아니면 None이 되거나

print(c3.send(15))

# print(c3.send(20)) # 예외
```

### 데코레이터 패턴

```python
from functools import wraps

def coroutine(func):
    '''Decorator run until yield'''
    @wraps(func)
    def primer(*args, **kwargs):
        gen = func(*args, **kwargs)
        next(gen)
        return gen
    return primer

@coroutine
def sumer():
    total = 0
    term = 0
    while True:
        term = yield total
        total += term

su = sumer()

print('EX2-1 -', su.send(100))
print('EX2-2 -', su.send(40))
print('EX2-3 -', su.send(60))
```

### 코루틴 예제3 (예외처리)

```python
class SampleException(Exception):
    '''설명에 사용할 예외 유형'''

def coroutine_except():
    print('>> coroutine stated.')
    try:
        while True:
            try:
                x = yield
            except SampleException:
                print('-> SampleException handled. Continuing..')
            else:
                print('-> coroutine received : {}'.format(x))
    finally:
        print('-> coroutine ending')


exe_co = coroutine_except()

print('EX3-1 -', next(exe_co))
print('EX3-2 -', exe_co.send(10))
print('EX3-3 -', exe_co.send(100))
print('EX3-4 -', exe_co.throw(SampleException)) # 예외처리했으므로 계속 값을 보낼 수 있음
print('EX3-5 -', exe_co.send(1000))
print('EX3-6 -', exe_co.close()) # GEN_CLOSED
```

### 코루틴 예제4(return)

```python
def averager_re():
    total = 0.0
    cnt = 0
    avg = None
    while True:
        term = yield
        if term is None:
            break
        total += term
        cnt += 1
        avg = total / cnt
    return 'Average : {}'.format(avg)

avger2 =averager_re()

next(avger2)

avger2.send(10)
avger2.send(30)
avger2.send(50)

try:
    avger2.send(None)
except StopIteration as e:
    print('EX4-1 -', e.value)
```

### 코루틴 예제5(yeild from)

```python
# StopIteration 자동 처리(3.7 -> await)
# 중첩 코루틴 처리


def gen1():
    for x in 'AB':
        yield x
    for y in range(1,4):
        yield y

t1 = gen1()

print('EX5-1 -', next(t1))
print('EX5-2 -', next(t1))
print('EX5-3 -', next(t1))
print('EX5-4 -', next(t1))
print('EX5-5 -', next(t1))
# print('EX5-6 -', next(t1))

t2 = gen1()

print('EX5-7 -', list(t2))

def gen2():
    yield from 'AB'
    yield from range(1,4)

t3 = gen2()

print('EX6-1 -', next(t3))
print('EX6-2 -', next(t3))
print('EX6-3 -', next(t3))
print('EX6-4 -', next(t3))
print('EX6-5 -', next(t3))
# print('EX6-6 -', next(t3))

t4 = gen2()

print('EX6-7 -', list(t4))

def gen3_sub():
    print('Sub coroutine.')
    x = yield 10
    print('Recv : ', str(x))
    x = yield 100
    print('Recv : ', str(x))

def gen4_main():
    yield from gen3_sub()

t5 = gen4_main()

print('EX7-1 -', next(t5))
print('EX7-2 -', t5.send(7))
print('EX7-2 -', t5.send(77))
```

## Future

### 순차 실행

```python
import os  # 폴더 경로 확인 위해
import time
import sys
import csv

# 국가정보, 대문자변수 - 바뀌지 않는 정보
NATION_LS = ('Singapore Germany Israel Norway Italy Canada France Spain Mexico').split() # 콤마로 구분된 리스트로 반환
# 초기 CSV 위치
TARGET_CSV = 'C:/python_advanced/resources/nations.csv' # 본인 경로 변경
# 저장 폴더 위치
DEST_DIR = 'C:/python_advanced/csvs' # 본인 경로 변경
# CSV 헤더 기초 정보
HEADER = ['Region','Country','Item Type','Sales Channel','Order Priority','Order Date','Order ID','Ship Date','Units Sold','Unit Price','Unit Cost','Total Revenue','Total Cost','Total Profit']

# 국가별 CSV 파일 저장
def save_csv(data, filename):
    # 최종 경로 생성
    path = os.path.join(DEST_DIR, filename)

    with open(path, 'w', newline='') as fp:
        writer = csv.DictWriter(fp, fieldnames=HEADER)
        # Header Write
        writer.writeheader()
        # Dict to CSV Write
        for row in data:
            writer.writerow(row)

# 국가별 분리
def get_sales_data(nt):
    with open(TARGET_CSV, 'r') as f:
        reader = csv.DictReader(f)
        # Dict을 리스트로 적재
        data = []
        # Header 확인
        # print(reader.fieldnames)
        for r in reader:
            # OrderedDict 확인
            # print(r)
            # 조건에 맞는 국가만 삽입
            if r['Country'] == nt:
                data.append(r)
    return data

# 중간 상황 출력
def show(text):
    print(text, end=' ')
    # 중간 출력(버퍼 비우기)
    sys.stdout.flush()


# 국가 별 분리 함수 실행
def separate_many(nt_list):
    for nt in sorted(nt_list):
        # 분리 데이터
        data = get_sales_data(nt)
        # 상황 출력
        show(nt)
        # 파일 저장
        save_csv(data, nt.lower() + '.csv')

    return len(nt_list)


# 시간 측정 및 메인함수
def main(separate_many):
    # 시작 시간
    start_tm = time.time()
    # 결과 건수
    result_cnt = separate_many(NATION_LS)
    # 종료 시간
    end_tm = time.time() - start_tm

    msg = '\n{} csv separated in {:.2f}s'
    # 최종 결과 출력
    print(msg.format(result_cnt, end_tm))


# 실행
if __name__ == '__main__': # 불필요한 함수 실행 안하도록
    main(separate_many)
```

### concurrent.futures 방법1

- 쓰레드를 늘려도 더 느린 이유
    - GIL 
    - 파일 입출력이 아닌 데이터 입출력 사용 시 더 빠르게 수행 가능
    - 컨텍스트 스위칭 비용
- ProcessPoolExecutor -> GIL 우회, CPU로 작동
- [Concurrency, Parallelism 차이](https://12bme.tistory.com/184)

```python
# 비동기 작업 실행
# 지연시간(Block) CPU 및 리소스 낭비 방지 -> (File)Network I/O 관련 작업 동시성 활용 권장
# 적합한 작업일 경우 순차 진행보다 압도적으로 성능 향상

# Google Python GIL(Global Interpreter Lock)
# Gil은 한 번에 하나의 스레드만 수행 할 수 있게 인터프리터 자체에서 락을 거는 것.

import os
import time
import sys
import csv
from concurrent import futures

# concurrent.future 방법1(ThreadPoolExecutor, ProcessPoolExecutor)
# map()
# 서로 다른 스레드 또는 프로세스에서 실행 가능
# 내부 과정 알 필요 없으며, 고수준으로 인터페이스 제공

# 시간 측정 및 메인함수
def main(separate_many):
    # worker 개수
    worker = min(20, len(NATION_LS))
    start_tm = time.time()
    # ProcessPoolExecutor : GIL 우회, 변경 후 -> os.cpu_count()
    # ThreadPoolExecutor : GIL 종속
    # with futures.ThreadPoolExecutor(worker) as excutor:
    with futures.ProcessPoolExecutor() as excutor:
        # map -> 작업 순서 유지, 즉시 실행
        result_cnt = excutor.map(separate_many, sorted(NATION_LS))
    # 종료 시간
    end_tm = time.time() - start_tm

    msg = '\n{} csv separated in {:.2f}s'
    # 최종 결과 출력
    print(msg.format(list(result_cnt), end_tm))

# 실행
if __name__ == '__main__':
    main(separate_many)
```

### concurrent.futures 방법2

```python
# Future 동시성
# 비동기 작업 실행
# 지연시간(Block) CPU 및 리소스 낭비 방지 -> (File)Network I/O 관련 작업 동시성 활용 권장
# 적합한 작업일 경우 순차 진행보다 압도적으로 성능 향상

import os
import time
import sys
import csv
from concurrent import futures

# 시간 측정 및 메인함수
def main(separate_many):
    # worker 개수
    worker = min(20, len(NATION_LS))
    # 시작 시간
    start_tm = time.time()
    # futures
    futures_list = []
    # 결과 건수
    # ProcessPoolExecutor : GIL 우회, 변경 후 -> os.cpu_count()
    # ThreadPoolExecutor : GIL 종속
    with futures.ProcessPoolExecutor() as excutor:
        # Submit -> Callable 객체 스케쥴링(실행 예약) -> Future -> 예외발생 시 적절한 처리
        # Future -> result(), done(), as_completed() 주로 사용
        # result() -> 결괏값
        # done () -> 각자 마무리 잘 했는지
        # as_completed() -> 모두 일이 끝날 때까지 기다려준다
        for nt in sorted(NATION_LS):
            # future 반환
            future = excutor.submit(separate_many, nt)
            # 스케쥴링
            futures_list.append(future)
            # 출력
            # print('Scheduled for {} : {}'.format(nt, future))
            # print()
        
        for future in futures.as_completed(futures_list):
            result = future.result()
            done = future.done()
            cancelled = future.cancelled
            # future 결과 확인
            print('Future Result : {}, Done : {}'.format(result, done))
            print('Future Cancelled : {}'.format(cancelled))
 
    # 종료 시간
    end_tm = time.time() - start_tm
    msg = '\n csv separated in {:.2f}s'
    # 최종 결과 출력
    # print(msg.format(list(futures_list), end_tm))
    print(msg.format(end_tm))


# 실행
if __name__ == '__main__':
    main(separate_many)
```

## Asyncio

### 순차실행

- 동기는 기다리고 비동기는 안 기다리는?

```python

# BlockIO
# 순차 실행

import timeit
from urllib.request import urlopen

urls = ['http://daum.net', 'https://google.com', 'https://apple.com', 'https://tistory.com', 'https://github.com/', 'https://gmarket.co.kr/']
start = timeit.default_timer()

# 순차 실행부
for url in urls:
    print('Start', url)
    urlopen(url)
    # urlopen으로 인해 다른 작업들도 모두 일시정지
    # 이를 BlockIO라 하고 주로 파일쓰기, 네트워크 I/O 등에서 일어난다
    print('Done', url)

# 완료시간 - 시작시간
duration = timeit.default_timer() - start

# 총 실행 시간
print('Total Time : ', duration) # 요청, 응답에 걸린 시간
```

### Thread

```python
# BlockIO -> Thread 사용
# 쓰레드 개수 및 GIL 문제 고려, 공유 메모리(뮤텍스) 문제 해결
# 비순차 실행

import timeit
from urllib.request import urlopen
from concurrent.futures import ThreadPoolExecutor
import threading

# 시작 시간
start = timeit.default_timer()
urls = ['http://daum.net', 'https://google.com', 'https://apple.com', 'https://tistory.com', 'https://github.com/', 'https://gmarket.co.kr/']


def fetch(url):
    print('Thread Name :', threading.current_thread().getName(), 'Start', url) # 현재 스레드 이름 출력
    urlopen(url)
    print('Thread Name :', threading.current_thread().getName(), 'Done', url)

def main():
    with ThreadPoolExecutor(max_workers=10) as executor:
        for url in urls:
            executor.submit(fetch, url)

if __name__ == '__main__': # 쓰레드 쓸 때는 진입점을 만들어줘야 에러가 안 난다
    # 함수 실행
    main()
    # 완료시간 - 시작시간
    duration = timeit.default_timer() - start
    # 총 실행 시간
    print('Total Time : ', duration)

```

### Asyncio

- 단일 스레드에서 너멈추면 내가 일하고, 내가 멈추면 네가 일한다

```python
# 비동기 I/O Coroutine - Asyncio 작업
# Generator -> 반복적인 객체 Return 사용 -> 단일 스레드로 여러 스레드 사용하는 것 같은 효과
# 즉, 실행 Stop -> 다른 작업으로 위임 -> Stop 지점 부터 재실행 원리
# non-blocking 비동기 처리에 적합

# aiohttp 사용 가능(Asyncio 지원)
import asyncio
import timeit
from urllib.request import urlopen
from concurrent.futures import ThreadPoolExecutor
import threading

# 시작 시간
start = timeit.default_timer()
urls = ['http://daum.net', 'https://google.com', 'https://apple.com', 'https://tistory.com', 'https://github.com/', 'https://gmarket.co.kr/']

# urlopen은 BlockIO라 쓰레드로 변경
# urlopen에 async를 붙인 aiohttp 라이브러리 쓰면 쓰레드 없이 해결 가능
async def fetch(url, executor):
    # 쓰레드 이름 주목!
    print('Thread Name :', threading.current_thread().getName(), 'Start', url)
    # 실행
    res = await loop.run_in_executor(executor, urlopen, url) # 일꾼 1명, 일꾼이 처리할 함수, url
    print('Thread Name :', threading.current_thread().getName(), 'Done', url)
    # 반환
    return res.read()[0:5]

async def main():
    # 쓰레드 풀 생성
    executor = ThreadPoolExecutor(max_workers=10)

    # asyncio.ensure_future :
    futures = [
        asyncio.ensure_future(fetch(url, executor)) for url in urls # task 정의
    ]
    
    # 결과 취합
    rst = await asyncio.gather(*futures) # await과 yield from 같다

    print()
    print('Result : ', rst)

if __name__ == '__main__':
    # 루프 생성
    loop = asyncio.get_event_loop() # 지금 일하는 컴이 yield를 만나면 다른 컴에 넘겨주는 루프
    # 루프 대기
    loop.run_until_complete(main())
    # 완료시간 - 시작시간
    duration = timeit.default_timer() - start
    # 총 실행 시간
    print('Total Time : ', duration)
```


## Link

- [파이썬 웹 개발](https://www.fastcampus.co.kr/dev_online_pyweb)
