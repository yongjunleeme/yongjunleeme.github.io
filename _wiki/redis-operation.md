---
layout  : wiki
title   : 
summary : 
date    : 2020-07-10 12:26:02 +0900
updated : 2020-07-10 12:34:57 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

### 캐시
 
#### 인메모리를 쓰는 이유 

- 애플리케이션 레벨의 로컬캐시가 빠르지만 두 가지 문제
    - 데이터유실
    - 데이터동기화
        - 서버가 두 대만 되어도 하나가 바뀌면 다른 서버에 어떻게 알려주냐부터 이슈
- 그래서 인메모리 캐시를 외부에 놓고 참조한다
    - 네트워크를 통하므로 로컬보다 느리지만 위 두 문제가 없다

#### 캐시가 유실되면?

- 인메모리는 모든 데이터가 디스크가 아닌 메모리에 올라가 있어
    - 유실될 수도 있으므로 캐시
    - 유실안 되는 개념은 스토어
- 캐시가 날라가면 디비로 가는데 준비를 안 해 놓아서 디비가 못 버티는 경우가 실제 꽤 많이 일어난다
    - 썬더링 커즈 - 캐시 키가 하나만 사라져도 십만 리퀴스트 일어나면 디비로 간다
예) 트위터 - 하이라키 캐시구조 1, 2, 3차까지, 비싸므로 파레토 법칙 적용해서
- 캐시 key가 하나가 없어진 사이에 이거에 요청이 몰리는 경우, DB가 죽을 수 있다


#### DB에 두고 공유하는 거랑, Redis 등의 in memory DB 서버를 따로 두고 쓰는 거랑 어떤 차이가 있는지?

- 디스크에 접근하면 초당 1천번 정도라면(DB는 디스크를 쓰게 되어 있다), 메모리에 접근하면 10만번이 가능 (in memory DB는 메모리만 쓴다)

#### 캐시에 데이터를 불러올 때

- 해시구조 키 밸류 (멤캐시드는 이것만 지원)
- 그 안에 셋
- 아니면 소티드 셋 

#### Cache를 스토리지처럼 쓸 수 있는 Data grid

- 레디스도 클러스터 있지만 클러스터보다 하나의 서버 개념
- 캐시 스토리지처럼 쓸 수 있다는 데이터그리드로 넘어가는 것이 새로운 경향?
    - 해이즐캐스트, 인피니스팬
    - 데이터 리플리케이션을 잘 전보다 복잡하고 잘 구현하는 개념


#### 네트워크에서 상용으로 쓰는 레디스

- 기본설정으로 쓰면 장애로 이어질 것
    - 테스트 옵션으로 되어 있기 떄문
- 엘라스틱 캐시 관리 이슈
    - 싱글 스레드라 긴 작업이 들어가면 장애

#### 캐시에 올릴 데이터 기준

- 잘 안 바뀌는데 자주 접근하는 데이터
    - SNS는 거의 캐시로 돌아가는 서비스

#### 어떤 데이터가 많이 잡히는지 볼 수 있는 방법?

- 태그 히트률을 가져와서 따로 테이블에 저장
- key마다 태그를 달아서 어떤 데이터가 많이 생성되는지 볼 수 있다. 혹은 read마다 update

#### Redis를 서버 동기화 목적?

- Redis를 서버 동기화 목적으로 쓰는 경우? 메시지큐 pub/sub
- 가볍다. sub이 없으면 pub해도 그냥 없어지므로, 보통 list 자료구조를 많이 쓴다.
- 그외 용도로 Redis를 활용하는 방법?
    - 다양한 자료구조를 지원하므로
        - 소티드셋 - 랭킹에 쓰기 좋다.
        - API limitter - 초당 몇 개만 써라

#### 레디스 랭킹API에 쓰면

- 보통 랭킹은 디비에 유저 스코어 저장하고 오더 바이로 정렬 후 읽어오기
- 개수가 많아지면 속도문제 발생
- 레디스는 소티드셋을 쓰면 랭킹 구현 가능
    - 덤으로 리플리케이션도 가능
    - 다만 한계에 종속될 수도
        - 저장할 id가 1개당 100byte라고 하면
            - 10명 1k
            - 10000명 1M
            - 10조면 1TB

#### 레디스 장점

- 레디스 자료구조가 Atomic하기 때문에 해당 Race Condition을 피할 수 있다.
    - 그래도 잘못 짜면 발생함


#### 레디스 사용처

- 리모트 데이터 스토어
    - A서버, B서버, C서버에서 데이터를 공유하고 싶을 때
- 한 대에서만 필요하다면 전역 변수를 쓰면 되지 않을까?
    - 레디스 자체가 Atomic을 보장해준다.
- 주로 많이 쓰는 곳들
    - 인증 토큰 통을 저장
    - 랭킹 보드로 사용
    - 유저 API Limit
    - 잡큐
        - 리시트로 많이 쓴다

