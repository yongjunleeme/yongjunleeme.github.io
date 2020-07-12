---
layout  : wiki
title   : 
summary : 
date    : 2020-07-10 12:28:05 +0900
updated : 2020-07-12 23:52:32 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 설치 in mac

```python
$ redisurl="http://download.redis.io/redis-stable.tar.gz"
$ curl -s -o redis-stable.tar.gz $redisurl
$ sudo su root
$ mkdir -p /usr/local/lib/
$ chmod a+w /usr/local/lib/
$ tar -C /usr/local/lib/ -xzf redis-stable.tar.gz
$ rm redis-stable.tar.gz
$ cd /usr/local/lib/redis-stable/
$ make && make install
```

- 확인

```python
$ redis-cli --version

$ # A snapshot of executables that come bundled with Redis
$ ls -hFG /usr/local/bin/redis-* | sort
```

### 설정

```python
$ sudo su root
$ mkdir -p /etc/redis/
$ touch /etc/redis/6379.conf
```

- `vim /etc/redis/6379.conf` 아래 내용 기록
    - 더 많은 설정은 redis.conf file을 참고
    - daemonize - 백그라운드에서 서버구동 여부


```python
# /etc/redis/6379.conf

port              6379
daemonize         yes
save              60 1
bind              127.0.0.1
tcp-keepalive     300
dbfilename        dump.rdb
dir               ./
rdbcompression    yes
```

```python
$ redis-server /etc/redis/6379.conf
```

### Redis REPL

```python
$ redis-cli
```

```python
127.0.0.1:6379> PING
PONG
```

### 서버 체크

```python
$ pgrep redis-server
```

- kill server

```python
$ redis-cli shutdown
```

## 기본 명령어

### 키 밸류

- 레디스는 리모트 딕셔너리 서비스

#### [SET](https://redis.io/commands/set)

```python
127.0.0.1:6379> SET Bahamas Nassau
OK

127.0.0.1:6379> SET Croatia Zagreb
OK

127.0.0.1:6379> GET Croatia
"Zagreb"

127.0.0.1:6379> GET Japan
(nil)
```

- 위의 레디스와 비슷한 파이썬
- `capitals['Japan']`을 쓰지 않고 `capitals.get('Japan')`을 쓴 이유는 레디스는 키를 찾을 수 없을 때 에러보다 `nil`을 리턴하기 때문이다. 

```python
>>> capitals = {}
>>> capitals["Bahamas"] = "Nassau"
>>> capitals["Croatia"] = "Zagreb"
>>> capitals.get("Croatia")
'Zagreb'
>>> capitals.get("Japan")  # None
```

#### [MGET](https://redis.io/commands/mget)

- 하나의 커맨드에 멀티플 키 밸류

```python
127.0.0.1:6379> MSET Lebanon Beirut Norway Oslo France Paris
OK

127.0.0.1:6379> MGET Lebanon Norway Bahamas
1) "Beirut"
2) "Oslo"
3) "Nassau"
```

- 파이썬의 `dict.update()`와 비슷
- `.__gititem__()`이 아니라 `.get()`을 쓴 이유는 레디스가 no key를 찾을 때 null-like 값을 리턴하기 때문

```python
...     "Lebanon": "Beirut",
...     "Norway": "Oslo",
...     "France": "Paris",
... })
>>> [capitals.get(k) for k in ("Lebanon", "Norway", "Bahamas")]
['Beirut', 'Oslo', 'Nassau']
```

#### [EXISTS](https://redis.io/commands/exists)

- 키가 존재하는지 체크

```python
127.0.0.1:6379> EXISTS Norway
(integer) 1

127.0.0.1:6379> EXISTS Sweden
(integer) 0
```

```python
>>> "Norway" in capitals
True

>>> "Sweden" in capitals
False
```

