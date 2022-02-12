---
layout  : wiki
title   : 
summary : 
date    : 2022-02-12 23:52:05 +0900
updated : 2022-02-13 00:42:07 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 동시성과 병렬성

- 동시성(concurrency)이란 컴퓨터가 같은 시간에 여러 다른 작업을 처리하는 것처럼 보이는 것을 뜻한다. 예를 들어 CPU 코어가 하나뿐인 컴퓨터에서 운영체제는 유일한 프로세서 코어에서 실행되는 프로그램을 아주 빠르게 변경할 수 있다. 이렇게 하면 여러 프로그램이 번갈아가며 실행되면서 프로그램이 동시에 수행되는 것 같은 착각을 불러일으킬 수 있다.
- 병렬성(parallelism)이란 같은 시간에 여러 다른 작업을 실제로 처리하는 것을 뜻한다. CPU 코어가 여러 개인 컴퓨터는 여러 프로그램을 동시에 실행할 수 있다.
- 스레드(thread)는 상대적으로 적은 양의 동시성을제공하지만, 코루틴(coroutine)은 수많은 동시성 함수를 사용할 수 있게 해준다. 파이썬은 시스템 콜, 하위 프로세스(subprocess), C 확장(extension)을 사용해 작업을 병렬로 수행할 수 있다. 하지만 동시성 파이썬 코드가 실제 병렬적으로 실행되게 만드는 것은 매우 어렵다. 여러 가지 상황에서 파이썬을 어떻게 사용하면 가장 좋을지 알아둬야 한다.

## [52. 자식 프로세스를 관리하기 위해 subprocess를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_52.py)

가독성과 유지 보수성을 높이기 위해 스크립트를 파이썬으로 다시 작성하는 것은 자연스러운 선택
파이썬이 시작한 자식 프로세스는 서로 병렬적으로 실행되기 때문에 파이썬이 컴퓨터의 모든 CPU 코어를 사용할 수 있고, 그에 따라 프로그램의 스루풋(throughput)을 최대로 높일 수 있다. 파이썬 자체는 한 CPU에 묶여 있지만 파이썬을 사용해 CPU를 많이 사용하는 여러 부하를 조작하면서 서로 협력하게 조정하기는 쉽다.

```python
import subprocess
# Enable these lines to make this example work on Windows
# import os
# os.environ['COMSPEC'] = 'powershell'

result = subprocess.run(
    ['echo', 'Hello from the child!'],
    capture_output=True,
    # Enable this line to make this example work on Windows
    # shell=True,
    encoding='utf-8')

result.check_returncode()  # No exception means it exited cleanly
print(result.stdout)
```

파이썬에서 subprocess 등의 모율을 통해 실행한 자식 프로세스는 부모 프로세스인 파이썬 인터프리터와 독립적으로 실행된다. run 함수 대신 Popen 클래스를 사용해 하위 프로세스를 만들면, 파이썬이 다른 일을 하면서 주기적으로 자식 프로세스의 상태를 검사(polling)할 수 있다.

```python
# Example 2
# Use this line instead to make this example work on Windows
# proc = subprocess.Popen(['sleep', '1'], shell=True)
proc = subprocess.Popen(['sleep', '1'])
while proc.poll() is None:
    print('Working...')
    # Some time-consuming work here
    import time
    time.sleep(0.3)

print('Exit status', proc.poll())
```

자식 프로세스와 부모를 분리하면 부모 프로세스가 원하는 개수만큼 많은 자식 프로세스를 병렬로 실행할 수 있다. 다음 코드는 Popen을 사용해 자식 프로세스를 한꺼번에 시작한다.

```python
import time

start = time.time()
sleep_procs = []
for _ in range(10):
    proc = subprocess.Popen(['sleep', '1'])
    sleep_procs.append(proc)


# Example 4
for proc in sleep_procs:
    proc.communicate()

end = time.time()
delta = end - start
print(f'Finished in {delta:.3} seconds')
```

파이썬 프로그램의 데이터를 파이프(pipe)를 사용해 하위 프로세스로 보내거나, 하위 프로세스의 출력을 받을 수 있다. 이를 통해 여러 다른 프로그램을 사용해서 병렬적으로 작업을 수행할 수 있다. 예를 들어 openssl 명령줄 도구를 사용해 데이터를 암호화한다고 해보자. 명령줄 인자를 사용해서 자식 프로세스를 시작하고 자식 프로세스와 I/O 파이프를 연결하는 것은 쉽다.
다음 코드는 자식 프로세스가 끝나기를 기다렸다가 마지막 출력을 가져온다.