#### Redis Collections

- Strings(키, 밸류)
    - 기본사용법
        - Set <Key> <Value>
        - Get <Key>
        - mset <key1> <value1> <key2> <value2> ...
        - mget <key1 <key2> ...
- List(삽입 시 헤드와 테일은 빠르지만 중간은 느리다)
    - 기본사용법
        - Lpush <Key> <A>
            - Key: <A>
        - Rpush <Key> <B>
            - Key: <A, B>
        - Lpush <Key> <C>
            - Key: <C, A, B>
        - pop
            - 꺼내올 떄, LPOP, RPOP
- Set
    - 기본사용법
        - SADD <Key> <Value>
            - Value가 이미 Key에 있으면 추가되지 않는다.
        - SMEMBERS <Key>
            - 모든 Value를 돌려줌
                - '모든'이 들어간 명령은 주의해서 써야
        - SISMEMBER <Key> <Value>
            - value가 존재하면 1, 없으면 0
    - 보통 특정 유저를 Follow하는 목록을 저장할 때 많이 쓴다. 유니크하므로
- Sorted Set
    - Set은 순서가 없지만 Sorted Set은 Score를 줘서 순서를 보장할 수 있다
    - 기본사용법
        - ZADD <Key> <Score> <Value>
            - Value가 이미 Key에 있으면 해당 Score로 변경된다.
            - 스코어 값 순서대로 정렬되어서 저장된다.
        - ZRANGE <Key> <StartIndex> <EndIndex>
            - 해당 Index 범위 값을 모두 돌려줌
            - Zrange testkey 0 -1
                - 모든 범위를 가져옴
                - 번호가 인덱스, -1이 끝을 의미
    - 유저 랭킹보드로 사용할 수 있음
    - 소티드 셋의 스코어는 정수형이 아니라 더블 타입이므로 주의해야
        - 실수가 표현할 수 없는 정수값들이 존재
        - 간혹 발생하는 내가 왜 그랬을까의 대표적 사례
    - Sorted Sets - 정렬이 필요한 값이 필요하다면?
        - `select * from rank order by score limit 50, 20;`
            - zrange rank 50 70
        - `select * from rank order by score desc limit 50, 20;`
            - Zrevrange rank 50 70
                - 뒤에서부터 순서를 매긴다.
- Hash
    - Key 밑에 sub key가 존재
    - 키밸류 안에 다시 키밸류가 존재
    - 기본 사용법
        - Hmset <Key> <subkey1> <value1> <subkey2> <value2>
        - Hgetall <Key>
            - 해당 key의 모든 subkey와 value를 가져옴
        - Hget <Key> <subkey>
        - Hmget <Key> <subkey1> <subkey2> ...

#### Collection 주의사항

- 하나의 컬렉션에 너무 많은 아이템을 담으면 좋지 않음
    - 10000개 이하 몇천 개 수준으로 유지하는 게 좋음
- Expire는 Collection의 item 개별로 걸리지 않고 전체 Collection에 대해서만 걸림
    - Expire - 삭제
    - 즉 해당 10000개의 아이템을 가진 Collection에 expire가 걸려 있다면 그 시간 후에 10000개의 아이템이 모두 삭제

#### 레디스 운영

- 메모리 관리를 잘하자
    - In-memory이므로 피지컬 메모리 이상 사용하면 당연히 문제 발생
        - 바로 죽지 않고 Swap 일어난다
        - Swap - 메모리 꽉차면 디스크로 내리고 비면 다시 메모리로 올리고
        - Swap이 한 번이라도 있다면 해당 메모리 Page 접근 시마다 느려짐
    - Maxmemory를 설정하더라도 이보다 더 사용할 가능성이 큼
        - jemalloc? 때문에 레디스는 자기가 사용하는 메모리 크기를 정확하게 알 수 없기 때문에
    - RSS 값을 모니터링해야 함
    - 메모리 관리
        - 큰 모메로 사용하는 인스턴스 하나보다 적은 메모리 인스턴스 여러 개가 더 안전하다.
            - 라이트가 헤비한 레디스는 포크를 해서 최대 메모리를 2배까지 쓸 수 있다.
            - 24기가면 48기가를 소모, 8기가면 16기가를 소모
    - 메모리 부족할 때는?
        - 좀더 메모리 많은 장비로 Migration
        - 있는 데이터 줄이기
            - 다만 Swap 사용중이라면 프로세스 재시작해야 함
        - 메모리 줄이기 위한 설정
            - 기본적인 Collection 틀
                - Hash -> HashTable
                - Sorted Set -> Skeplist와 HashTable
                - Set -> HashTable
                - 해당 자료 구조들은 메모리를 많이 사용함
            - Ziplist를 이용하자
                - In-memory 특성상 적은 개수라면 선형 탐색을 하더라도 빠르다.
                - List, hash, sorted set 등을 ziplist로 대체해서 처리함는 설정이 존재
