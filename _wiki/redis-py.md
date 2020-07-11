---
layout  : wiki
title   : 
summary : 
date    : 2020-07-10 12:28:05 +0900
updated : 2020-07-11 10:49:08 +0900
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

- 6라인의 b'Nassau'는 파이썬의 [bytes](https://docs.python.org/3/library/stdtypes.html#bytes) 타입이지 스트링이 아니다. 이에 `r.get("Bahamas").decode("utf-8")`을 호출해야 원하는 값을 얻을 수 있을 것이다.
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
    - 스트링을 base-10 64비트 signed integer로 interpret
    - 이는 다른 데이터 구조에서 증가, 감소와 관련된 커맨드를 적용하는 것과 같다(INCR, INCRBY, INCRBYFLOAT, ZINCRBY, and HINCRBYFLOAT)
    - 스트링이 int로 표현되지 않은면 에러가 날 것

```python
>>> r.hincrby("hat:56854717", "quantity", -1)
199
>>> r.hget("hat:56854717", "quantity")
b'199'
>>> r.hincrby("hat:56854717", "npurchased", 1)
1
```

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
- 유저가 재고를 체크하고 구매를 시도할 때 인벤토리에 접근한다면 레디스는 레러를 리턴하고 redis-py는 WatchError를 발생시킨다. 
- `.hincrby()`(Line 20, 21)를 호출 하기 전 `.hget()`을 호출해 itemid의 변화가 일어난다면 whilt true의 결과로서 전체 프로세스를 다시 시작한다.
- 데이터베이스 getting과 setting 오퍼레이션을 통해 클라이언트에게 타임 컨슈밍 토털 락을 거는 것보다 레디스가 클라이언트에게 알려주고 유저는 인벤토리를 다시 체크하는 것
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
- `hncrby()`가 결과를 반환하는 반면 클라인턴 사이트의 전ㅌ체 트랜잭션이 마칠 때까지 즉시 참조를 할 수 없다?
- There’s a catch-22: this is also why you can’t put the call to .hget() into the transaction block. If you did this, then you’d be unable to know if you want to increment the npurchased field yet, since you can’t get real-time results from commands that are inserted into a transactional pipeline.
- Finally, if the inventory sits at zero, then we UNWATCH the item ID and raise an OutOfStockError (Line 27), ultimately displaying that coveted Sold Out page that will make our hat buyers desperately want to buy even more of our hats at ever more outlandish prices:


## Link

- [realpython python-redis](https://realpython.com/python-redis/)