```python
import os

def run_encrypt(data):
    env = os.environ.copy()
    env['password'] = 'zf7ShyBhZOraQDdE/FiZpm/m/8f9X+M1'
    proc = subprocess.Popen(
        ['openssl', 'enc', '-des3', '-pass', 'env:password'],
        env=env,
        stdin=subprocess.PIPE,
        stdout=subprocess.PIPE)
    proc.stdin.write(data)
    proc.stdin.flush()  # Ensure that the child gets input
    return proc


# Example 6
procs = []
for _ in range(3):
    data = os.urandom(10)
    proc = run_encrypt(data)
    procs.append(proc)


# Example 7
for proc in procs:
    out, _ = proc.communicate()
    print(out[-10:])
```

유닉스 파이프라인처럼 한 자식 프로세스의 출력을 다음 프로세스의 입력으로 계속 연결시켜서 여러 병렬 프로세스를 연쇄적으로 연결할 수도 있다. 다음은 openssl 명령줄 도구를 하위 프로세스로 만들어서 입력 스트림의 월풀(Whirlpool) 해시를 계산한다.

-> stdin=input_stdin -> encrypt_proc.stdout으로 잇는 듯

자식 프로세스들이 시작되면 프로세스간 I/O가 자동으로 일어난다.

```python
# Example 8
def run_hash(input_stdin):
    return subprocess.Popen(
        ['openssl', 'dgst', '-whirlpool', '-binary'],
        stdin=input_stdin,
        stdout=subprocess.PIPE)


# Example 9
encrypt_procs = []
hash_procs = []
for _ in range(3):
    data = os.urandom(100)

    encrypt_proc = run_encrypt(data)
    encrypt_procs.append(encrypt_proc)

    hash_proc = run_hash(encrypt_proc.stdout)
    hash_procs.append(hash_proc)

    # 자식이 입력 스트림에 들어오는 데이터를 소비하고 communicate( 세스는 파이썬 인터프리터와 병렬로 실행되므로 CPU 코어를 최대로 쓸 수 있다.
- 간단하게 자식 프로세스를 실행하고 싶은 경우에는 run 편의 함수를 사용하라. 유닉스 스타일의 파이프라인이 필요하면 Popen 클래스를 사용하라.

## [53. 블로킹 I/O의 경우 스레드를 사용하고 병렬성을 피하라](https://github.com/yongjunleeme/effectivepython/commit/7e75b477d26a52fc49e06f98046e90ee1993720a)

파이썬의 표준 구현을 CPython이라고 한다. CPython은 두 단계를 거쳐 파이썬 프로그램을 실행한다. 첫 번째 단계는 소스 코드를 구문 분석해서 바이트코드(bytecode)로 변환한다. 바이트코드는 8비트 명령어를 사용하는 저수준 프로그램 표현이다(파이썬 3.6부터는 16비트 명령어를 사용하기 때문에 기술적으로 워크코드(workcode)라고 불러야 하지만, 기본적인 아이디어는 똑같다).

그 후 CPython은 바이트코드를 스택 기반 인터프리터를 통해 실행한다. 바이트코드 인터프리터에는 파이썬 프로그램이 실행되는 동안 일관성 있게 유지해야 하는 상태가 존재한다. CPython은 전역 인터프리터 락(Global Interpreter Lock, GIL)이라는 방법을 사용해 일관성을 강제로 유지한다.

근본적으로 GIL은 상호 배제 락(mutual exclusion lock)(뮤텍스(mutex))이며, CPython이 선점형(preemprive) 멀티스레드로 인해 영향을 받는 것을 방지한다. 선점형 멀티스레드에서는 한 스레드가 다른 스레드의 실행을 중간에 인터럽트(interrupt)시키고 제어를 가져올 수 있다.

이런 인터럽트가 예기치 못한 때 발생하면 인터프리터의 상태(예: 쓰레기 수집)가 오염될 수 있다. GIL은 CPython 자체와 CPython이 사용하는 C 확장 모듈이 실행되면서 인터럽트가 함부로 발생하는 것을 방지해, 인터프리터 상태가 제대로 유지되고 바이트코드 명령들이 제대로 실행되도록 만든다.

파이썬도 다른 언어와 같이 다중 실행 스레드를 지원하지만, GIL로 인해 여러 스레드 중 어느 하나만 앞으로 진행할 수 있다. 따라서 파이썬 프로그램의 속도를 높이고 병렬 처리를 수행하고자 스레드를 사용한다면 여러분은 크게 실망할 것이다.

(예제생략)스레드 하나만 써서 순차적으로 진행하는 것보다 여러 개를 썼을 때 속도가 더 느리다..
그럼에도 파이썬이 스레드를 지원하는 이유?

블로킹(blocking) I/O를 다루기 위해서다. 블로킹 I/O는 파이썬이 특정 시스템 콜을 사용할 때 일어난다. 파이썬 프로그램은 시스템 콜을 사용해 컴퓨터 운영체제가 자기 대신 외부 환경과 상호작용하도록 의뢰한다.

파일쓰기나 읽기, 네트워크와 상호작용하기, 디스플레이 장치와 통신하기 등의 작업이 블로킹 I/O에 속한다. 스레드를 사용하면 운영체제가 시스템 콜 요청에 응답하는 데 걸리는 시간 동안 파이썬 프로그램이 다른 일을 할 수 있다.

아래 시스템 콜을 순차적으로 실행하면 실행에 필요한 시간이 선형(linear)으로 증가한다.

```python
# Example 6
import select
import socket