- 참고
- [Redis Protocol specification](https://redis.io/topics/protocol)
- [redis-cli, the Redis command line interface](https://redis.io/topics/rediscli)

### 다양한 데이터 타입

#### Hash

- 가장 위에 있는 키 아래 키 밸류 구조로 매핑된 형태?

```python
127.0.0.1:6379> HSET realpython url "https://realpython.com/"
(integer) 1

127.0.0.1:6379> HSET realpython github realpython
(integer) 1

127.0.0.1:6379> HSET realpython fullname "Real Python"
(integer) 1
```

- 하나의 키에 세 개의 키 밸류 페어

```python
data = {
    "realpython": {
        "url": "https://realpython.com/",
        "github": "realpython",
        "fullname": "Real Python",
    }
}
```

#### [HMSET](https://redis.io/commands/hmset)

```python
127.0.0.1:6379> HMSET pypa url "https://www.pypa.io/" github pypa fullname "Python Packaging Authority"
OK

127.0.0.1:6379> HGETALL pypa
1) "url"
2) "https://www.pypa.io/"
3) "github"
4) "pypa"
5) "fullname"
6) "Python Packaging Authority"
```

#### [lists](https://redis.io/topics/data-types-intro#redis-lists)

- L or R이 붙는 것은 방향을 뜻
- B가 붙는 것은 Blocking. 블로킹 오퍼레이션은 실행 중에 다른 오퍼레이션에게 인터럽트될 수 없다.

### 참고할 공식문서들

#### [sets](https://redis.io/topics/data-types-intro#redis-sets)

#### [geospatial items](https://redis.io/commands#geo)

#### [sorted sets](https://redis.io/commands#sorted_set)

#### [HyperLogLog](https://redis.io/commands#hyperloglog)

#### [commands](https://redis.io/commands)

#### [data types summary](https://redis.io/topics/data-types)

#### Database Clear

- [FLUSHDB](https://redis.io/commands/flushdb)

```python
127.0.0.1:6379> FLUSHDB
OK

127.0.0.1:6379> QUIT
```

## [redis-py](https://github.com/andymccurdy/redis-py)

### First Step

```python
$ python -m pip install redis
```

- 레디스 서버가 백그라운드에서 돌고 있는지 확인

```python
$ pgrep redis-server

# empty-handed가 나온다면 restart
$ redis-server /etc/redis/6379.conf
```

- 파이썬에서 Hello World

```python
>>> import redis
>>> r = redis.Redis()
>>> r.mset({"Croatia": "Zagreb", "Bahamas": "Nassau"})
True
>>> r.get("Bahamas")
b'Nassau'
```

- 6라인의 b'Nassau'는 파이썬의 [bytes](https://docs.python.org/3/library/stdtypes.html#bytes) 이지 스트링이 아니다. `r.get("Bahamas").decode("utf-8")`을 호출해야 원하는 값을 얻을 수 있을 것이다.
- redis-py 명령어는 레디스 커맨드와 비슷
    - `r.mset()`, `r.get()`은 레디스 API의 MSET, GET과 같다.
    - `r.hgetall()`은 HGETALL
    - [몇 가지 예외](https://github.com/andymccurdy/redis-py#api-reference)
- 아래 예제에는 인자가 없지만 사용 가능한 인자는 [여기](https://github.com/andymccurdy/redis-py/blob/b940d073de4c13f8dfb08728965c6ac7c183c935/redis/client.py#L605)를 참고
    - localhost:6397은 redis 서버 인스턴스를 로컬에서 유지할 때 필요한 hostnam:port 페어
    - db 파라미터는 데이터베이스 이름
        - 다수의 데이터베이스 운영 가능하며 각각 int로 식별
        - 최대치는 16
        - redis-cli로 구동하면 데이터베이스는 0으로 시작
        - 변경을 원하면 -n을 사용,  `redis-cli -n 5`

```python
# From redis/client.py
class Redis(object):
    def __init__(self, host='localhost', port=6379,
                 db=0, password=None, socket_timeout=None,
                 # ...
```

### Allowed Key Types

- 서버로 보내기 전에 모두 bytes 타입으로 변환

```python
>>> import datetime
>>> today = datetime.date.today()
>>> visitors = {"dan", "jon", "alex"}
>>> r.sadd(today, *visitors)
Traceback (most recent call last):
# ...
redis.exceptions.DataError: Invalid input of type: 'date'.
Convert to a byte, string or number first.
```

- `.isformat()`을 사용해서 오브젝트를 스트링으로 변환
- 레디스는 키로 오직 스트링만을 허용하지만 redis-py는 좀더 자유로운 편이다. 하지만 결국 레디스 서버로 보내기 전에 bytes로 변환해야 한다.

```python
>>> stoday = today.isoformat()  # Python 3.7+, or use str(today)
>>> stoday
'2019-03-10'
>>> r.sadd(stoday, *visitors)  # sadd: set-add
3
>>> r.smembers(stoday)
{b'dan', b'alex', b'jon'}
>>> r.scard(today.isoformat())
3
```

### Example: PyHats.com

- 세 가지 리미티드 에디션 모자를 판매한다는 가정
- 각 모자는 레디스 키 밸류 페어 해시를 사용
- 해시 키는 랜덤 int로 prefixed(hat:56854717)
- hat:prefix는 레디스 컨벤션으로 레디스 데이터베이스 안에 네임스페이스를 생성한다

```python
import random

random.seed(444)
hats = {f"hat:{random.getrandbits(32)}": i for i in (
    {
        "color": "black",
        "price": 49.99,
        "style": "fitted",
        "quantity": 1000,
        "npurchased": 0,
    },
    {
        "color": "maroon",
        "price": 59.99,
        "style": "hipster",
        "quantity": 500,
        "npurchased": 0,
    },
    {
        "color": "green",
        "price": 99.99,
        "style": "baseball",
        "quantity": 200,
        "npurchased": 0,
    })
}
```

- 이전에 0을 썼으므로 1로 세팅

```python
>>> r = redis.Redis(db=1)
```

- `.hmset()`(hash multi-set)을 사용해 각 딕셔너리를 호출
- 'field'는 nested 딕셔너리 중 어떤 키에도 일치한다
- [pipelining](https://redis.io/topics/pipelining) 은 레디스 서버에서 데이터를 읽거나 쓸 때 round-trip transaction을 없애는 방법
    - 만약 `r.hmset()`을 세 번 호출하면 각 로우를 3번 앞뒤로 왔다갔다 해야 하지만
    - `pipe.hmset()`을 사용하면 파이프라인에서 모든 커맨드는 클라이언트 사이드에 버퍼되고 파이프를 사용해 한 번에 보내진다.
    - [Redis Mass Insertion](https://redis.io/topics/mass-insert)

```python
>>> with r.pipeline() as pipe:
...    for h_id, hat in hats.items():
...        pipe.hmset(h_id, hat)
...    pipe.execute()
Pipeline<ConnectionPool<Connection<host=localhost,port=6379,db=1>>>
Pipeline<ConnectionPool<Connection<host=localhost,port=6379,db=1>>>
Pipeline<ConnectionPool<Connection<host=localhost,port=6379,db=1>>>
[True, True, True]

>>> r.bgsave()
True
```

- 레디스 데이터베이스 내부 체크

```python
>>> pprint(r.hgetall("hat:56854717"))
{b'color': b'green',
 b'npurchased': b'0',
 b'price': b'99.99',
 b'quantity': b'200',
 b'style': b'baseball'}

>>> r.keys()  # Careful on a big DB. keys() is O(N)
[b'56854717', b'1236154736', b'1326692461']
```

- 유저가 구매버튼을 클릭했을 때 어떤 일이 일어날까?
- 재고에서 구매량이 1 늘어나고 수량이 1 줄어든다
- `.hincrby()`를 사용
    - [hincrby](https://redis.io/commands/hincrby)
    - 스트링을 base-10 64비트 signed integer로 interpret
    - 다른 데이터 구조에서 증가, 감소와 관련된 커맨드를 적용하는 것과 같다(INCR, INCRBY, INCRBYFLOAT, ZINCRBY, and HINCRBYFLOAT)
    - 스트링이 int로 표현되지 않은면 에러가 날 것

```python
>>> r.hincrby("hat:56854717", "quantity", -1)
199
>>> r.hget("hat:56854717", "quantity")
b'199'
>>> r.hincrby("hat:56854717", "npurchased", 1)
1
```

#### Pyhat.com Part 1

- step 1 : 재고에 아이템이 있는지 또는 백엔드에서 다른 방법으로 exception을 raise하는지 체크
    - `.hget()`으로 재고 체크
- step 2 : 재고가 있다면 트랜잭션을 샐행해서 수량을 줄이고 구매량을 늘린다.
    - [transcation](https://redis.io/topics/transactions) 블록을 사용(all or nothing)
    - 레디스 파이프라인은 transactinoal pipline 클래스가 디폴트
    - MULTI로 시작, EXEC로 끝
- step 3 : 1, 2 스템 사이의 인벤토리 내 변화를 알려라.([race condition](https://realpython.com/python-concurrency/#threading-version))

```python
127.0.0.1:6379> MULTI
127.0.0.1:6379> HINCRBY 56854717 quantity -1
127.0.0.1:6379> HINCRBY 56854717 npurchased 1
127.0.0.1:6379> EXEC
```

- step 3 : 유저 A가 남은 하나의 모자를 확인하고 트랜스액션이 처리되는 동시에 유저 B도 하나의 모자가 남은 것을 확인한다면? 두 유저에게 모두 구매가 허용되는 곤란한 상황
- [optimistic locking](https://en.wikipedia.org/wiki/Optimistic_concurrency_control) 데이터베이스를 잠그는 것과 달리 데이터의 변화를 모니터링하는 것. 만약 충돌이 난다면 함수는 전체 과정을 다시 시도한다.
    - `.watch()`를 사용하는 최적화된 locking이 [check-and-set](https://redis.io/topics/transactions#cas) 을 제공
- 유저가 buy now나 구매 버튼을 클릭하면 언제든 `buyitem()`을 호출
- race condition을 감지하고 재시도할 수 있는 안전한 방법

```python
import logging
import redis

logging.basicConfig()

class OutOfStockError(Exception):
    """Raised when PyHats.com is all out of today's hottest hat"""

def buyitem(r: redis.Redis, itemid: int) -> None:
    with r.pipeline() as pipe:
        error_count = 0
        while True:
            try:
                # Get available inventory, watching for changes
                # related to this itemid before the transaction
                pipe.watch(itemid) ##
                nleft: bytes = r.hget(itemid, "quantity") ##
                if nleft > b"0":
                    pipe.multi()
                    pipe.hincrby(itemid, "quantity", -1)
                    pipe.hincrby(itemid, "npurchased", 1)
                    pipe.execute()
                    break
                else:
                    # Stop watching the itemid and raise to break out
                    pipe.unwatch()
                    raise OutOfStockError(
                        f"Sorry, {itemid} is out of stock!"
                    )
            except redis.WatchError:
                # Log total num. of errors by this user to buy this item,
                # then try the same process again of WATCH/HGET/MULTI/EXEC
                error_count += 1
                logging.warning(
                    "WatchError #%d: %s; retrying",
                    error_count, itemid
                )
    return None
```

- `pipe.watch(itemid)`는 값의 변화를 모니터링
- `r.hget(itemid, "quantity")`를 통해 인벤토리를 체크
- 유저가 재고를 체크하고 구매를 시도할 때 다른 유저가 인벤토리에 접근한다면 레디스는 에러를 리턴하고 redis-py는 WatchError를 발생시킨다. 
- `.hincrby()`(Line 20, 21)를 호출 하기 전 `.hget()`을 호출해 itemid의 변화가 일어난다면 whilt true의 전체 프로세스를 다시 시작한다.
- 데이터베이스 getting과 setting 오퍼레이션을 통해 클라이언트에게 타임 컨슈밍 토털 락을 거는 것보다 레디스가 클라이언트에게 알려주고 유저는 인벤토리를 다시 체크하는 것이 최적화된 방법
- 아래는 클라이언트 측과 서버 측 오퍼레이션 사이의 차이를 이해하는 키

```python
nleft = r.hget(itemid, "quantity")
```

- 파이썬 할당은 `r.hget()`을 클라이언트측으로 가져온다. 반대로 파이프를 호출하는 메소드는 커맨드를 하나로 만들어 효율적인 버퍼를 수행한다.

```python
pipe.multi()
pipe.hincrby(itemid, "quantity", -1)
pipe.hincrby(itemid, "npurchased", 1)
pipe.execute()
```

- 클라이언트는 `pipe.hincrby(itemid, "quantity", -1)`의 결과를 즉시 사용할 수 없는데 이유는 파이프라인의 메소드가 파이프 인스턴스 자체를 리턴하기 때문이다.
- `hincrby()`가 결과를 반환하는 반면 트랜잭션이 마칠 때까지 클라인터 사이드는 참조할 수 없다.
- 만약 `hget()`을 썼다면 실시간 결과를 얻을 수 없었을 
- 결국 인벤토리가 0일 때 item idㅇ를 UNWATCH하고 OutOfStockError 발생

```python
else:
    # Stop watching the itemid and raise to break out
    pipe.unwatch()
    raise OutOfStockError(
        f"Sorry, {itemid} is out of stock!"
    )
```

#### Using Key Expiry

- 키를 삭제하면 대응되는 값도 자동적으로 삭제 설정

```python
>>> from datetime import timedelta

>>> # setex: "SET" with expiration
>>> r.setex(
...     "runner",
...     timedelta(minutes=1),
...     value="now you see me, now you don't"
... )
True
```

- 남은 시간 측정

```python
>>> r.ttl("runner")  # "Time To Live", in seconds
58
>>> r.pttl("runner")  # Like ttl, but milliseconds
54368
```

#### PyHats.com, Part 2

- 수백 개의 아이템을 몇 초 내에 봇으로 구매하는 유저 등장
- 멀티플 HTTPS 커넥션의 IP 주소 스트림을 처리하고 소비자(or watcher)와 같은 클라이언트를 생성
- watcher는 IP 주소의 스트림을 모니터링하는데 짧은 시간 내 의심스러운 요청 등을 감시
- 미들웨어는 모든 들어오는 IP addresses를 레디스 리스트의 `.lpush()`로 밀어 넣는다. 

```python
>>> r = redis.Redis(db=5)
>>> r.lpush("ips", "51.218.112.236")
1
>>> r.lpush("ips", "90.213.45.98")
2
>>> r.lpush("ips", "115.215.230.176")
3
>>> r.lpush("ips", "51.218.112.236")
4
```

- `.lpush()`는 리스트의 길이를 리턴
- 같은 클라이언트의 요청을 시뮬레이션
- 아래는 다른 목적의 클라언트를 생성, 무한 루프, ips 리스트를 blocking left-pop [BLPOP](https://redis.io/commands/blpop)


```python
# New shell window or tab

import datetime
import ipaddress

import redis

# Where we put all the bad egg IP addresses
blacklist = set()
MAXVISITS = 15

ipwatcher = redis.Redis(db=5)

while True:
    _, addr = ipwatcher.blpop("ips")
    addr = ipaddress.ip_address(addr.decode("utf-8"))
    now = datetime.datetime.utcnow()
    addrts = f"{addr}:{now.minute}"
    n = ipwatcher.incrby(addrts, 1)
    if n >= MAXVISITS:
        print(f"Hat bot detected!:  {addr}")
        blacklist.add(addr)
    else:
        print(f"{now}:  saw {addr}")
    _ = ipwatcher.expire(addrts, 60)
```

- ipwatcher는 새로운 IPs가 레디스 리스트 ips에 푸시되는 것을 기다리는 consumer와 같다
- b”51.218.112.236”와 같이 bytes로된 주소를 받아 적절한 address object으로 만든다

```python
_, addr = ipwatcher.blpop("ips")
addr = ipaddress.ip_address(addr.decode("utf-8"))
```

- 주소와 시간(분)을 스크링 키로 만들고
- ipwatcher가 증가하는 응답을 카운트

```python
now = datetime.datetime.utcnow()
addrts = f"{addr}:{now.minute}"
n = ipwatcher.incrby(addrts, 1)
```

- MAXVISITS보다 주소가 많아지면 웹스크래퍼가 [tulip bubble](https://en.wikipedia.org/wiki/Tulip_mania)을 만드는 것과 같다.
- `ipwatcher.exper(addrts, 60)`으로 60초 후 expire하는 것은 수상한one-time page viewers를 막기 위해?

```python
2019-03-11 15:10:41.489214:  saw 51.218.11236
2019-03-11 15:10:41.490298:  saw 115.215.230.176
2019-03-11 15:10:41.490839:  saw 90.213.45.98
2019-03-11 15:10:41.491387:  saw 51.218.112.236
```

- `blpop()`(BLPOP)은 아이템이 리스트에서 이용 가능할 떄까지 블록한 다음 이를 꺼낼 것인데 파이썬의 [Queue.get()](https://docs.python.org/3/library/queue.html#queue.Queue.get)과 비슷하다. 
- ipwatcher는 주어진 시간 내에 15회 이상 요청을 보내는 hot-bot의 IP 주소를 분류할 것이다.

```python
for _ in range(20):
    r.lpush("ips", "104.174.118.18")
```

```python
2019-03-11 15:15:43.041363:  saw 104.174.118.18
2019-03-11 15:15:43.042027:  saw 104.174.118.18
2019-03-11 15:15:43.042598:  saw 104.174.118.18
2019-03-11 15:15:43.043143:  saw 104.174.118.18
2019-03-11 15:15:43.043725:  saw 104.174.118.18
2019-03-11 15:15:43.044244:  saw 104.174.118.18
2019-03-11 15:15:43.044760:  saw 104.174.118.18
2019-03-11 15:15:43.045288:  saw 104.174.118.18
2019-03-11 15:15:43.045806:  saw 104.174.118.18
2019-03-11 15:15:43.046318:  saw 104.174.118.18
2019-03-11 15:15:43.046829:  saw 104.174.118.18
2019-03-11 15:15:43.047392:  saw 104.174.118.18
2019-03-11 15:15:43.047966:  saw 104.174.118.18
2019-03-11 15:15:43.048479:  saw 104.174.118.18
Hat bot detected!:  104.174.118.18
Hat bot detected!:  104.174.118.18
Hat bot detected!:  104.174.118.18
Hat bot detected!:  104.174.118.18
Hat bot detected!:  104.174.118.18
Hat bot detected!:  104.174.118.18
```

- 콘트롤 C를 누르고 blacklist를 쳐보면 IP 주소가 블랙리스트에 올라간 것을 확인


```python
>>> blacklist
{IPv4Address('104.174.118.18')}
```

- [ClassDojo](https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/) 에서 소티드 셋을 사용한 Crafty 솔루션이 있다.

#### Persistence and Snapshotting

- 레디스는 메모리에서 읽고 쓰기 때문에 빠르지만 디스크에 저장도 가능 [snapshotting](https://redis.io/topics/persistence#snapshotting)
    - 주된 용도는 파이너리 포맷으로 백업, 데이터는 재구조화될 수 있다.
    - 필요할 때 메모리로 다시 불러올 수 있다.
- 아래 `save` 항목이 디스크에 저장하는 설정
    - `save <second> <changes>`

```python
# /etc/redis/6379.conf

port              6379
daemonize         yes
save              60 1
bind              127.0.0.1
tcp-keepalive     300
dbfilename        dump.rdb
dir               ./
rdbcompression    yes
```

- 위 설정은 아래 sample Redis config file에 비해서는 공격적인 설정

```python
# Default redis/redis.conf
save 900 1
save 300 10
save 60 10000
```

- RDB 스냅샷은 전체 데이터베이스 캡처
- dbfilename, dir, rdbcompression으로 이름, 위치 설정

```python
# /etc/redis/6379.conf

port              6379
daemonize         yes
save              60 1
bind              127.0.0.1
tcp-keepalive     300
dbfilename        dump.rdb
dir               ./
rdbcompression    yes
```

```python
$ file -b dump.rdb
data
```

- [BGSAVE](https://redis.io/commands/bgsave)로 저장도 가능

```python
127.0.0.1:6379> BGSAVE
Background saving started
```

- 확인

```python
>>> r.lastsave()  # Redis command: LASTSAVE
datetime.datetime(2019, 3, 10, 21, 56, 50)
>>> r.bgsave()
True
>>> r.lastsave()
datetime.datetime(2019, 3, 10, 22, 4, 2)
```

- RDB 스냅샷은 부모 프로세스가 [fork()](http://man7.org/linux/man-pages/man2/fork.2.html)를 통해 자식 프로세스가 디스크에 저장되도록 하므로 빠른 수행이 가능하다. BGSAVE도 마찬가지
- SAVE는 그렇지 않으므로 특별한 이유가 아니면 쓰지 말아야
- RDB의 대안으로 [AOP(append-only-file)](https://redis.io/topics/persistence#append-only-file)
    - 레디스 커맨드를 디스크에 실시간으로 저장
    - 필요하면 커맨드 기반으로 다시 시스템을 만들 수 있다

#### Serialization Workarounds

- 레디스 해시와 파이썬 딕셔너리 비교

```python
127.0.0.1:6379> hset mykey field1 value1

r.hset("mykey", "field1", "value1")
```

1


- 레디스에서 리스트나 네스티드 딕셔너리 같은 값을 호출하려면?
    - restaurant_484272가 네스티드되어 있으므로 오류

```python
restaurant_484272 = {
    "name": "Ravagh",
    "type": "Persian",
    "address": {
        "street": {
            "line1": "11 E 30th St",
            "line2": "APT 1",
        },
        "city": "New York",
        "state": "NY",
        "zip": 10016,
    }
}
```

```python
>>> r.hmset(484272, restaurant_484272)
Traceback (most recent call last):
# ...
redis.exceptions.DataError: Invalid input of type: 'dict'.
Convert to a byte, string or number first.
```

- 레디스에서 네스티드 키를 통해 호출할 두 가지 방법
    - `json.dumps()`와 같이 값을 스트링으로 시리얼라이즈
    - delimiter를 사용

##### Option 1: Serialize the Values Into a String

```python
>>> import json
>>> r.set(484272, json.dumps(restaurant_484272))
True
```

- `.get()`을 호출하면 bytes 오브젝트가 리턴되므로 deserialize해야
- `json.dumps()`와 `json.loads()`는 반대, 각각 시리얼라이징과 디시리얼라이징

```python
>>> from pprint import pprint
>>> pprint(json.loads(r.get(484272)))
{'address': {'city': 'New York',
             'state': 'NY',
             'street': '11 E 30th St',
             'zip': 10016},
 'name': 'Ravagh',
 'type': 'Persian'}
```

- 다른 시리얼라이즈 프로토콜인 [ymal](https://github.com/yaml/pyyaml)

```python
>>> import yaml  # python -m pip install PyYAML
>>> yaml.dump(restaurant_484272)
'address: {city: New York, state: NY, street: 11 E 30th St, zip: 10016}\nname: Ravagh\ntype: Persian\n'
```

##### Option 2: Use a Delimiter in Key Strings

```python
from collections.abc import MutableMapping

def setflat_skeys(
    r: redis.Redis,
    obj: dict,
    prefix: str,
    delim: str = ":",
    *,
    _autopfix=""
) -> None:
    """Flatten `obj` and set resulting field-value pairs into `r`.

    Calls `.set()` to write to Redis instance inplace and returns None.

    `prefix` is an optional str that prefixes all keys.
    `delim` is the delimiter that separates the joined, flattened keys.
    `_autopfix` is used in recursive calls to created de-nested keys.

    The deepest-nested keys must be str, bytes, float, or int.
    Otherwise a TypeError is raised.
    """
    allowed_vtypes = (str, bytes, float, int)
    for key, value in obj.items():
        key = _autopfix + key
        if isinstance(value, allowed_vtypes):
            r.set(f"{prefix}{delim}{key}", value)
        elif isinstance(value, MutableMapping):
            setflat_skeys(
                r, value, prefix, delim, _autopfix=f"{key}{delim}"
            )
        else:
            raise TypeError(f"Unsupported value type: {type(value)}")
```

```python
>>> r.flushdb()  # Flush database: clear old entries
>>> setflat_skeys(r, restaurant_484272, 484272)

>>> for key in sorted(r.keys("484272*")):  # Filter to this pattern
...     print(f"{repr(key):35}{repr(r.get(key)):15}")
...
b'484272:address:city'             b'New York'
b'484272:address:state'            b'NY'
b'484272:address:street:line1'     b'11 E 30th St'
b'484272:address:street:line2'     b'APT 1'
b'484272:address:zip'              b'10016'
b'484272:name'                     b'Ravagh'
b'484272:type'                     b'Persian'

>>> r.get("484272:address:street:line1")
b'11 E 30th St'
```

#### Encryption

- 레디스 서버로 보내기 전 대칭 암호화 
- [cryptography](https://github.com/pyca/cryptography/) 예시
- 민감한 cardholder data (CD) 데이터를 어느 서버에도 플레인텍스트로 저장하지 말아야
- 레디스에 캐시로 저장하기 전에 시리얼라이즈할 수 있고 [Fernet](https://cryptography.io/en/latest/fernet/) 으로 암호화
    - CBC 모델에서 AES 128 암호화. [cryptograpgy docs](https://cryptography.io/en/latest/hazmat/primitives/symmetric-encryption/)

```python
$ python -m pip install cryptography
```

```python
>>> import json
>>> from cryptography.fernet import Fernet

>>> cipher = Fernet(Fernet.generate_key())
>>> info = {
...     "cardnum": 2211849528391929,
...     "exp": [2020, 9],
...     "cv2": 842,
... }

>>> r.set(
...     "user:1000",
...     cipher.encrypt(json.dumps(info).encode("utf-8"))
... )

>>> r.get("user:1000")
b'gAAAAABcg8-LfQw9TeFZ1eXbi'  # ... [truncated]

>>> cipher.decrypt(r.get("user:1000"))
b'{"cardnum": 2211849528391929, "exp": [2020, 9], "cv2": 842}'

>>> json.loads(cipher.decrypt(r.get("user:1000")))
{'cardnum': 2211849528391929, 'exp': [2020, 9], 'cv2': 842}
```

- info에 리스트가 포함되어 있으므로 스트링으로 시리얼라이즈
- cipher 오브젝트를 사용해서 암호화, 복호화
- 복호화된 bytes를 `json.loads()`로 디시리얼라이즈해서 dict로 변환

#### Compression

- 비용 효율적인 대역폭에 관심이 있다면 손실없는 compression과 decompression을 고려
- 아래 bzip2 compression 알고리즘으로 2000개가 넘는 팩터에 의한 커넥션으로 가는 bytes의 수를 줄일 수 있다?

```python
>>> import bz2

>>> blob = "i have a lot to talk about" * 10000
>>> len(blob.encode("utf-8"))
260000

>>> # Set the compressed string as value
>>> r.set("msg:500", bz2.compress(blob.encode("utf-8")))
>>> r.get("msg:500")
b'BZh91AY&SY\xdaM\x1eu\x01\x11o\x91\x80@\x002l\x87\'  # ... [truncated]
>>> len(r.get("msg:500"))
122
>>> 260_000 / 122  # Magnitude of savings
2131.1475409836066

>>> # Get and decompress the value, then confirm it's equal to the original
>>> rblob = bz2.decompress(r.get("msg:500")).decode("utf-8")
>>> rblob == blob
True
```

#### Using Hiredis

- redis-py는 [REdis Serialization Protocol](https://redis.io/topics/protocol) 또는 RESP를 따른다
- 로우 바이트스트링에서 파이썬 오브젝트로 컨버팅
    - "OK" -> "+OK\r\n"
- [RESP arrays](https://redis.io/topics/protocol#resp-arrays) 와 같은 다른 데이터 타입보다 복잡
- 파싱이 궁금하다면 [.read_response()](https://github.com/andymccurdy/redis-py/blob/cfa2bc9/redis/connection.py#L289)
- C 라이브러리 [Hiredis](https://github.com/redis/hiredis) 는 LRANGE와 같은 레디스 커맨스의 속도를 높여줄 것
    - 여기서는 파이썬 래퍼로 Hiredis의 부분인 [hiredis-py](https://github.com/redis/hiredis-py) 사용

```python
$ python -m pip install hiredis
```

- 파이썬 파서 대신 HiredisParser를 사용

```python
# redis/utils.py
try:
    import hiredis
    HIREDIS_AVAILABLE = True
except ImportError:
    HIREDIS_AVAILABLE = False


# redis/connection.py
if HIREDIS_AVAILABLE:
    DefaultParser = HiredisParser
else:
    DefaultParser = PythonParser
```

#### Using Enterprise Redis Applications

- [Amazon ElastiCache for Redis](https://docs.aws.amazon.com/ko_kr/AmazonElastiCache/latest/red-ug/WhatIs.html)
- [Microsoft’s Azure Cache for Redis](https://azure.microsoft.com/en-us/services/cache/)
- 엔드포인트에 DNS를 넣는다 
- AWS 

```python
$ export REDIS_ENDPOINT="demo.abcdef.xz.0009.use1.cache.amazonaws.com"
$ redis-cli -h $REDIS_ENDPOINT
```

- Azure
- [SSL(port 6380)](https://docs.microsoft.com/en-us/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) 을 사용 

```python
$ export REDIS_ENDPOINT="demo.redis.cache.windows.net"
$ redis-cli -h $REDIS_ENDPOINT -p 6380 -a <primary-access-key>
```

- -h flag는 호스로 로컬호스트(127.0.0.1)이 디폴트

```python
>>> import os
>>> import redis

>>> # Specify a DNS endpoint instead of the default localhost
>>> os.environ["REDIS_ENDPOINT"]
'demo.abcdef.xz.0009.use1.cache.amazonaws.com'
>>> r = redis.Redis(host=os.environ["REDIS_ENDPOINT"])
```

- `r.get()` 등의 커맨드 사용 가능

#### 참고

- [server-side Lua scripting](https://redis.io/commands/eval)
- [sharding](https://redis.io/topics/partitioning)
- [master-slave replication](https://redis.io/topics/replication)
- [updated protocol, RESP3](http://antirez.com/news/125)

## Link

- [realpython python-redis](https://realpython.com/python-redis/)
