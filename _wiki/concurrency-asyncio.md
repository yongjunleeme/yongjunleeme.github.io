---
layout  : wiki
title   : 
summary : 
date    : 2020-07-14 13:24:19 +0900
updated : 2020-07-15 21:14:47 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


## Background

- concurrency encompasses both multiprocessing (ideal for CPU-bound tasks) and threading (suited for IO-bound tasks)
- async IO is a single-threaded, single-process design: it uses cooperative multitasking,

![2](https://user-images.githubusercontent.com/48748376/87383492-50f20b80-c5d4-11ea-883b-bb75029877f7.jpeg)

### sync vs. async

- 프로그램의 주 실행흐름을 멈추지 않고 진행 가능 여부
- 비동기
    - 코드의 실행 결과 처리나 활용을 별도의 채널에 맡겨둔 뒤 결과를 기다리지 않고 바로 다음 코드를 실행하는 방식
    - 예: Future, Promise, Coroutine

### blocking vs. non-blocking I/O

- 입출력 처리가 완료될 떄까지 기다릴 것인지(Blocking) 혹은 시작만 해두고 다음 작업을 계속 진행할 것이지(Non-blocking) 여부
- I/O 작업이 완료된 이후에 연결해서 진행할 후속 작업이 있는 경우, 폴링이나 콜백 함수를 사용
- Blocking I/O를 사용했어도 이 부분이 별도의 채널을 통한 작업으로 이뤄지고 어떤 프로그램의 주 실행흐름을 막지 않았다면, 이는 비동기에 해당
- 직접 제어할 수 없는 IO, 멀티스레드를 대상으로 함

### Async Framework

#### Vibora

- 플라스크와 유사
- 가장 빠른 파이썬 웹 서버를 만드는 것이 목표
- 멀티 프로세스 아키텍처로 멀티 CPU 코어를 기본적으로 사용
- 가능한 경우 uvloop과 C로 구현된 더 빠른 방식을 사용

#### Aoihttp

- asyncio를 사용하는 비동기 HTTP 클라이언트와 서버 프레임워크
- 서버와 클라이언트 웹소켓 둘 다 지원
- 웹 서버는 미들웨어와 시그널을 제공

#### Sanic

- Able to read and write cookie
- 높은 성능의 HTTP 서버를 쉽게 구축하고 확장하는 것이 목표
- 클래스 기반의 뷰 제공
- 블루프린트라는 기능을 통해 서버라우팅 제공
- uvloop 기반의 비동기로 HTTP를 빠르게 처리할 수 있는 프레임워크

### uvloop

- uvloop은 파이썬 asyncio에서 구현된 이벤트 루프를 대체함
- Cython으로 구현되었으며 libuv를 기반으로 함

```python
import asyncio
import uvloop

asyncio.set_event_loop_policy(uvloop.EventLoopPolicy())
```

### libuv

- Unicorn Velocirprot Library
- libuv는 크로스 플랫폼을 지원하는 라이브러리, 최초 Node.js를 위해 작성
    - Node.js를 OS 종류에 상관없이 실행하고자
- 이벤트 드리븐 비동기 I/O 모델을 기반으로 구성
- kqueue(Mac OS를 포함하는 BSD 계열), epoll(리눅스), IOCP(윈도우즈) 등을 추상화
- libuv의 빠른 속도를 위한 장치들
    - 현재 시각을 캐시 - 이벤트 루프 시간 관련 시스템 호출 수를 줄이기 위해
    - 루프 활성 및 참조 처리 핸들 - 활성 요청 또는 닫히는 중인 핸들이 있으면 활성 상태인 것으로 간주
    - 마감 타이머가 시작됨
    - 대기 중인 콜백이 호출됨
        - 모든 콜백을 직접 관리 - 언제 실행할지 이벤트 루프 안에서 결정
    - I/O 콜백은 대부분 I/O 폴링 직후에 호출됨
    - 그러나 다음 루프로 호출이 지연되는 콜백도 있다.
        - 이전 반복에서 지연된 I/O 콜백은 이 시점에서 호출됨
    - idle handles
    - prepare handles - 루프가 I/O를 차단(block)하기 직전에 준비 핸들이 콜백을 호출함
    - Poll for I/O
        - I/O를 차단하기 전에 루프는 얼마나 오래 차단해야 하는지 폴링 타임아웃을 계산
        - 루프가 I/O를 차단
        - 이 시점에서 루프는 이전 단계에서 계산된 지속 시간 동안 I/O를 차단
        - 읽기 또는 쓰기 조작에 대해 주어진 파일 디스크립터를 모니터링하고 있는 모든 I/O 관련 핸들은 이 시점에서 호출된 콜백을 가져옴
    - check handles
        - 루프가 I/O를 차단한 직후 체크 핸들 콜백 호출
        - 체크 핸들은 기본적으로 준비 핸들과 대응됨

<img width="606" alt="스크린샷 2020-07-15 오전 10 56 32" src="https://user-images.githubusercontent.com/48748376/87498868-d2f53980-c693-11ea-9d75-21f4194aafb2.png">

### The I/O loop

- 각각 다른 쓰레드에서 실행된다면 여러 이벤트 루프를 실행 가능
- libuv의 이벤트 루프는 직접 언급돼 있지 않다면 안전하지 않은 쓰레드
- 모든 I/O는 각 OS에 맞는 non-blocking 소켓에서 수행


### Django X async

- Django 3.0에서 ASGI와 함께 완전한 비동기 방식을 선택적으로 지원
- 동기 방식 코드를 그대로 사용하면 SynchronousOnlyOperation 에러
    - 기존 코드는 거대한 마이그레이션이 필요할 것으로 보임

### 이벤트 루프?

- [asyncio 공식](https://docs.python.org/ko/3/library/asyncio.html)
- 이벤트 루프
     - 루프(반복문)을 돌며 하나씩 실행시키는데 특정 작업을 요청 후 기다려야 한다면 이 작업은 이벤트 루프가 통제권을 가진다.
     - 통제권을 받은 이벤트 루프는 다음 작업을 실행
     - 모든 이벤트 루프는 필수적으로 주어진 이벤트 타입과 매치된 함수가 연결되기 전에 발생하는 이벤트를 기다려야 한다. 예) 웹서버
- 코루틴
    - 이벤트 루프의 작업은 코루틴으로 이뤄져 있다. 응답이 지연되는 코루틴은 이벤트 루프에 통제권이 넘어가며 기존 상태에서부터 다시 남은 작업을 완료한다.

![3](https://user-images.githubusercontent.com/48748376/87383922-74698600-c5d5-11ea-9446-99b564429794.jpg)


- asynco 이벤트 루프
    - 호출 등록, 실행, 취소
    - 하위 프로세스 및 외부 프로그램에서 관련 통신 실행
    - 스레드 풀에서 가장 많이 호출되는 함수 지정

## asyncio

- 코루틴을 활용한 단일 스레드 기반 동시성 프로그램을 쉽게 작성할 수 있는 모듈
- 소켓과 그 외 자원들이 멀티플렉싱 I/O 접근 작업을 해준다.
- 비교적 쉽게 스레드 세이프 프로그램을 작성할 수 있도록 동기화 작업 제공

```python
#!/usr/bin/env python3
# countasync.py

import asyncio

async def count():
    print("One")
    await asyncio.sleep(1)
    print("Two")

async def main():
    await asyncio.gather(count(), count(), count())

if __name__ == "__main__":
    import time
    s = time.perf_counter()
    asyncio.run(main())
    elapsed = time.perf_counter() - s
    print(f"{__file__} executed in {elapsed:0.2f} seconds.")
```

```python
$ python3 countasync.py
One
One
One
Two
Two
Two
countasync.py executed in 1.01 seconds.
```

- `time.sleep()`은 블로킹 함수이지만 `asyncio.sleep()`은 논 블로킹 함수
- `time.sleep()`이나 다른 블로킹 호출은 비동기 파이썬 코드와는 양립 불가능하다. 블로킹 시 다른 모든 트랙이 스톱이기 때문이다.

### The Rules of Async IO

- `await`은 이벤트 루프에게 함수 제어권을 넘기는 것(주변 코루틴은 중지)
- f()의 결과가 반환될 때까지 g()의 실행을 중지한다. 그동안 다른 일이라도 뛰게 놔둬라

```python
async def g():
    # Pause here and come back to g() when f() is ready
    r = await f()
    return r
```

- await은 코루틴 함수를 리턴한다.
    - 어웨이터블 객체 3가지
    - 코루틴 객체 - 코루틴 함수를 호출하여 반환된 객체
    - 태스크 - 코루틴을 동시에 예약
    - 퓨처 - 비동기 연산의 최종 결과를 나타내는 저수준의 어웨어터블 객체

```python
#!/usr/bin/env python3
# rand.py

import asyncio
import random

# ANSI colors
c = (
    "\033[0m",   # End of color
    "\033[36m",  # Cyan
    "\033[91m",  # Red
    "\033[35m",  # Magenta
)

async def makerandom(idx: int, threshold: int = 6) -> int:
    print(c[idx + 1] + f"Initiated makerandom({idx}).")
    i = random.randint(0, 10)
    while i <= threshold:
        print(c[idx + 1] + f"makerandom({idx}) == {i} too low; retrying.")
        await asyncio.sleep(idx + 1)
        i = random.randint(0, 10)
    print(c[idx + 1] + f"---> Finished: makerandom({idx}) == {i}" + c[0])
    return i

async def main():
    res = await asyncio.gather(*(makerandom(i, 10 - i - 1) for i in range(3)))
    return res

if __name__ == "__main__":
    random.seed(444)
    r1, r2, r3 = asyncio.run(main())
    print()
    print(f"r1: {r1}, r2: {r2}, r3: {r3}")
```

### Cancellation

- 모든 awit 구문에서 발생할 수 있다.
    - await가 없는 코드는 아무리 길고 오래 걸려도 cancel 불가능
    - 당하는 입장에서는 CancelledError 예외로 보이며, 이에 대한 처리를 통해 clean-up 가능
    - 협조적 멀티 태스킹 방식이므로 아무리 밖에서 캔슬하고 싶어도 컨텍스트 스위칭이 일어나지 않으면 취소할 수 없다.
- 취소방법
    - 퓨처 또는 태스크 객체의 cancel() 메소드 호출
    - 퓨처는 즉시 취소 완료 및 완료 상태가 되지만 태스크는 클린 업 작업을 위해 추가로 비동기 작업을 더 해야 할 수 있으므로 await를 항상 한 번 더 걸어주어야 한다.
    - 스스로 취소하려면? raise asncio.CancelledError
        - asyncio.current_task().cancel() -> 예외 발생이 안 되므로 사용하면 안 된다.

### Swallowed cancellation

```python
try:

except Exception:
```

- 예외처리 구조가 아래와 같으몰 예외처리 시 구체적인 에러가 아니라 Exception만 잡으면 된다?
- BaseException
    - Exception
        - ValueError, ... 대부분의 에러타입
    - KeyboardInterrupt
    - SystemExit
    - GeneratorExit
- asyncio.CancceledError는 BaseException이 아니므로 명시적으로 잡아줘야 한다.
    - python 3.8부터는 BaseException에 들어간다?

```python
try:

except asycio.CancelledError:
    raise
```

### aiojobs

- 트랜잭션 등의 요청 처리가 중간에 예외가 생기더라도 끝까지 처리해야 할 때? sheild 쓸 수 있지만 aiojobs 라이브러리 쓸 수도 있다.

```python
async def coro(timeout):
    await asyncio.sleep(timeout)
    
async def main():
    scheduler = await aiojobs.create_scheduler()
    for i in range(100):
        # spawn jobs
        await scheduler.spawn(coro(i / 10))
    
    await asyncio.sleep(5.0)
    # not all scheduled jobs ar finished at the moment
    # wait completion of started jobs & cancel not started jobs
    await schduler.close()
```

- aiphttp는 핸들러 처리 도중이라도 연결이 끊기거나 하면 handler task를 바로 캔슬
- aiojobs는 일단 request handler가 실행되면, 그 핸들러의 실행은 끝까지 하도록 보장(process shutdown 시에도 진행 중이던 핸들러들이 모두 끝나도록 일정 시간 기다림)

```python
from aiohttp import web
from aiojobs.aiohttp import setup, spawn
import aiojobs

async def handler(request):
    await spawn(request, coro())
    return web.Response()

app = web.Application()
app.router.add_get('/', handler)
setup(app)
```

- 네트워크 처리 루프를 캔슬했을 때 종료 처리를 위해 사용 중이던 네트워크 연결을 재사용하는 경우가 있다.
    - 예) aioredis를 이용해 Redis 서버의 블로킹 콜을 await하는 상태에서 해당 코루틴을 캔슬한 수 종료 처리 과정에서 레디스 서버에 접근하고 할 땐 기존 커넥션을 사용할 수 없고 새 커넥션을 만들어야 함
    - aioredis 내부 구현이 캔슬이 발생했을 때 프로토콜 상태를 원래 상태로 되돌리지 못하기 때문
- 레디스 pub/sub 권장 구현
    - sub 명령은 해당 커넥션을 더이상 다른 용도로 못 쓰게 함(캔슬 처리를 통해 'unsubscribe'하는 것이 불가능)
    - redis connection pool을 두고 aiohttp request handler에서 매번 sub 명령을 실행하는 커넥션을 만들면 connection pool이 금방 소진될 수 있다.

### Async IO Design Patterns

- pass

## Future

```python
class Future:
    _state = _PENDING
    _result = None
    _exception = None
```

- 비동기 작업의 클래스를 나타내는 퓨처 
    - 비동기 작업은 필연적으로 상태를 저장해야 하므로 상태가 있다.
    - 비동기 작업의 결과를 저장하는 객체

## 메소드

### 이벤트 루프

#### `run_forever`

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

#### `run_until_complete()`, `stop()`

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

#### `is_closed`

- 이벤트 루프가 close() 메소드로부터 호출돼 종료됐다면 True 반환

```python
print(loop.is_closed())
```

### 태스크

- 태스크는 이벤트 내 코루틴의 실행을 담당
- 태스크는 한 번에 1개의 이벤트 루프에서만 실행
- 병렬 실행을 위해 멀티스레드를 바탕으로 멀티 이벤트 루프를 실행
- 동시에 실행


#### `ensure_future()`

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

#### `all_task(loop=None)`

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

#### `current_task()`

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

### 태스크 함수

- 이전에는 태스크 객체에서 사용되는 함수
- 이번에는 태스크 조합에서 어떻게 작동하는지

#### `as_completed(fs, *, loop=None, timeout=None)`

- 태스크에서 반환된 결과를 사용할 수 있다.
- 이 결과를 다른 코루틴에 전달해 간단한 로깅 등의 작업에 사용 가능

```python
for f in as_completed(fs):
    result = yield from f
    # result를 사용한다
```

#### `ensure_future(coro_or_future, *, loop=None)`

```python
import asyncio

async def myCoro():
    print("myCoro")

task = asyncio.ensure_future(myCoro())
```

#### `warp_future(future, *, loop=None)`

- `concurrent.futures.Future` 객체를 asyncio.Future 객체로 변환해주는 어댑터

```python
with ThreadPoolExecutor(max_workers=4) as executor:
    future = executor.submit(task, (4))
    myWrappedFuture = asyncio.wrap_future(future)
```

#### `gathre(*coroes_or_futures, loop=None, return_exceptions=False)`

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

### 퓨처

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

### 코루틴

- 단일 스레드 기반 컨텍스트에서 동작하는 예외를 가진 비동기 프로그램을 작성할 수 있다.
- awit 키워드는 호출된 코루틴이 결과를 반환하기까지 이벤트 루프 진행을 막는다. 그러나 이러한 코드의 단점은 비동기성을 잃어 기본적인 동기적 실행으로 돌아간다는 것이다.

### 트랜스포트

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

### 코루틴 간의 동기화

#### Lock

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

### 큐

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

### 세마포어

- 코루틴을 얻으면 카운터가 감소하고 릴리스하면 증가
- 주어진 자원에 접근하면서 코루틴의 수를 조절할 수 있으며 자원 부족이 발생하지 않도록 한다.

```python
import asyncio
...
mySemaphore = asyncio.Semaphore(value=4, *, loop=None)
boundedSemaphore = asyncio.BoundedSemaphore(value=4, *, loop=None)
```

### 디버깅

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


### asyncio 이외

#### 트위스티드

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

#### gevent

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

#### 비동기 지원 라이브러리

- motor(MongoDB)
- aiomysql(MySQL)
- asyncpg(PostgresSQL)
- aioredis(Redis)
- aiomcache(Memcached)
- aiokafka(Apache Kafka)

## Link

- [파이썬에서 비동기 프로그래밍 시작하기](https://sjquant.tistory.com/14)
- [Async IO in Python: A Complete Walkthrough](https://realpython.com/async-io-python/)
- [Real-world asyncio](https://www.youtube.com/watch?v=QaiczQzJAmA&list=WL&index=9&t=1563s)