def slow_systemcall():
    select.select([socket.socket()], [], [], 0.1)


# Example 7
start = time.time()

for _ in range(5):
    slow_systemcall()

end = time.time()
delta = end - start
print(f'Took {delta:.3f} seconds')
```

문제는 `slow_systemcall` 함수가 실행되는 동안 프로그램이 아무런 진전을 이룰 수 없다는 것이다. 이 프로그램이 주 실행 스레드는 `select` 시스템 콜에 의해 블록된다. 

블로킹 I/O와 계산을 동시에 수행해야 한다면 시스템 콜을 스레드로 옮기는 것을 고려해봐야 한다.

```python
from threading import Thread

# Example 8
start = time.time()

threads = []
for _ in range(5):
    thread = Thread(target=slow_systemcall)
    thread.start()
    threads.append(thread)


# Example 9
def compute_helicopter_location(index):
    pass

for i in range(5):
    compute_helicopter_location(i)

for thread in threads:
    thread.join()

end = time.time()
delta = end - start
print(f'Took {delta:.3f} seconds')
```

- GIL은 파이썬 프로그램이 병렬적으로 실행되지 못하게 막지만, 시스템 콜에는 영향을 끼칠 수 없다.
- 코드를 가급적 손보지 않고 블로킹 I/O를 병렬로 실행하고 싶을 때는 스레드를 사용하는 것이 가장 간편하다.

## [54.스레드에서 데이터 경합을 피하기 위해 Lock을 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_54.py)

GIL이 동시 접근을 보장해주는 락 역할을 하는 것처럼 보여도 실제로는 전혀 그렇지 않다. GIL은 여러분을 보호해주지 못한다. 파이썬 스레드는 한 번에 단 하나만 실행될 수 있지만, 파이썬 인터프리터에서 어떤 스레드가 데이터 구조에 대해 수행하는 연산은 연속된 두 바이트코드 사이에서 언제든 인터럽트될 수 있다. 여러 스레드가 같은 데이터 구조에 동시에 접근하면 위험하다. 이런 인터럽트로 인해 실질적으로는 언제든지 데이터 구조에 대한 불변 조건이 위반될 수 있고, 그에 따라 프로그램의 상태가 오염될 수 있다.

...

파이썬 인터프리터는 실행되는 모든 스레드를 강제로 공평하게 취급해서 각 스레드의 실행 시간을 거의 비슷하게 만든다. 이를 위해 파이썬은 실행 중인 스레드를 일시 중단시키고 다른 스레드를 실행시키는 일을 반복한다. 문제는 파이썬이 스레드를 언제 일시 중단시킬지 알 수 없다는 점이다. 심지어 원자적(atomic)인 것처럼 보이는 연산을 수행하는 도중에도 파이썬이 스레드를 일시 중단시킬 수 있다.

데이터 경합이나 다른 유형의 데이터 구조 오염을 해결하기 위해 파이썬은 threading 내장 모듈 안에 여러 가지 튼튼한 도구를 제공한다. 가장 간단하지만 유용한 도구로 Lock 클래스가 있다. Lock 클래스는 상호 배제 락(뮤텍스)이다.
다음 코드에서는 whit 문을 사용해 락을 획득하고 해제한다. 한 번에 단 하나의 스레드만 락을 획득할 수 있다.

```python
def worker(sensor_index, how_many, counter):
    # I have a barrier in here so the workers synchronize
    # when they start counting, otherwise it's hard to get a race
    # because the overhead of starting a thread is high.
    BARRIER.wait()
    for _ in range(how_many):
        # Read from the sensor
        # Nothing actually happens here, but this is where
        # the blocking I/O would go.
        counter.increment(1)