- O(N) 관련 명령어는 주의하자
    - 싱글스레드라 한 번에 한 명령만 처리
        - 긴 시간이 필요한 명령을 수행하면? 망한다.
        - 초당 10만TPS 처리하는데 1개만 1초 걸려도 끝장
    - 대표적인 O(N) 명령어들
        - KEYS
            - 한 번에 모든 데이터 가져오는 명령
            - scan 명령으로 하나의 긴 명령을 짧은 여러 번 명령으로 바꿀 수 있다
        - FLUSHALL, FLUSHDB
        - Delete Collections
        - Get All Collections
            - Collection의 모든 item을 가져와야 할 때?
                - Collection의 일부만 거져오거나
                    - Sorted Set
                - 큰 Collection을 작은 여러 개의 Collection으로 나눠서 저장
                    - Userranks -> Userrank1, Userrank2, Userrank3
                    - 하나당 몇천 개 안쪽으로 저장하는 게 좋음

#### Redis Replication

- Async Replication
    - Replication Lag이 발생할 수 있다.
    - A 데이터 바뀌면 B 데이터에게 쏴주는데 그 틈에 생긴 변화
    - 많이 벌어지면 슬레이브가 마스터와 연결을 끊어버린 후 일정 기간 이후 다시 연결되면서 부하
- `'Replicaof'(>=5.0.0) or 'slaveof'` 명령으로 설정 가능(마스터, 슬레이브가 바뀐 용어)
    - Replicaof hostname port
- DBMS로 보면 statement replication이 유사
- Replication 설정 과정
    - Secondary에 replicaof or slaveof 명령을 전달
        - 마스터의 호스트 IP, 도메인 어드레스, 포트
    - Secondary는 Primary에 sync 명령 전달
    - Primary는 현재 메모리 상태를 저장하기 위해
        - Fork
    - Fork한 프로세서는 현재 메모리 정보를 디스크에 덤프
    - 해당 정보를 secondary에 전달
    - Fork 이후의 데이터를 Secondary에 계속 전달
- Redis Replication 시 주의할 점
    - Replication 과정에서 포크가 발생하므로 메모리 부족이 발생할 수 있다
    - Redis-cli --rdb 명령은 현재 상태의 메모리 스냅샷을 가져오므로 같은 문제를 발생시킴
    - AWS나 클라우드의 Redis는 좀 다르게 구현되어서 해당 부분이 좀더 안정적
    - 많은 대수의 레디스 서버가 Replica를 두고 있다면
        - 네트워크 이슈나 사람의 작업으로 동시에 replication이 재시도되도록 하면 문제가 발생할 수 있다
        - ex) 같은 네트웍 안에 30GB를 쓰는 Redis Master 100대 정도가 리클리케이션을 동시에 재시작하면 어떤 일이 벌어질 수 있을까?

#### redis.conf 권장 설정 Tip

- Maxclient 설정 50000
- RDB/AOF 설정 off
- 특정 커맨드 disable
    - Keys
    - AWS의 ElasticCache는 이미 하고 있음
- 전체 장애의 90% 이상이 KEYS와 SAVE 설정을 사용해서 발생

#### Redis 데이터 분산

- 데이터의 특성에 따라 선택할 수 있는 방법이 달라진다.
    - Cache일 때는 우아한 Redis?
    - Persistent 해야만 안 우아한 Redis!
        - Open the hellgate
- 데이터 분산 방법
    - Application
        - Consistent Hashing
            - twemproxy를 사용하는 방법으로 쉽게 사용 가능
        - Sharding
    - Redis Cluster

#### 참고할만한 sample 설정?

- save 끄기
    - 기본값: 5분에 1만번 이상 업데이트하면 모든걸 디스크로 내림.
    - 테스트는 괜찮은데 실서비스에서 장애날 가능성 농후
    - elasticache에서는 아예 못 쓰게 막았다
    - [troubleshooting redis 참고](https://www.slideshare.net/charsyam2/troubleshooting-redis)

#### Cache에 대한 새로운 접근법/논문?

- Cache를 썼을 때 생기는 문제점. Look-Aside caching
- read/write 발생빈도에 따라 range 조절가능> 좀더 적은 수의 cache 서버로 같은 트래픽을 처리할 수 있다. adaptive c???
- hot data는 memory에, cold data는 ssd에.


#### 15년차 개발자로서 다른 분들에게 말하고 싶은 것

- 오픈소스를 쓴다면 코드를 꼭 봐라.
- 알고 넘어가는 것/ 에러를 근본적으로 해결하는 태도가 중요하다

## Link

- [강대명님 강의](https://www.slideshare.net/charsyam2/presentations)
