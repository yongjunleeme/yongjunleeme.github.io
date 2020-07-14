---
layout  : wiki
title   : 
summary : 
date    : 2020-07-14 13:24:19 +0900
updated : 2020-07-14 22:55:54 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


## Background

- [asyncio 공식](https://docs.python.org/ko/3/library/asyncio.html)
- 이벤트 루프
     - 루프(반복문)을 돌며 하나씩 실행시키는데 특정 작업을 요청 후 기다려야 한다면 이 작업은 이벤트 루프가 통제권을 가진다.
     - 통제권을 받은 이벤트 루프는 다음 작업을 실행
     - 모든 이벤트 루프는 필수적으로 주어진 이벤트 타입과 매치된 함수가 연결되기 전에 발생하는 이벤트를 기다려야 한다. 예) 웹서버
- 코루틴
    - 이벤트 루프의 작업은 코루틴으로 이뤄져 있다. 응답이 지연되는 코루틴은 이벤트 루프에 통제권이 넘어가며 멈췄던 부분부터 기존 상태에서부터 다시 남은 작업을 완료하는 함수 

![3](https://user-images.githubusercontent.com/48748376/87383922-74698600-c5d5-11ea-9446-99b564429794.jpg)


- asynco 이벤트 루프
    - 호출 등록, 실행, 취소
    - 하위 프로세스 및 외부 프로그램에서 관련 통신 실행
    - 스레드 풀에서 가장 많이 호출되는 함수 지정

### asyncio

- 코루틴을 활용한 단일 스레드 기반 동시성 프로그램을 쉽게 작성할 수 있는 모듈
- 소켓과 그 외 자원들이 멀티플렉싱 I/O 접근 작업을 해준다.
- 비교적 쉽게 스레드 세이프 프로그램을 작성할 수 있도록 동기화 작업 제공

#### 이벤트 루프

##### `run_forever`

- 이벤트 루프를 시작해 계속 블로킹

```python
import asyncio
 
@asyncio.coroutine
def hello_world():
    yield from asyncio.sleep(1)
    print('Hello World')
    asyncio.async(hello_world())
 
@asyncio.coroutine
def good_evening():
    yield from asyncio.sleep(1)
    print('Good Evening')
    asyncio.async(good_evening())
 
print('step: asyncio.get_event_loop()')
loop = asyncio.get_event_loop()
try:
    print('step: loop.run_until_complete()')
    asyncio.async(hello_world())
    asyncio.async(good_evening())
    loop.run_forever()
except KeyboardInterrupt:
    pass
finally:
    print('step: loop.close()')
    loop.close()
```

##### `run_until_complete()`, `stop()`

- 자체적으로 종료하기 전에 이벤트 루프가 주어진다.

```python
import asyncio
import time

async def myWork():
  print("Starting Work")
  time.sleep(5)
  print("Ending Work")

def main():
  loop = asyncio.get_event_loop()
  loop.run_until_complete(myWork())
#  loop.stop()
#  print("Loop Stopped")
  loop.close()

  print(loop.is_closed())

if __name__ == '__main__':
  main()

```

##### `is_closed`

- 이벤트 루프가 close() 메소드로부터 호출돼 종료됐다면 True 반환

```python
print(loop.is_closed())
```

#### 태스크

- 태스크는 이벤트 내 코루틴의 실행을 담당
- 태스크는 한 번에 1개의 이벤트 루프에서만 실행
- 병렬 실행을 위해 멀티스레드를 바탕으로 멀티 이벤트 루프를 실행
- 동시에 실행


##### `ensure_future()`

- 코루틴 스케쥴링

```python
import asyncio
import time

@asyncio.coroutine
def myTask(n):
    time.sleep(1)
    print("Processing {}".format(n))

@asyncio.coroutine
def myGenerator():
    for i in range(5):
        asyncio.ensure_future(myTask(i))
    print("Complete Tasks")
    yield from asyncio.sleep(2)

def main():
    loop = asyncio.get_event_loop()
    loop.run_until_complete(myGenerator())
    loop.close()
```

##### `all_task(loop=None)`

- 주어진 이벤트 루프 세트를 반환
- 아무런 이벤트 루프도 전달되지 않았다면 현재 이벤트 루프의 모든 태스크를 기본적으로 출력

```python
import asyncio

async def myCoroutine():
  print("My Coroutine")

async def main():
  await asyncio.sleep(1)

loop = asyncio.get_event_loop()
try:
  loop.create_task(myCoroutine())
  loop.create_task(myCoroutine())
  loop.create_task(myCoroutine())

  pending = asyncio.Task.all_tasks()
  print(pending)
  loop.run_until_complete(main())
finally:
  loop.close()
```

```python
{<Task pending coro=<myCoroutine() running at test.py:3>>, <Task pending coro=<myCoroutine() running at test.py:3>>, <Task pending coro=<myCoroutine() running at test.py:3>>}
My Coroutine
My Coroutine
My Coroutine
```

##### `current_task()`

- 현재 어떤 태스크 실행 중인지 확인
- 실행 중 태스크 리스트를 돌면서 원한다면 종료도 가능

```python
import asyncio
async def myCoroutine():
    print("My Coroutine")

async def main():
    current = asyncio.Task.current_task()
    print(current)

loop = asyncio.get_event_loop()
try:
    loop.create_task(myCoroutine())
    loop.create_task(myCoroutine())
    loop.create_task(myCoroutine())
    loop.run_until_complete(main())
finally:
    loop.close()
```

#### 태스크 함수

- 이전에는 태스크 객체에서 사용되는 함수
- 이번에는 태스크 조합에서 어떻게 작동하는지

##### `as_completed(fs, *, loop=None, timeout=None)`

- 태스크에서 반환된 결과를 사용할 수 있다.
- 이 결과를 다른 코루틴에 전달해 간단한 로깅 등의 작업에 사용 가능

```python
for f in as_completed(fs):
    result = yield from f
    # result를 사용한다
```

##### `ensure_future(coro_or_future, *, loop=None)`

```python
import asyncio

async def myCoro():
    print("myCoro")

task = asyncio.ensure_future(myCoro())
```

##### `warp_future(future, *, loop=None)`

- `concurrent.futures.Future` 객체를 asyncio.Future 객체로 변환해주는 어댑터

```python
with ThreadPoolExecutor(max_workers=4) as executor:
    future = executor.submit(task, (4))
    myWrappedFuture = asyncio.wrap_future(future)
```

##### `gathre(*coroes_or_futures, loop=None, return_exceptions=False)`

- 코루틴 세트 및 퓨처를 인자로 취해 입력된 세트로부터 종합된 결과를 future 객체로 반환

```python
import asyncio

asnc def my Coroutine(i):
    print("My Coroutine {}".format(i))

loop = asyncio.get_event_loop()
try:
    loop.run_until_complete(asyncio.gather(myCotoutine(1), myCotoutine(2)))
finally:
    loop.close()
```

#### 퓨처

- 퓨처
    - 이후에 결과를 받고자 하는 의도로 생성
    - `result()`와 `exception()`은 timeout 파라미터를 갖지 않으며 퓨처가 아직 일어나지 않았을 경우 예외를 발생시킨다.
    - `add_done_callback()` 포맷으로 등록된 콜백은 `call_soon_threadasafe()` 이벤트 루프를 바탕으로만 호출된다.
    - future 클래스는 concurrent.futures 패키지의 wait 및 as_completed 함수와 호환되지 않는다.
- 아래 예제
    - `asyncio.ensure_future`를 호출하ㅈ기 전 `await` 사용이 중요
    - `ensure_future()` 메소드는 이후 코루틴을 감쌀 뿐만 아니라 실행 스케쥴링도 하므로 `result()`에 접근하기 전에 완료해야 한다.


```python
import asyncio

async def myFuture(future):
  await asyncio.sleep(1)
  future.set_result("My Future Has Completed")

async def main():
  future = asyncio.Future()
  await asyncio.ensure_future(myFuture(future))
  print(future.result())

loop = asyncio.get_event_loop()
try:
  loop.run_until_complete(main())
finally:
  loop.close()
```

#### 코루틴

- 단일 스레드 기반 컨텍스트에서 동작하는 예외를 가진 비동기 프로그램을 작성할 수 있다.
- awit 키워드는 호출된 코루틴이 결과를 반환하기까지 이벤트 루프 진행을 막는다. 그러나 이러한 코드의 단점은 비동기성을 잃어 기본적인 동기적 실행으로 돌아간다는 것이다.

#### 트랜스포트

- 트랜스포트는 다양한 통신 타입 구현을 지원하는 asyncio 모듈에 포함된 클래스
- 모두 BaseTransport 클래스로부터 상속
    - ReadTransport
    - WriteTransport
    - DatagramTransport
    - BaseSubprocesTransport
- 메소드
    - close(): 트랜스포트를 닫는다.
    - is_closing(): 트랜스포트가 닫혔는지 참, 거짓을 반환
    - get_extra_info(name, default=None): 추가적인 트랜스포트 정보를 반환
    - set_protocol(protocol): 함수명 그대로 프로토콜을 설정
    - get_protocol(): 현재 프로토콜을 반환

#### 코루틴 간의 동기화

##### Lock

- 어떤 코루틴도 동시에 실행할 수 없기에 락
- 각 작업자 차례에 락을 얻고 이전의 락을 릴리스하기 전에 실행해야 할 부분을 진행하고 두 번째 코루틴이 지속되며 코드상 중요한 부분을 실행한다.

```python
import asyncio
import time

async def myWorker(lock):
  with await lock:
    print(lock)
    print("myWorker has attained lock, modifying variable")
    time.sleep(2)
  print(lock)
  print("myWorker has release the lock")
  
async def main(loop):
  lock = asyncio.Lock()
  await asyncio.wait([myWorker(lock),myWorker(lock)])
  
loop = asyncio.get_event_loop()
try:
  loop.run_until_complete(main(loop))
finally:
  loop.close()
```

#### 큐

```python
import asyncio
import random
import time

@asyncio.coroutine
def newsProducer(myQueue):
  while True:
    yield from myQueue.put(random.randint(1,5))
    yield from asyncio.sleep(1)
    
@asyncio.coroutine
def newsConsumer(myQueue):
  while True:
    articleId = yield from myQueue.get()
    print("News Reader Consumed News Article {}", articleId)

myQueue = asyncio.Queue()

loop = asyncio.get_event_loop()

loop.create_task(newsProducer(myQueue))
loop.create_task(newsConsumer(myQueue))

try:
  loop.run_forever()
finally:
  loop.close()
```

#### 세마포어

- 코루틴을 얻으면 카운터가 감소하고 릴리스하면 증가
- 주어진 자원에 접근하면서 코루틴의 수를 조절할 수 있으며 자원 부족이 발생하지 않도록 한다.

```python
import asyncio
...
mySemaphore = asyncio.Semaphore(value=4, *, loop=None)
boundedSemaphore = asyncio.BoundedSemaphore(value=4, *, loop=None)
```

#### 디버깅

```python
import asyncio
import logging
import time

logging.basicConfig(level=logging.DEBUG)

async def myWorker():
  logging.info("My Worker Coroutine Hit")
  time.sleep(1)

async def main():
  logging.debug("My Main Function Hit")
  await asyncio.wait([myWorker()])

loop = asyncio.get_event_loop()
loop.set_debug(True)
try:
  loop.run_until_complete(main())
finally:
  loop.close()
```

```python
DEBUG:asyncio:Using selector: KqueueSelector
DEBUG:root:My Main Function Hit
INFO:root:My Worker Coroutine Hit
WARNING:asyncio:Executing <Task finished coro=<myWorker() done, defined at test.py:7> result=None created at /Users/hwaryangyoo/miniconda3/lib/python3.7/asyncio/tasks.py:387> took 1.006 seconds
DEBUG:asyncio:Close <_UnixSelectorEventLoop running=False closed=False debug=True>
```

- 추가된 로그 출력이 있다.
- 코루틴 중 하나가 이벤트 루프가 종료됐을 떄 100밀리초 이상 실행됐다.
- 디버깅 모드는 어떤 코루틴을 전달하지 말아야 할지 결정하는 데 유용
- 다른 유용한 기능들
    - `call_soon()`과 `call_at()` 메소드는 잘못된 스레드로부터 호출도리 경우 예외를 발생
    - 선택자의 실행 시간 로깅
    - ResourceWarning 경고는 트랜스포트와 이벤트 루프가 명시적으로 닫히지 않았을 경우 발생


#### asyncio 이외

##### 트위스티드

- 비동기 및 이벤트 기반 디자인
- 이벤트 기반 네트워킹 엔진
- 복잡한 코드 없이 프로그램 생성 가능, 로우 레벨 API 제공

```python
from twisted.web.server import Site
from twisted.web.static import File
from twisted.internet import reactor, endpoints

resource = File('tmp')
factory = Site(resource)
endpoint = endpoints.TCP4ServerEndpoint(reactor, 8888)
endpoint.listen(factory)
reactor.run()
```

##### gevent

```python
import gevent
from gevent import socket

def main():
  urls = ['www.google.com', 'www.example.com', 'www.python.org']
  jobs = [gevent.spawn(socket.gethostbyname, url) for url in urls]
  gevent.joinall(jobs, timeout=2)
  print([job.value for job in jobs])

if __name__ == '__main__':
  main()
```


## Link

- [파이썬에서 비동기 프로그래밍 시작하기](https://sjquant.tistory.com/14)