# Example 3
from threading import Barrier
BARRIER = Barrier(5)
from threading import Thread

how_many = 10**5
counter = Counter()

threads = []
```

```python
# Example 7
from threading import Lock

class LockingCounter:
    def __init__(self):
        self.lock = Lock()
        self.count = 0

    def increment(self, offset):
        with self.lock:
            self.count += offset


# Example 8
BARRIER = Barrier(5)
counter = LockingCounter()

for i in range(5):
    thread = Thread(target=worker,
                    args=(i, how_many, counter))
    threads.append(thread)
    thread.start()

for thread in threads:
    thread.join()

expected = how_many * 5
found = counter.count
print(f'Counter should be {expected}, got {found}')
```

## [55. Queue를 사용해 스레드 사이의 작업을 조율하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_55.py)

파이썬 프로그램이 동시에 여러 일을 수행한다면 각 작업을 잘 조율해야 한다. 동시성 작업을 처리할 떄 가장 유용한 방식은 함수 파이프라인이다.

...

각 단계마다 실행할 구체적인 함수가 정해진다. 각 함수는 동시에 실행될 수 있고 각 단계에서 처리해야 하는 일을 담당한다. 작업은 매 단계 함수가 완료될 때마다 다음 단계로 전달되며, 더 이상 실행할 단계가 없을 때 끝난다.

이런 접근 방법은 작업 처리에 블로킹 I/O나 하위 프로세스가 포함되는 경우에 특히 좋다. 파이썬에서는 블로킹 I/O나 하위 프로세스를 쉽게 병렬화할 수 있기 때문이다.

Queue는 새로운 데이터가 나타날 때까지 get 메서드가 블록되게 만들어서 작업자의 바쁜 대기(busy waiting) 문제를 해결한다. 예를 들어 다음 코드는 큐에 입력 데이터가 들어오기를 기다리는 스레드를 하나 시작한다.

```python
# Example 11
from threading import Thread
from queue import Queue
import time

my_queue = Queue()

def consumer():
    print('Consumer waiting')
    my_queue.get()              # Runs after put() below
    print('Consumer done')

thread = Thread(target=consumer)
thread.start()
```

위 스레드가 먼저 실행되지만, Queue 인스턴스에 원소가 put돼서 get 메서드가 반환할 원소가 생기기 전까지 이 스레드는 끝나지 않는다.

```python
# Example 12
print('Producer putting')
my_queue.put(object())          # Runs before get() above
print('Producer done')
thread.join()
```

파이프라인 중간이 막히는 경우를 해결하기 위해 Queue 클래스에서는 두 단계 사이에 허용할 수 있는 미완성 작업의 최대 개수를 지정할 수 있다. 

이렇게 버퍼 크기를 정하면 큐가 이미 가득 찬 경우 put이 블록된다. 예를 들어 다음 코드는 큐 원소가 소비될 때까지 대기하는 스레드를 정의한다.

```python
# Example 13
my_queue = Queue(1)             # Buffer size of 1

def consumer():
    time.sleep(0.1)             # Wait
    my_queue.get()              # Runs second
    print('Consumer got 1')
    my_queue.get()              # Runs fourth
    print('Consumer got 2')
    print('Consumer done')

thread = Thread(target=consumer)
thread.start()


# Example 14
my_queue.put(object())          # Runs first
print('Producer put 1')
my_queue.put(object())          # Runs third
print('Producer put 2')
print('Producer done')
thread.join()
```

Queue 클래스의 `task_done` 메서드를 통해 작업의 진행을 추적할 수 있다. 이 메서드를 사용하면 어떤 단계의 입력 큐가 다 소진될 때까지 기다릴 수 있고 파이프라인의 마지막 단계를 폴링할 필요도 없어진다.

예를 들어 다음 코드는 소비자 스레드가 자신의 작업을 하나 완료한 다음에 `task_done`을 호출하게 만든다.

```python
in_queue = Queue()

def consumer():
    print('Consumer waiting')
    work = in_queue.get()       # Done second
    print('Consumer working')
    # Doing work
    print('Consumer done')
    in_queue.task_done()        # Done third

thread = Thread(target=consumer)
thread.start()


# Example 16
print('Producer putting')
in_queue.put(object())         # Done first
print('Producer waiting')
in_queue.join()                # Done fourth
print('Producer done')
thread.join()
```

생산자는 Queue 인스턴스의 join 메서드를 호출함으로써 in_queue가 끝나기를 기다릴 수 있다. in_queue가 비어 있더라도 지금까지 이 큐에 들어간 모든 원소에 대해 task_done이 호출되기 전까지는 join이 끝나지 않는다.

이 모든 동작을 Queue 하위 클래스에 넣고, 처리가 끝났음을 작업자 스레드에게 알리는 기능을 추가할 수 있다. 다음 코드는 큐에 더 이상 다른 입력이 없음을 표시하는 센티넬(sentinel) 원소를 추가하는 close 메서드를 정의한다.

```python
# Example 17
class ClosableQueue(Queue):
    SENTINEL = object()

    def close(self):
        self.put(self.SENTINEL)

    def __iter__(self):
        while True:
            item = self.get()
            try:
                if item is self.SENTINEL:
                    return  # Cause the thread to exit
                yield item
            finally:
                self.task_done()
```

그 후 큐를 이터레이션하다가 이 특별한 object를 찾으면 이터레이션을 끝낸다. 그리고 이 `__iter__` 메서드는 큐의 작업 진행을 감시할 수 있게 하고자 task_done을 적당한 횟수만큼 호출해준다.

이제 작업자 스레드가 ClosableQueue 클래스의 동작을 활용할 수 있다. 이 스레드는 for 루프가 끝나면 종료된다.

```python
# Example 1
def download(item):
    return item

def resize(item):
    return item

def upload(item):
    return item


# Example 19
class StoppableWorker(Thread):
    def __init__(self, func, in_queue, out_queue):
        super().__init__()
        self.func = func
        self.in_queue = in_queue
        self.out_queue = out_queue

    def run(self):
        for item in self.in_queue:
            result = self.func(item)
            self.out_queue.put(result)


# Example 20
download_queue = ClosableQueue()
resize_queue = ClosableQueue()
upload_queue = ClosableQueue()
done_queue = ClosableQueue()
threads = [
    StoppableWorker(download, download_queue, resize_queue),
    StoppableWorker(resize, resize_queue, upload_queue),
    StoppableWorker(upload, upload_queue, done_queue),
]
```

작업자 스레드를 실행하고 첫 번째 단계의 입력 큐에 모든 입력 작업을 추가한 다음, 입력이 모두 끝났음을 표시하는 신호를 추가한다. 

```python
# Example 21
for thread in threads:
    thread.start()

for _ in range(1000):
    download_queue.put(object())

download_queue.close()
```

마지막으로 각 단계를 연결하는 큐를 join함으로써 작업 완료를 기다린다. 각 단계가 끝날 때마다 다음 단계의 입력 큐의 close를 호출해서 작업이 더이상 없음을 통지한다. 마지막 done_queue에는 예상대로 모든 출력이 들어 있다.

이 접근 방법을 확장해 단계마다 여러 작업자를 사용할 수 있다. 그러면 I/O 병렬성을 높일 수 있으므로 이런 유형에 속한 프로그램의 속도를 상당히 증가시킬 수 있다. 이를 위해 먼저 다중 스레드를 시작하고 끝내는 도우미 함수를 만든다. 

`stop_threads` 함수는 소비자 스레드의 입력 큐마다 close를 호출하는 방식으로 작동한다. 이렇게 하면 모든 작업자를 깔끔하게 종료시킬 수 있다.

```python
# Example 23
def start_threads(count, *args):
    threads = [StoppableWorker(*args) for _ in range(count)]
    for thread in threads:
        thread.start()
    return threads

def stop_threads(closable_queue, threads):
    for _ in threads:
        closable_queue.close()

    closable_queue.join()

    for thread in threads:
        thread.join()


# Example 24
download_queue = ClosableQueue()
resize_queue = ClosableQueue()
upload_queue = ClosableQueue()
done_queue = ClosableQueue()

download_threads = start_threads(
    3, download, download_queue, resize_queue)
resize_threads = start_threads(
    4, resize, resize_queue, upload_queue)
upload_threads = start_threads(
    5, upload, upload_queue, done_queue)

for _ in range(1000):
    download_queue.put(object())

stop_threads(download_queue, download_threads)
stop_threads(resize_queue, resize_threads)
stop_threads(upload_queue, upload_threads)

print(done_queue.qsize(), 'items finished')
```

## [56. 언제 동시성이 필요할지 인식하는 방법을 알아두라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_56.py)

- 동시성을 조율하는 일반적인 방법으로는 팬아웃(새로운 동시성 단위들을 만들어냄)과 팬인(기존 동시성 단위들의 실행이 끝나기를 기다림)이 있다.

```python
# Example 1
ALIVE = '*'
EMPTY = '-'


# Example 2
class Grid:
    def __init__(self, height, width):
        self.height = height
        self.width = width
        self.rows = []
        for _ in range(self.height):
            self.rows.append([EMPTY] * self.width)

    def get(self, y, x):
        return self.rows[y % self.height][x % self.width]

    def set(self, y, x, state):
        self.rows[y % self.height][x % self.width] = state

    def __str__(self):
        output = ''
        for row in self.rows:
            for cell in row:
                output += cell
            output += '\n'
        return output


# Example 3
grid = Grid(5, 9)
grid.set(0, 3, ALIVE)
grid.set(1, 4, ALIVE)
grid.set(2, 2, ALIVE)
grid.set(2, 3, ALIVE)
grid.set(2, 4, ALIVE)
print(grid)


# Example 4
def count_neighbors(y, x, get):
    n_ = get(y - 1, x + 0)  # North
    ne = get(y - 1, x + 1)  # Northeast
    e_ = get(y + 0, x + 1)  # East
    se = get(y + 1, x + 1)  # Southeast
    s_ = get(y + 1, x + 0)  # South
    sw = get(y + 1, x - 1)  # Southwest
    w_ = get(y + 0, x - 1)  # West
    nw = get(y - 1, x - 1)  # Northwest
    neighbor_states = [n_, ne, e_, se, s_, sw, w_, nw]
    count = 0
    for state in neighbor_states:
        if state == ALIVE:
            count += 1
    return count

alive = {(9, 5), (9, 6)}
seen = set()

def fake_get(y, x):
    position = (y, x)
    seen.add(position)
    return ALIVE if position in alive else EMPTY

count = count_neighbors(10, 5, fake_get)
assert count == 2

expected_seen = {
    (9, 5),  (9, 6),  (10, 6), (11, 6),
    (11, 5), (11, 4), (10, 4), (9, 4)
}
assert seen == expected_seen


# Example 5
def game_logic(state, neighbors):
    if state == ALIVE:
        if neighbors < 2:
            return EMPTY     # Die: Too few
        elif neighbors > 3:
            return EMPTY     # Die: Too many
    else:
        if neighbors == 3:
            return ALIVE     # Regenerate
    return state

assert game_logic(ALIVE, 0) == EMPTY
assert game_logic(ALIVE, 1) == EMPTY
assert game_logic(ALIVE, 2) == ALIVE
assert game_logic(ALIVE, 3) == ALIVE
assert game_logic(ALIVE, 4) == EMPTY
assert game_logic(EMPTY, 0) == EMPTY
assert game_logic(EMPTY, 1) == EMPTY
assert game_logic(EMPTY, 2) == EMPTY
assert game_logic(EMPTY, 3) == ALIVE
assert game_logic(EMPTY, 4) == EMPTY


# Example 6
def step_cell(y, x, get, set):
    state = get(y, x)
    neighbors = count_neighbors(y, x, get)
    next_state = game_logic(state, neighbors)
    set(y, x, next_state)

alive = {(10, 5), (9, 5), (9, 6)}
new_state = None

def fake_get(y, x):
    return ALIVE if (y, x) in alive else EMPTY

def fake_set(y, x, state):
    global new_state
    new_state = state

# Stay alive
step_cell(10, 5, fake_get, fake_set)
assert new_state == ALIVE

# Stay dead
alive.remove((10, 5))
step_cell(10, 5, fake_get, fake_set)
assert new_state == EMPTY

# Regenerate
alive.add((10, 6))
step_cell(10, 5, fake_get, fake_set)
assert new_state == ALIVE


# Example 7
def simulate(grid):
    next_grid = Grid(grid.height, grid.width)
    for y in range(grid.height):
        for x in range(grid.width):
            step_cell(y, x, grid.get, next_grid.set)
    return next_grid


# Example 8
class ColumnPrinter:
    def __init__(self):
        self.columns = []

    def append(self, data):
        self.columns.append(data)

    def __str__(self):
        row_count = 1
        for data in self.columns:
            row_count = max(
                row_count, len(data.splitlines()) + 1)

        rows = [''] * row_count
        for j in range(row_count):
            for i, data in enumerate(self.columns):
                line = data.splitlines()[max(0, j - 1)]
                if j == 0:
                    padding = ' ' * (len(line) // 2)
                    rows[j] += padding + str(i) + padding
                else:
                    rows[j] += line

                if (i + 1) < len(self.columns):
                    rows[j] += ' | '

        return '\n'.join(rows)

columns = ColumnPrinter()
for i in range(5):
    columns.append(str(grid))
    grid = simulate(grid)

print(columns)


# Example 9
def game_logic(state, neighbors):
    # Do some blocking input/output in here:
    data = my_socket.recv(100)
```

## [57. 요구에 따라 팬아웃을 진행하려면 새로운 스레드를 생성하지 말라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_57.py)

- 스레드에는 많은 단점이 있다. 스레드를 시작하고 실행하는 데 비용이 들기 때문에 스레드가 많이 필요하면 상당히 많은 메모리를 사용한다. 그리고 스레드 사이를 조율하기 위해 Lock 과 같은 특별한 도구를 사용해야 한다.
- 스레드를 시작하거나 스레드가 종료하기를 기다리는 코드에게 스레드 실행 중에 발생한 예외를 돌려주는 파이썬 내장 기능은 없다. 이로 인해 스레드 디버깅이 더 어려워진다.

## [58. 동시성과 Queue를 사용하기 위해 코드를 어떻게 리팩터링해야 하는지 이해하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_58.py)

- 작업자 스레드 수를 고정하고 Queue와 함께 사용하면 스레드를 사용할 때 팬인과 팬아웃의 규모 확장성을 개선할 수 있다.
- Queue를 사용하도록 기존 코드를 리팩터링하려면 상당히 많은 작업이 필요하다. 특히 다단계로 이뤄진 파이프라인이 필요하면 작업량이 더 늘어난다.
- 다른 파이썬 내장 기능이나 모듈이 제공하는 병렬 I/O를 가능하게 해주는 다른 기능과 비교하면, Queue는 프로글매이 활용할 수 있는 전체 I/O 병렬성의 정도를 제한한다는 단점이 있다.

## [59. 동시성을 위해 스레드가 필요한 경우에는 ThreadpoolExecutor를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_59.py)

- ThreadPoolExecutor를 사용하면 한정된 리팩터링만으로 간단한 I/O 병렬성을 활성화할 수 있고, 동시성을 팬아웃해야 하는 경우에 발생하는 스레드 시작 비용을 쉽게 줄일 수 있다.
- ThreadPoolExecutor를 사용하면 스레드를 직접 사용할 때 발생할 수 있는 잠재적인 메모리 낭비 문제를 없재주지만, max_workers의 개수를 미리 지정해야 하므로 I/O 병렬성을 제한한다.

## [60. I/O를 할 때는 코루틴을 사용해 동시성을 높여라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_60.py)

파이썬은 높은 I/O 동시성을 처리하기 위해 코루틴을 사용한다. 코루틴을 사용하면 파이썬 프로그램 안에서 동시에 실행되는 것처럼 보이는 함수를 아주 많이 쓸 수 있다.

코루틴은 async와 await 키워드를 사용해 구현되며, 제너레이터를 실행하기 위한 인프라를 사용한다.

코루틴을 시작하는 비용은 함수 호출뿐이다. 활성화된 코루틴은 종료될 때까지 1KB 미만의 메모리를 사용한다. 스레드와 마찬가지로 코루틴도 환경으로부터 입력을 소비하고 결과를 출력할 수 있는 독립적인 함수다.

코루틴과 스레드를 비교해보면, 코루틴은 매 await 식에서 일시 중단되고 일시 중단된 대기 가능성(awaitable)이 해결된 다음에 async 함수로부터 실행을 재개한다는 차이점이 있다.(이는 제너레이터의 yield 동작과 비슷하다.)

여러 분리된 async 함수가 서로 장단을 맞춰 실행되면 마치 모든 async 함수가 동시에 실행되는 것처럼 보여미, 이를 통해 파이썬 스레드와 달리 메모리 부가 비용이나 시작 비용, 컨텍스트 전환 비용이 들지 않고 복잡한 락과 동기화 코드가 필요하지 않다.

코루틴을 가능하게 하는 마법과 같은 메커니즘은 **이벤트 루프**로 다수의 I/O를 효율적으로 동시에 실행할 수 있고 이벤트 루프에 맞춰 작성된 함수들을 빠르게 전화해가며 골고루 실행할 수 있다.

```python
# Example 1
ALIVE = '*'
EMPTY = '-'

# 56번과 같다
class Grid:
    ...

def count_neighbors(y, x, get):
    ...

async def game_logic(state, neighbors):
    # Do some input/output in here:
    data = await my_socket.read(50)

async def game_logic(state, neighbors):
    if state == ALIVE:
        if neighbors < 2:
            return EMPTY     # Die: Too few
        elif neighbors > 3:
            return EMPTY     # Die: Too many
    else:
        if neighbors == 3:
            return ALIVE     # Regenerate
    return state
```

```python
# Example 2
async def step_cell(y, x, get, set):
    state = get(y, x)
    neighbors = count_neighbors(y, x, get)
    next_state = await game_logic(state, neighbors)
    set(y, x, next_state)


# Example 3
import asyncio

async def simulate(grid):
    next_grid = Grid(grid.height, grid.width)

    tasks = []
    for y in range(grid.height):
        for x in range(grid.width):
            task = step_cell(
                y, x, grid.get, next_grid.set)      # Fan out
            tasks.append(task)

    await asyncio.gather(*tasks)                    # Fan in

    return next_grid
```

step_cell을 호출해도 이 함수가 즉시 호출되지 않는다. 대신 step_cell 호출은 나중에 await 식에 사용할 수 있는 coroutine 인스턴스를 반환한다.

마치 yield를 사용하는 제너레이터 함수를 호출하면 즉시 실행되지 않고 제너레이터를 반환하는 것과 같다. 이와 같은 실행 연기 메커니즘이 팬아웃(새로운 동시성 단위를 만들어 냄)을 수행한다.

asyncio 내장 라이브러리가 제공하는 gather 함수는 팬인(기존 동시성 단위들의 실행이 끝나기를 기다림)을 수행한다. gather에 대해 적용한 await 식은 이벤트 루프가 step_cell 코루틴을 동시에 실행하면서 step_cell 코루틴이 완료될 때마다 simulate 코루틴 실행을 재개하라고 요청한다.

모든 실행이 단일 스레드에서 이뤄지므로 Grid 인스턴스에 락을 사용할 필요가 없다. I/O는 asyncio가 제공하는 이벤트 루프의 일부분으로 병렬화된다.

```python
# Example 4
class ColumnPrinter:
    def __init__(self):
        self.columns = []

    def append(self, data):
        self.columns.append(data)

    def __str__(self):
        row_count = 1
        for data in self.columns:
            row_count = max(
                row_count, len(data.splitlines()) + 1)

        rows = [''] * row_count
        for j in range(row_count):
            for i, data in enumerate(self.columns):
                line = data.splitlines()[max(0, j - 1)]
                if j == 0:
                    padding = ' ' * (len(line) // 2)
                    rows[j] += padding + str(i) + padding
                else:
                    rows[j] += line

                if (i + 1) < len(self.columns):
                    rows[j] += ' | '

        return '\n'.join(rows)

logging.getLogger().setLevel(logging.ERROR)

grid = Grid(5, 9)
grid.set(0, 3, ALIVE)
grid.set(1, 4, ALIVE)
grid.set(2, 2, ALIVE)
grid.set(2, 3, ALIVE)
grid.set(2, 4, ALIVE)

columns = ColumnPrinter()
for i in range(5):
    columns.append(str(grid))
    grid = asyncio.run(simulate(grid))   # Run the event loop

print(columns)
```

스레드와 관련한 모든 부가 비용이 제거됐다. Queue나 ThreadPoolExecutor 접근 방법은 예외 처리에 한계가 있었지만(예외를 스레드 경계에서 다시 발생시켜야 했다.), 코루틴에서는 대화형 디버거를 사용해 코드를 한 줄씩 실행시켜볼 수 있다.

```python
logging.getLogger().setLevel(logging.DEBUG)


# Example 5
try:
    async def game_logic(state, neighbors):
        raise OSError('Problem with I/O')
    
    logging.getLogger().setLevel(logging.ERROR)
    
    asyncio.run(game_logic(ALIVE, 3))
    
    logging.getLogger().setLevel(logging.DEBUG)
except:
    logging.exception('Expected')
else:
    assert False
```

코루틴은 코드에서 외부 환경에 대한 명령(예: I/O)과 원하는 명령을 수행하는 방법을 구현하는 것(예: 이벤트 루프)을 분리해준다는 점이 멋지다.
코루틴을 사용하면 원하는 목표를 동시성을 사용해 달성하는 방법을 알아내는 데 시간을 낭비하는 대신, 여러분이 만들고 싶은 로직을 작성하는 데 초점을 맞출 수 있다.


## Link

- [effective python](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=254321728)

