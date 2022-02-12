---
layout  : wiki
title   : 
summary : 
date    : 2022-02-12 23:52:15 +0900
updated : 2022-02-12 23:52:15 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## [65. try/except/else/finally의 각 블록을 잘 활용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_65.py)

### finally 블록

예외를 호출 스택의 위(함수 자신을 호출한 함수 쪽)로 전달해야 하지만, 예외가 발생하더라도 정리 코드를 실행해야 한다면 `try/finally`를 사용하라.

파일 핸들을 안전하게 닫기 위해 try/finally를 사용하는 경우가 자주 있다.

```python
# Example 1
def try_finally_example(filename):
    print('* Opening file')
    handle = open(filename, encoding='utf-8') # May raise OSError
    try:
        print('* Reading data')
        return handle.read()  # May raise UnicodeDecodeError
    finally:
        print('* Calling close()')
        handle.close()        # try 블록이 실행된 다음에는 항상 이 블록이 실행됨
```

read 메서드에서 예외가 발생하면 항상 try_finally_example을 호출한 쪽으로 이 예외가 전달되지만, finally 블록에 있는 handle의  close 메서드가 먼저 호출된다.

```python
# Example 2
try:
    filename = 'random_data.txt'
    
    with open(filename, 'wb') as f:
        f.write(b'\xf1\xf2\xf3\xf4\xf5')  # Invalid utf-8
    
    data = try_finally_example(filename)
    # This should not be reached.
    import sys
    sys.exit(1)
except:
    logging.exception('Expected')
else:
    assert False
```

파일을 열 때 발생하는 오류(파일이 없는 경우 발생하는 OSError 등)는 finally 블록을 전혀 거치지 않고 호출한 쪽에 전달돼야 하므로 try블록 앞에서 open을 호출해야 한다.

### else 블록

코드에서 처리할 예외와 호출 스택을 거슬러 올라가며 전달할 예외를 명확히 구분하기 위해 `try/catch/else`를 사용하라.

try 블록이 예외를 발생시키지 않으면 else 블록이 실행된다. else 블록을 사용하면 try 블록 안에 들어갈 코드를 최소화할 수 있다. try 블록에 들어가는 코드가 줄어들면 발생할 여지가 있는 예외를 서로 구분할 수 있으므로 가독성이 좋아진다.

예를 들어, 문자열에서 JSON 딕셔너리 데이터를 읽어온 후 어떤 키에 해당하는 값을 반환하고 싶다고 하자.

```python
import json

def load_json_key(data, key):
    try:
        print('* Loading JSON data')
        result_dict = json.loads(data)  # May raise ValueError
    except ValueError as e:
        print('* Handling ValueError')
        raise KeyError(key) from e
    else:
        print('* Looking up key')
        return result_dict[key]         # May raise KeyError
```

성공적으로 실행되는 경우, JSON 데이터가 try 블록 안에서 디코딩된 다음에 else 블록 안에서 키 검색이 일어난다.

```python
# Example 5
assert load_json_key('{"foo": "bar"}', 'foo') == 'bar'
```

키 검색에서 예외가 발생하면, try 블록 외부이므로 호출자에게 이 예외가 전달된다. else절은 try/except 뒤에 따라오는 코드를 except 블록과 시각적으로 구분해준다.

이렇게 하면 예외가 전파되는 방식을 (소스 코드에서) 더 명확히 볼 수 있다.

```python
load_json_key('{"foo": "bar"}', '존재하지 않음')
```

### 모든 요소를 한꺼번에 사용하기

복합적인 문장 안에 모든 요소를 다 넣고 싶다면 `try/except/else/finally`를 사용하라.

예를 들어 수행할 작업에 대한 설명을 파일에서 읽어 처리한 다음, 원본 파일 자체를 변경하고 싶다. 이 경우 try 블록을 사용해 파일을 읽고 처리하며, try 블록 안에서 발생할 것으로 예상되는 예외를 처리하고자 except 블록을 사용한다. 

else 블록을 사용해 원본 파일의 내용을 변경하고, 이 과정에서 오류가 생기면 호출한 쪽에 예외를 돌려준다. finally 블록은 파일 핸들을 닫는다.

```python
# Example 8
UNDEFINED = object()
DIE_IN_ELSE_BLOCK = False

def divide_json(path):
    print('* Opening file')
    handle = open(path, 'r+')   # May raise OSError
    try:
        print('* Reading data')
        data = handle.read()    # May raise UnicodeDecodeError
        print('* Loading JSON data')
        op = json.loads(data)   # May raise ValueError
        print('* Performing calculation')
        value = (
            op['numerator'] /
            op['denominator'])  # May raise ZeroDivisionError
    except ZeroDivisionError as e:
        print('* Handling ZeroDivisionError')
        return UNDEFINED
    else:
        print('* Writing calculation')
        op['result'] = value
        result = json.dumps(op)
        handle.seek(0)          # May raise OSError
        if DIE_IN_ELSE_BLOCK:
            import errno
            import os
            raise OSError(errno.ENOSPC, os.strerror(errno.ENOSPC))
        handle.write(result)    # May raise OSError
        return value
    finally:
        print('* Calling close()')
        handle.close()          # Always runs
```

- 정상적인 경우 try, else, finally 블록이 실행된다.
- 계산이 잘못된 경우 try, except, finally 블록은 실행되지만 else 블록은 실행되지 않는다.


```python

# Example 9
temp_path = 'random_data.json'

with open(temp_path, 'w') as f:
    f.write('{"numerator": 1, "denominator": 10}')

assert divide_json(temp_path) == 0.1


# Example 10
with open(temp_path, 'w') as f:
    f.write('{"numerator": 1, "denominator": 0}')

assert divide_json(temp_path) is UNDEFINED


# Example 11
try:
    with open(temp_path, 'w') as f:
        f.write('{"numerator": 1 bad data')
    
    divide_json(temp_path)
except:
    logging.exception('Expected')
else:
    assert False


# Example 12
try:
    with open(temp_path, 'w') as f:
        f.write('{"numerator": 1, "denominator": 10}')
    DIE_IN_ELSE_BLOCK = True
    
    divide_json(temp_path)
except:
    logging.exception('Expected')
else:
    assert False
```

...

- else 블록에서 결과 데이터를 파일에 다시 쓰는 동안 예외가 발생하면, 예상대로 finally 블록이 실행돼 파일 핸들을 닫아준다.


## [66. 재사용 가능한 try/finally 동작을 원한다면 contextlib과 with 문을 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_66.py)

파이썬의 with 은 코드가 특별한 컨텍스트(context) 안에서 실행되는 경우를 표현한다. 예를 들어 상호 배제 락(뮤텍스)을 with 문 안에서 사용하면 락을 소유했을 때만 코드 블록이 실행되다는 것을 의미한다.

```python
from threading import Lock

lock = Lock()
with lock:
    # Do something while maintaining an invariant
    pass
```

Lock 클래스가 with문을 적절히 활성화해주므로 위 예제는 다음 try/finally 구조와 동등하다.

```python
# Example 2
lock.acquire()
try:
    # Do something while maintaining an invariant
    # 어떤 불변 조건을 유지하면서 작업을 수행한다.
    pass
finally:
    lock.release()
```

이 경우에는 with문 쪽이 낫다. try/finally 구조를 반복적으로 사용할 필요가 없고, acquire에 대응하는 release를 실수로 빠뜨리는 경우를 방지할 수 있기 때문이다.

contextlib 내장 모듈을 사용하면 여러분이 만든 객체나 함수를 with 문에서 쉽게 쓸 수 있다. contextlib 모듈은 with 문에 쓸 수 있는 함수를 간단히 만들 수 있는 contextmanager 데코레이터를 제공한다.

예를 들어 어떤 코드 영역에서 디버깅 관련 로그를 더 많이 남기고 싶다고 하자. 다음 코드는 두 단계의 심각성 수준에서 디버깅 로그를 남기는 함수를 정의한다.

```python
# Example 3
import logging
logging.getLogger().setLevel(logging.WARNING)

def my_function():
    logging.debug('Some debug data')
    logging.error('Error log here')
    logging.debug('More debug data')


# Example 4
my_function()
```

프로그램의 디폴트 로그 수준은 WARNING이다. 따라서 이 함수를 실행하면 에러 메시지만 화면에 출력된다.

컨텍스트 매니저를 정의하면 이 함수의 로그 수준을 일시적으로 높일 수 있다. 이 도우미 함수는 with 블록을 실행하기 직전에 로그 심각성 수준을 높이고, 블록을 실행한 직후에 심각성 수준을 이전 수준으로 회복시켜준다.

```python
# Example 5
from contextlib import contextmanager

@contextmanager
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield
    finally:
        logger.setLevel(old_level)
```

yield 식은 with 블록의 내용이 실행되는 부분을 지정한다. with 블록 안에서 발생한 예외는 어떤 것이든 yield 식에 의해 yield 식에 의해 다시 발생되기 때문에 이 예외를 도우미 함수(이 경우는 `debug_logging`) 안에서 잡아낼 수 있다.

이제 같은 로그 함수를 호출하되, 이번에는 debug_logging 컨텍스트 안에서 실행하자. with 블록 안에서는 화면에 모든 로그 메시지가 출력된다. with 블록 밖에서 같은 함수를 실행하면 디버그 수준의 메시지는 화면에 출력되지 않는다.

```python
# Example 6
with debug_logging(logging.DEBUG):
    print('* Inside:')
    my_function()

print('* After:')
my_function()
```

### with와 대상 변수 함께 사용하기

with 문에 전달된 컨텍스트 매니저가 객체를 반환할 수도 있다. 이렇게 반환된 객체는 with 복합문의 일부로 지정된 지역 변수에 대입된다. 이를 통해 with 블록 안에서 실행되는 코드가 직접 컨텍스트 객체와 상호작용할 수 있다.

예를 들어 파일을 작성하고 이 파일이 제대로 닫혔는지 확인하고 싶다고 하자. with 문에 open을 전달하면 이렇게 할 수 있다. open은 with 문에서 as를 통해 대상으로 지정된 변수에게 파일 핸들을 전달하고, with 블록에서 나갈 때 이 핸들을 닫는다.

```python
with open('my_output.txt', 'w') as handle:
    handle.write('This is some data!')
```

이 방식을 사용하면 코드 실행이 with 문을 벗어날 때 결국에는 파일이 닫힌다고 확신할 수 있다.

여러분이 만든 함수가 as 대상 변수에게 값을 제공하도록 하기 위해 필요한 일은 컨텍스트 매니저 안에서 yield에 값을 넘기는 것뿐이다. 예를 들면, 다음과 같이 Logger 인스턴스를 가져와서 로그 수준을 설정하고 yield로 대상을 전달하는 컨텍스트 매니저를 만들 수 있다.

```python
# Example 8
@contextmanager
def log_level(level, name):
    logger = logging.getLogger(name)
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield logger
    finally:
        logger.setLevel(old_level)
```

with의 as 대상 변수로 얻은 로그 객체에 대해 debug와 같은 로그 관련 메서드를 호출하면, with 블록 내의 로그 심각성 수준이 낮게 설정돼 있으므로 디버깅 메시지가 출력된다.

하지만 디폴트 로그 심각성 수준이 WARNING이기 때문에 logging 모듈을 직접 사용해 debug 로그 메서드를 호출하면 아무 메시지도 출력되지 않는다.

```python
with log_level(logging.DEBUG, 'my-log') as logger:
    logger.debug(f'This is a message for {logger.name}!')
    logging.debug('This will not print')
```

with 문이 끝날 때 로그 심각성 수준이 원래대로 복구되므로, with 문 밖에서 my-log라는 로거에 대해 debug를 통해 메시지를 출력해도 아무 메시지가 표시되지 않는다. 하지만 error로 출력한 로그는 항상 출력된다.

```python
# Example 10
logger = logging.getLogger('my-log')
logger.debug('Debug will not print')
logger.error('Error will print')
```

나중에 with 문을 바꾸기만 하면 로거 이름을 바꿀 수 있다. 이렇게 로거 이름을 바꾼 경우 with의 as 대상 변수가 가리키는 Logger가 다른 인스턴스를 기리키게 되지만, 이에 맞춰 다른 코드를 변경할 필요는 없다.

```python
# Example 11
with log_level(logging.DEBUG, 'other-log') as logger:
    logger.debug(f'This is a message for {logger.name}!')
    logging.debug('This will not print')
```

이런 식으로 상태를 격리할 수 있고, 컨텍스트를 만드는 부분과 컨텍스트 안에서 실행되는 코드를 서로 분리할 수 있다는 것이 with 문의 또 다른 장점이다.

- with 문을 사용하면 try/finally 블록을 통해 사용해야 하는 로직을 재활용하면서 시각적인 잡음도 줄일 수 있다.
- contextlib 내장 모듈이 제공하는 contextmanager 데코레이터를 사용하면 여러분이 만든 함수를 with 문에 사용할 수 있다.
- 컨텍스트 매니저가 yield하는 값은 with 문의 as 부분에 전달된다. 이를 활용하면 특별한 컨텍스트 내부에서 실행되는 코드 안에서 직접 그 컨텍스트에 접근할 수 있다.

## [67. 지역 시간에는 time보다는 datetime을 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_67.py)

파이썬에서 시간대를 변환하는 방법은 두 가지다. 예전 방식은 time 내장 모듈을 사용하는데, 프로그래머가 실수하기 쉽다. 새로운 방식은 datetime 내장 모듈을 사용하며, 파이썬 커뮤니티에서 만들어진 pytz라는 패키지를 활용하면 아주 잘 작동한다.

문제는 time 모듈이 플랫폼에 따라 다르게 작동한다는 데 있다. time 모듈의 동작은 호스트 운영체제의 C 함수가 어떻게 작동하는지에 따라 달라진다. 이로 인해 파이썬에서는 time 모듈의 동작을 신뢰할 수 없다.

여러 시간대에서 time 모듈이 일관성 있게 동작한다고 보장할 수 없으므로 여러 시간대를 다뤄야 하는 경우에는 time 모듈을 사용하면 안 된다.

### datetime 모듈

다음 코드는 UTC로 된 시간을 컴퓨터의 지역 시간인 KST로 바꾼다.

```python
# Example 5
from datetime import datetime, timezone

now = datetime(2019, 3, 16, 22, 14, 35)
now_utc = now.replace(tzinfo=timezone.utc)
now_local = now_utc.astimezone()
print(now_local)
```

또한 datetime 모듈을 사용하면 지역 시간을 UTC로 된 유닉스 타임스탬프로 쉽게 바꿀 수 있다.

```python
# Example 6
time_str = '2019-03-16 15:14:35'
now = datetime.strptime(time_str, time_format)  # 시간대 설정이 안 된 시간으로 문자열을 구문 분석
time_tuple = now.timetuple()  # 유닉스 시간 구조체로 변환
utc_now = time.mktime(time_tuple)  # 구조체로부터 유닉스 타임스탬프 생성
print(utc_now)
```

time 모듈과 달리 datetime 모듈은 한 지역 시간을 다른 지역 시간으로 바꾸는 신뢰할 수 있는 기능을 제공한다. 하지만 datetime은 자신의 tzinfo 클래스와 이 클래스 안에 들어 있는 메서드에 대해서만 시간대 관련 기능을 제공한다.

파이썬 기본 설치에는 UTC를 제외한 시간대 정의가 들어 있지 않다. 다행히 파이썬 패키지 인덱스에서 pytz 모듈을 내려받아 기본 설치가 제공하지 않는 시간대 정보를 추가할 수 있다.

pytz를 효과적으로 사용하려면 항상 지역 시간을 UTC로 먼저 바꿔야 한다. 그 후 UTC 값에 대해 여러분이 필요로 하는 datetime 연산(오프셋 연산 등)을 수행하라. 마지막으로 UTC를 지역시간으로 바꿔라.

예를 들어 다음 코드는 샌프란시스코의 비행기 도착 시간을 UTC datetime으로 변경한다. 각 함수 호출 중에는 불필요해 보이는 부분도 있겠지만, pytz를 사용하려면 이 모든 호출이 필요하다.

```python
# Example 7
import pytz

arrival_nyc = '2019-03-16 23:33:24'
nyc_dt_naive = datetime.strptime(arrival_nyc, time_format)
eastern = pytz.timezone('US/Pacific')  # 샌프란시스코
nyc_dt = eastern.localize(nyc_dt_naive)  # 시간대를
utc_dt = pytz.utc.normalize(nyc_dt.astimezone(pytz.utc))  # UTC로 변경
print(utc_dt)
```

일단 UTC datetime을 얻으면, 이를 한국 지역 시간으로 변환할 수 있다.

```python
# Example 8
korea = pytz.timezone('Asia/Seoul')
korea_dt = pacific.normalize(utc_dt.astimezone(korea))
print(korea_dt)
```

## [68. copyreg를 사용해 pickle을 더 신뢰성 있게 만들라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_68.py)

pickle 내장 모듈을 사용하면 파이썬 객체를 바이트 스트림으로 직렬화하거나 바이트 스트림을 파이썬으로 역직렬화할 수 있다. 피클(pickle)된 바이트 스트림은 서로 신뢰할 수 없는 당사자 사이의 통신에 사용하면 안 된다.

pickle의 목적은 여러분이 제어하는 프로그램들이 이진 채널을 통해 서로 파이썬 객체를 넘기는 데 있다. 

설계상 pickle 모듈의 직렬화 형식은 안전하지 않다. 직렬화한 데이터에는 원본 파이썬 객체를 복원하는 방법을 표현하는 데이터가 들어 있는데, 이 데이터는 근본적으로 프로그램이라 할 수 있다. 이는 악의적인 pickle 데이터가 자신을 역직렬화하는 파이썬 프로그램의 일부를 취약하게 만들 수도 있다는 뜻이다.

반대로 json 모듈은 설계상 안전하다. 직렬화한 JSON 데이터에는 객체 계층 구조를 간단하게 묘사한 값이 들어 있다. JSON 데이터를 역직렬화해도 파이썬 프로그램이 추가적인 위험에 노출되는 일은 없다. 서로를 신뢰할 수 없는 프로그램이 통신해야 할 경우에는 JSON 같은 형식을 사용해야 한다.

다음 코드는 dump 함수를 사용해 GameState 객체를 파일에 기록한다.

```python
# Example 1
class GameState:
    def __init__(self):
        self.level = 0
        self.lives = 4


# Example 2
state = GameState()
state.level += 1  # Player beat a level
state.lives -= 1  # Player had to try again

print(state.__dict__)


# Example 3
import pickle

state_path = 'game_state.bin'
with open(state_path, 'wb') as f:
    pickle.dump(state, f)
```

나중에 이 파일에 대해 load 함수를 호출하면 직렬화한 적이 전혀 없었던 것처럼 다시 GameState 객체를 돌려 받을 수 있다.

그러나 pickle에 객체를 2개 이상 저장할 수 없다..?..

pickle의 주 용도는 객체 직렬화를 쉽게 만드는 것이다. 간단한 수준을 벗어나면 pickle의 동작을 예상할 수 없다. copyreg 내장 모듈을 사용하면 쉽게 해결 가능하다.

파이썬 객체를 직렬화하고 역직렬화할 때 사용할 함수를 등록할 수 있으므로 pickle의 동작을 제어할 수 있고, 그에 따라 pickle 동작의 신뢰성을 높일 수 있다.

### copyreg 

#### 디폴트 애트리뷰트 값

디폴트 인자가 있는 생성자를 사용하면 GameState 객체를 언피클(unpickle)했을 때도 항상 필요한 모든 애트리뷰트를 포함시킬 수 있다.

```python
# Example 9
class GameState:
    def __init__(self, level=0, lives=4, points=0):
        self.level = level
        self.lives = lives
        self.points = points
```

이 생성자를 피클링에 사용하려면 GameState 객체를 받아서 copyreg 모듈이 사용할 수 있는 튜플 파라미터로 변환해주는 도우미 함수가 필요하다.

이 함수가 반환한 튜플 객체에는 언피클 시 사용할 함수와 언피클 시 함수에 전달해야 하는 파라미터 정보가 들어간다.

```python
# Example 10
def pickle_game_state(game_state):
    kwargs = game_state.__dict__
    return unpickle_game_state, (kwargs,)


# Example 11
def unpickle_game_state(kwargs):
    return GameState(**kwargs)


# Example 12
import copyreg

copyreg.pickle(GameState, pickle_game_state)


# Example 13
state = GameState()
state.points += 1000
serialized = pickle.dumps(state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)
```

변경 함수를 등록해도 게임 데이터가 제대로 만들어진다. pickle 모듈의 디폴트 동작은 object에 속한 애트리뷰트만 저장한 후 역직렬화할 때 복구하지만, unpickle_game_state는 GameState의 생성자를 직접 호출하므로 이런 경우에도 객체가 제대로 생성된다. 

GameState의 생성자 키워드 인자에는 파라미터가 들어오지 않은 경우 설정할 디폴트 값이 지정돼 있다. 따라서 예전 게임 상태 파일을 역직렬화하면 새로 추가한 magic 필드의 값은 디폴트 값으로 설정된다.

```python
# Example 14
class GameState:
    def __init__(self, level=0, lives=4, points=0, magic=5):
        self.level = level
        self.lives = lives
        self.points = points
        self.magic = magic  # New field


# Example 15
print('Before:', state.__dict__)
state_after = pickle.loads(serialized)
print('After: ', state_after.__dict__)
```

#### 클래스 버전 지정

가끔은 파이썬 객체의 필드를 제거해 예전 버전 객체와의 하위 호환성(backward compatibility)이 없어지는 경우도 발생한다. 이런 식의 변경이 일어나면 디폴트 인자를 사용하는 접근 방법을 사용할 수 없다.

copyreg 함수에게 전달하는 함수에 버전 파라미터를 추가하면 이 문제를 해결할 수 있다. 새로운 GameState 객체를 피클링할 때는 직렬화한 데이터의 버전이 2로 설정된다.

```python
# Example 18
def pickle_game_state(game_state):
    kwargs = game_state.__dict__
    kwargs['version'] = 2
    return unpickle_game_state, (kwargs,)
```

이전 버전 데이터에는 version 인자가 들어 있지 않다. 따라서 이에 맞춰 GameState 생성자에 전달할 인자를 적절히 변경할 수 있다.

```python
# Example 19
def unpickle_game_state(kwargs):
    version = kwargs.pop('version', 1)
    if version == 1:
        del kwargs['lives']
    return GameState(**kwargs)
```

이제는 이전에 직렬화했던 객체도 제대로 역직렬화된다.

미래에 같은 클래스의 버전이 변경되는 경우에도 이와 같은 접근 방법을 계속 사용할 수 있다. 이전 버전이 객체를 새 버전 객체에 맞추기 위해 필요한 모든 로직을 unpickle_game_state 함수에 넣을 수 있다.

#### 안정적인 임포트 경로

pickle을 할 때 마주칠 수 있는 다른 문제점으로, 클래스 이름이 바뀌어 코드가 깨지는 경우를 들 수 있다. 프로그램이 존재하는 생명 주기에서 클래스 이름을 변경하거나 클래스를 다른 모듈로 옮기는 방식으로 코드를 리팩터링하는 경우가 있다. 

이 예외가 발생하는 것은 피클된 데이터 안에 직렬화한 클래스의 임포트 경로가 들어 있기 때문이다.

이 경우에도 copyreg를 쓰는 것이 해결 방법이 될 수 있다. copyreg를 쓰면 객체를 언피클할 때 사용할 함수에 대해 안정적인 식별자를 지정할 수 있다.

이로 인해 여러 다른 클래스에서 다른 이름으로 피클된 데이터를 역직렬화할 때 서로 전환할 수 있다. 이 기능은 한 번 더 간접 계층을 추가해준다.

```python
# Example 20
copyreg.pickle(GameState, pickle_game_state)
print('Before:', state.__dict__)
state_after = pickle.loads(serialized)
print('After: ', state_after.__dict__)


# Example 21
copyreg.dispatch_table.clear()
state = GameState()
serialized = pickle.dumps(state)
del GameState
class BetterGameState:
    def __init__(self, level=0, points=0, magic=5):
        self.level = level
        self.points = points
        self.magic = magic


# Example 22
try:
    pickle.loads(serialized)
except:
    logging.exception('Expected')
else:
    assert False


# Example 23
print(serialized)
```

copyreg를 쓰면 BetterGameState 대신 unpickle_game_state에 대한 임포트 경로가 인코딩된다는 사실을 알 수 있다.

```python
# Example 24
copyreg.pickle(BetterGameState, pickle_game_state)

# Example 25
state = BetterGameState()
serialized = pickle.dumps(state)
print(serialized)
```

- 신뢰할 수 있는 프로그램 사이에 객체를 직렬화하고 역직렬화할 때는 pickle 내장 모듈이 유용하다.
- 시간이 지남에 따라 클래스가 바뀔(애트리뷰트의 추가나 삭제 등) 수 있으므로 이전에 피클한 객체를 역직렬화하면 문제가 생길 수 있다.
- 직렬화한 객체의 하위 호환성을 보장하고자 copyreg 내장 모듈과 pickle을 함께 사용하라.

## [69. 정확도가 매우 중요한 경우에는 decimal을 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_69.py)

파이썬 정수 타입은 실용적으로 어떤 크기의 정수든 표현할 수 있다. 2배 정밀도(double precision) 부동수소점 타입은 IEEE 754 표준을 따른다.

2배 정밀도('배정도'라고 쓰는 경우도 있음)에서 정밀도가 두 배라는 말은 float에 비해 정밀도가 두 배 이상 높다는 뜻이다.

IEEE 754 표준에서 32비트 타입(표준에서는 binary32라고 부름)은 24개의 이진 유효 숫자 비트와 8비트 지수를 사용하고, 64비트 타입(표준에서는 binary64라고 부름)은 53개의 이진 유효 숫자 비트와 11비트 지수를 사용하므로 십진수로 보면 유효 숫자 개수는 대략 두 배이고 지수의 범위는 열 배정도 차이가 난다.

한편 부동소수점이라는 말은 소수점 위치가 고정적이지 않아서 붙은 이름이다. 이는 부동소수점 수가 과학적 표기법에 사용하는 1.23e10과 같은 방식을 사용해 값을 표현하기 때문엗 붙은 이름이다.

부동소수점 수에서는 11.23을 1.123e1, 0.1123e2 등과 같이 여러 가지 형태로 표현할 수 있으므로 소수점 위치가 고정돼 있지 않다.

```python
# Example 1
rate = 1.45
seconds = 3*60 + 42
cost = rate * seconds / 60
print(cost)


# Example 2
print(round(cost, 2))
```

IEEE 754 부동소수점 수의 내부(이진) 표현법으로 인해 결과는 올바른 값(5.365)보다 0.000000000001만큼 더 작다. 근본적인 이유를 살펴보면, 십진 유한 소수는 분모가 10의 거듭제곱 꼴이어서 분모를 소인수 분해하면 2와 5의 거듭제곱으로 나타나지만 이진 소수는 2의 거듭제곱 꼴로 표현 가능한 수만 유한하게 표현할 수 있기 때문이다.

예를 들어 0.2(이 값은 1/5와 같다. 2와 5는 서로소이다)를 이진 소수로 표현하면 0.001100110011...이라는 순환 소수가 된다. 따라서 이런 경우에는 정해진 소수점 이하 자릿수의 십진수와 이진수를 변화하는 과정에서 약간의 값 차이가 발생할 수밖에 없다.

Decimal 클래스는 디폴트로 소수점 이하 28번째 자리까지 고정소수점 수 연산을 제공한다. 심지어 자릿수를 더 늘릴 수도 있다. 이 기능을 활용하면 IEEE 74 부동소수점 수에 존재하는 문제를 우회할 수 있다.

Decimal을 사용하면 반올림 처리도 원하는 대로 더 정확히 할 수 있다.

```python
# Example 3
from decimal import Decimal

rate = Decimal('1.45')
seconds = Decimal(3*60 + 42)
cost = rate * seconds / Decimal(60)
print(cost)
```

Decimal에 int 넣지 말고 정확한 답이 필요하다면 str을 사용하라.

```python
# Example 4
print(Decimal('1.45'))
print(Decimal(1.45))


# Example 5
print('456')
print(456)


# Example 6
rate = Decimal('0.05')
seconds = Decimal('5')
small_cost = rate * seconds / Decimal(60)
print(small_cost)
```

```python
# Example 8
from decimal import ROUND_UP

rounded = cost.quantize(Decimal('0.01'), rounding=ROUND_UP)
print(f'Rounded {cost} to {rounded}')


# Example 9
rounded = small_cost.quantize(Decimal('0.01'), rounding=ROUND_UP)
print(f'Rounded {small_cost} to {rounded}')
```

Decimal은 고정소수점 수에 대해서는 잘 작동하지만 여전히 정밀도에 한계가 있다(예를 들어 1/3은 여전히 근사치를 사용해야 한다). 정밀도 제한 없이 유리수(rational number)를 사용하고 싶다면 fractions 내장 모듈에 있는 Fraction 클래스를 사용하라.

## [70. 최적화하기 전에 프로파일링을 하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_70.py)

파이썬은 프로그램의 각 부분이 실행 시간을 얼마나 차지하는지 결정할 수 있게 해주는 프로파일러(profiler)를 제공한다. 다음 코드에서는 삽입 정렬을 사용해 데이터 리스트를 정렬하는 함수를 정의한다.

```python
import random
random.seed(1234)

import logging
from pprint import pprint
from sys import stdout as STDOUT

# Example 1
def insertion_sort(data):
    result = []
    for value in data:
        insert_value(result, value)
    return result


# Example 2
def insert_value(array, value):
    for i, existing in enumerate(array):
        if existing > value:
            array.insert(i, value)
            return
    array.append(value)
```

위에는 입력 배열을 선형 검색하는 비효율적인 insert_value 함수.

```python
# Example 3
from random import randint

max_size = 10**4
data = [randint(0, max_size) for _ in range(max_size)]
test = lambda: insertion_sort(data)
```

다음 코드는 cProfile 모듈에 있는 Profile 객체를 인스턴스화하고, 이 인스턴스의 runcall 메서드를 사용해 테스트 함수를 실행한다. (test에 람다를 사용해 함수를 할당?하는구만)

```python
# Example 4
from cProfile import Profile

profiler = Profile()
profiler.runcall(test)
```

테스트 함수가 실행되고 나면 pstats 내장 모듈에 들어 있는 Stats 클래스를 사용해 성능 통계를 추출할 수 있다. Stats에 들어 있는 여러 메서드를 사용해 관심 대상 프로파일 정보를 선택하고 정렬하는 방법을 조절해 관ㅅ미 있는 항목만 표시할 수 있다.

```python
from pstats import Stats

stats = Stats(profiler)
stats = Stats(profiler, stream=STDOUT)
stats.strip_dirs()
stats.sort_stats('cumulative')  # 누적 통계

stats.print_stats()
```

- ncalls: 프로파일링 기간 동안 함수가 몇 번 호출됐는지 보여준다.
- tottime: 프로파일링 기간 동안 대상 함수를 실행하는 데 걸린 시간의 합계를 보여준다. 대상 함수가 다른 함수를 호출한 경우, 이 다른 함수를 실행하는 데 걸린 시간은 제외한다.
- tottime percall: 프로파일링 기간 동안 함수가 호출될 때마다 걸린 시간의 평균을 보여준다. 대상 함수가 다른 함수를 호출한 경우, 이 다른 함수를 실행하기 위해 걸린 시간은 제외된다. 이 값은 tottime을 ncalls로 나눈 값과 같다.
- cumtime: 함수를 실행할 때 걸린 누적 시간을 보여준다. 이 시간에는 대상함수가 호출한 다른 함수를 실행하는 데 걸린 시간이 모두 포함된다.
- cumtime percall: 프로파일링 기간 동안 함수가 호출될 떄마다 걸린 누적시간의 평균을 보여준다. 이 시간에는 대상 함수가 호출한 다른 함수를 실행하는 데 걸린 시간이 모두 포함된다. 이 값은 cumtime을 ncalls로 나눈 값과 같다.

앞의 프로파일러 통계 표를 보면 CPU를 가장 많이 사용한 함수는 insert_value 함수라는 점을 알 수 있다. 이 함수를 bisect 내장 모듈을 사용해 다시 구현했다.

```python
# Example 6
from bisect import bisect_left

def insert_value(array, value):
    i = bisect_left(array, value)
    array.insert(i, value)


# Example 7
profiler = Profile()
profiler.runcall(test)
stats = Stats(profiler, stream=STDOUT)
stats.strip_dirs()
stats.sort_stats('cumulative')
stats.print_stats()
```

이전에 비해 실행 시간이 100배 가까이 줄어들었음을 알 수 있다.

전체 프로그램을 프로파일링했는데 공통 유틸리티 함수가 대부분의 실행 시간을 차지한다는 사실을 발견할 때도 있다. 프로그램의 여러 부분에서 이런 유틸리티 함수를 호출하기 떄문에 프로파일러의 디폴트 출력을 사용하면 이런 상황을 제대로 이해하기 어려울 수 있다.

이런 상황을 처리하고자 파이썬 프로파일러는 각 함수를 프로파일링한 정보에 대해 그 함수를 호출한 함수들이 얼마나 기여했는지를 보여주는 `print_callers` 메서드를 제공한다.

```python
# Example 8
def my_utility(a, b):
    c = 1
    for i in range(100):
        c += a * b

def first_func():
    for _ in range(1000):
        my_utility(4, 5)

def second_func():
    for _ in range(10):
        my_utility(1, 3)

def my_program():
    for _ in range(20):
        first_func()
        second_func()


# Example 9
profiler = Profile()
profiler.runcall(my_program)
stats = Stats(profiler, stream=STDOUT)
stats.strip_dirs()
stats.sort_stats('cumulative')
stats.print_stats()


# Example 10
stats = Stats(profiler, stream=STDOUT)
stats.strip_dirs()
stats.sort_stats('cumulative')
stats.print_callers()
```

이 프로파일러 정보 표는 왼쪾에 호출된 함수를, 오른쪽에 그 함수를 호출한 함수를 보여준다. first_func가 my_utility 함수를 가장 많이 썼다는 점을 분명히 알 수 있다.

## [71.생산자-소비자 큐로 deque를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_71.py)

```python
# Example 1
class Email:
    def __init__(self, sender, receiver, message):
        self.sender = sender
        self.receiver = receiver
        self.message = message


# Example 2
def get_emails():
    yield Email('foo@example.com', 'bar@example.com', 'hello1')
    yield Email('baz@example.com', 'banana@example.com', 'hello2')
    yield None
    yield Email('meep@example.com', 'butter@example.com', 'hello3')
    yield Email('stuff@example.com', 'avocado@example.com', 'hello4')
    yield None
    yield Email('thingy@example.com', 'orange@example.com', 'hello5')
    yield Email('roger@example.com', 'bob@example.com', 'hello6')
    yield None
    yield Email('peanut@example.com', 'alice@example.com', 'hello7')
    yield None

EMAIL_IT = get_emails()

class NoEmailError(Exception):
    pass

def try_receive_email():
    # Returns an Email instance or raises NoEmailError
    try:
        email = next(EMAIL_IT)
    except StopIteration:
        email = None

    if not email:
        raise NoEmailError

    print(f'Produced email: {email.message}')
    return email
```

```python
# Example 3
def produce_emails(queue):
    while True:
        try:
            email = try_receive_email()
        except NoEmailError:
            return
        else:
            queue.append(email)  # Producer


# Example 4
def consume_one_email(queue):
    if not queue:
        return
    email = queue.pop(0)  # Consumer
    # Index the message for long-term archival
    print(f'Consumed email: {email.message}')


# Example 5
def loop(queue, keep_running):
    while keep_running():
        produce_emails(queue)
        consume_one_email(queue)

def make_test_end():
    count=list(range(10))

    def func():
        if count:
            count.pop()
            return True
        return False

    return func


def my_end_func():
    pass

my_end_func = make_test_end()
loop([], my_end_func)
```

```python
# Example 6
import timeit

def print_results(count, tests):
    avg_iteration = sum(tests) / len(tests)
    print(f'Count {count:>5,} takes {avg_iteration:.6f}s')
    return count, avg_iteration

def list_append_benchmark(count):
    def run(queue):
        for i in range(count):
            queue.append(i)

    tests = timeit.repeat(
        setup='queue = []',
        stmt='run(queue)',
        globals=locals(),
        repeat=1000,
        number=1)

    return print_results(count, tests)


# Example 7
def print_delta(before, after):
    before_count, before_time = before
    after_count, after_time = after
    growth = 1 + (after_count - before_count) / before_count
    slowdown = 1 + (after_time - before_time) / before_time
    print(f'{growth:>4.1f}x data size, {slowdown:>4.1f}x time')

baseline = list_append_benchmark(500)
for count in (1_000, 2_000, 3_000, 4_000, 5_000):
    print)원소를 제 위치로 옮겨야 해서(리스트의 모든 원소를 복사하지만, 실제로는 우너소를 가리키는 포인터를 복사하기만 하면 된다는 사실을 염두에 두라. 이로 인해 pop(0)을 하면 n-1번 복사해야 하므로 pop(0)에 걸리는 시간은 len(queue)에 비례한다.) 결과적으로 전체 리스트 내용을 다시 재대입하기 때문이다.

파이썬 collections 내장 모듈에는 deque 클래스가 들어 있다. deque는 양방향 큐(double-ended queue) 구현이며 '데크'라고 읽는다. 데크의 시작과 끝 지점에 원소를 넣거나 빼는 데는 상수 시간이 걸린다.

따라서 **데크는 FIFO 큐를 구현할 때 이상적**이다.

deque 클래스를 사용하더라도 produce_emails 함수에 있는 append는 리스트를 사용할 때와 같은 형태로 그대로 둬도 된다. 하지만 consume_one_email에 있는 list.pop 메서드는 deque.popleft 메서드를 아무 인자도 없이 호출하게 바꿔야 한다.

그리고 loop 메서드를 호출할 때 리스트 인스턴스 대신 deque 인스턴스를 전달해야 한다.

```python
# Example 10
import collections

def consume_one_email(queue):
    if not queue:
        return
    email = queue.popleft()  # Consumer
    # Process the email message
    print(f'Consumed email: {email.message}')

def my_end_func():
    pass

my_end_func = make_test_end()
EMAIL_IT = get_emails()
loop(collections.deque(), my_end_func)


# Example 11
def deque_append_benchmark(count):
    def prepare():
        return collections.deque()

    def run(queue):
        for i in range(count):
            queue.append(i)

    tests = timeit.repeat(
        setup='queue = prepare()',
        stmt='run(queue)',
        globals=locals(),
        repeat=1000,
        number=1)
    return print_results(count, tests)

baseline = deque_append_benchmark(500)
for count in (1_000, 2_000, 3_000, 4_000, 5_000):
    print()
    comparison = deque_append_benchmark(count)
    print_delta(baseline, comparison)
```

```python
# Example 12
def dequeue_popleft_benchmark(count):
    def prepare():
        return collections.deque(range(count))

    def run(queue):
        while queue:
            queue.popleft()

    tests = timeit.repeat(
        setup='queue = prepare()',
        stmt='run(queue)',
        globals=locals(),
        repeat=1000,
        number=1)

    return print_results(count, tests)

baseline = dequeue_popleft_benchmark(500)
for count in (1_000, 2_000, 3_000, 4_000, 5_000):
    print()
    comparison = dequeue_popleft_benchmark(count)
    print_delta(baseline, comparison)
```

popleft를 사용하면 앞에서 선형보다 더 나쁜 결과를 보이던 pop(0)과 달리 대기열 길이에 비례해 시간이 늘어난다. 생산자-소비자 대기열이 임계 단계라면 deque가 훌륭한 선택이다.

임계 단계 - 어떤 시스템의 전체 성증이 시스템을 이루는 특정 요소에 의해 달라지고 이 요소의 성능을 개선하면 시스템 전체 성능이 좋아질 수 있는 경우, 이런 요소르 임계적(critical) 요소라고 부른다.

단선적인 프로세스의 경우에는 해당 프로세스를 이루는 모든 하위 프로세스가 임계 프로세스다. 

- 생산자는 append를 호출해 원소를 추가하고 소비자는 pop(0)을 사용해 원소를 받게 만들면 리스트 타입을 FIFO 큐로 사용할 수 있다. 하지만 리스트를 FIFO 큐로 사용하면, 큐 길이가 늘어남에 따라 pop(0)의 성능이 선형보다 더 크게 나빠지기 때문에 문제가 될 수 있다.
- collections 내장 모듈에 있는 deque 클래스틑 큐 길이에 관계없이 상수 시간 만에 append와 popleft를 수행하기 때문에 FIFO 구현에 이상적이다.

## [72. 정렬된 시퀀스를 검색할 때는 bisect를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_72.py)

프로그램이 구체적으로 처리해야 하는 정보의 유형이 무엇이든, 리스트에서 index 함수를 사용해 특정 값을 찾아내려면 리스트 길이에 선형으로 비례하는 시간이 필요하다.

```python
# Example 1
data = list(range(10**5))
index = data.index(91234)
assert index == 91234


# Example 2
def find_closest(sequence, goal):
    for index, value in enumerate(sequence):
        if goal < value:
            return index
    raise ValueError(f'{goal} is out of bounds')

index = find_closest(data, 91234.56)
assert index == 91235

try:
    find_closest(data, 100000000)
except ValueError:
    pass  # Expected
else:
    assert False
```

파이썬 내장 bisect 모듈은 순서가 정해져 있는 리스트에 대해 이런 유형의 검사를 더 효과적으로 수행한다. bisect_left 함수를 사용하면 정렬된 원소로 이뤄진 시퀀스에 대해 이진 검색(binary search)을 효율적으로 수헹할 수 있다.

bisect_left가 반환하는 인덱스는 리스트에 찾는 값의 원소가 존재하는 경우 이 원소의 인덱스이며, 리스트에 찾는 원소가 존재하지 않는 경우 정렬 순서상 해당 값을 삽입해야 할 자리의 인덱스다.

```python
# Example 3
from bisect import bisect_left

index = bisect_left(data, 91234)     # Exact match
assert index == 91234

index = bisect_left(data, 91234.56)  # Closest match
assert index == 91235
```

bisect 모듈이 사용하는 이진 검색 알고리즘의 복잡도는 로그 복잡도다. 이는 길이가 100만인 리스트를 bisect로 검색하는 데 걸리는 시간과 길이가 20인 리스트를 list.index로 선형 검색하는 데 걸리는 시간이 거의 같다는 뜻이다(math.log2(10**6) == 19.93).

bisect에서 가장 좋은 점은 리스트 타입뿐 아니라 시퀀스처럼 작동하는 모든 파이썬 객체에 대해 bisect 모듈의 기능을 사용할 수 있다는 점이다. bisect 모듈은 더 고급스런 기능도 제공한다. help(bisect)를 보라.

- 리스트에 들어 있는 정렬된 데이터를 검색할 때 index 메서드를 사용하거나 for 루프와 맹목적인 비교를 사용하면 선형 시간이 걸린다.
- bisect 내장 모듈의 bisect_left 함수는 정렬된 리스트에서 원하는 값을 찾는 데 로그 시간이 걸린다. 따라서 다른 접근 방법보다 훨씬 빠르다.

## [73. 우선순위 큐로 heapq를 사용하는 방법을 알아두라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_73.py)

프로그램에서 원소를 받은 순서가 아니라 원소 간의 상대적인 중요도에 따라 우너소를 정렬해야 하는 경우가 있다. 이런 목적에는 우선순위 큐가 적합하다.

```python
# Example 1
class Book:
    def __init__(self, title, due_date):
        self.title = title
        self.due_date = due_date


# Example 2
def add_book(queue, book):
    queue.append(book)
    queue.sort(key=lambda x: x.due_date, reverse=True)

queue = []
add_book(queue, Book('Don Quixote', '2019-06-07'))
add_book(queue, Book('Frankenstein', '2019-06-05'))
add_book(queue, Book('Les Misérables', '2019-06-08'))
add_book(queue, Book('War and Peace', '2019-06-03'))


# Example 3
class NoOverdueBooks(Exception):
    pass

def next_overdue_book(queue, now):
    if queue:
        book = queue[-1]
        if book.due_date < now:
            queue.pop()
            return book

    raise NoOverdueBooks


# Example 4
now = '2019-06-10'

found = next_overdue_book(queue, now)
print(found.title)

found = next_overdue_book(queue, now)
print(found.title)


# Example 5
def return_book(queue, book):
    queue.remove(book)

queue = []
book = Book('Treasure Island', '2019-06-04')

add_book(queue, book)
print('Before return:', [x.title for x in queue])

return_book(queue, book)
print('After return: ', [x.title for x in queue])


# Example 6
try:
    next_overdue_book(queue, now)
except NoOverdueBooks:
    pass          # Expected
else:
    assert False  # Doesn't happen

```

위 해결 방법의 계산 복잡도는 이상적이지 않다. 연체된 책을 검사하거나 제거하는 비용은 상수이지만, 책을 추가할 때마다 전체 리스트를 다시 정렬해야 하는 추가 비용이 들어간다.

다행히 파이썬은 원소가 들어 있는 리스트에 대해 우선순위 큐를 효율적으로 구현하는 내장 heapq 모듈을 제공한다. heapq에서 힙(heap)은 여러 아이템을 유지하되 새로운 원소를 추가하거나 가장 작은 원소를 제거할 때 로그 복잡도가 드는 데이터 구조다.

따라서 선형 복잡도보다 더 규모 확장성이 좋다.

다음 코드는 heapq 모듈을 사용해 add_book 함수를 다시 구현한 것이다. 큐는 여전히 일반 리스트다. heappush 함수는 앞에서 사용한 list.append 호출을 대신한다. 그리고 이 큐에 대해 더 이상 List.sort를 호출할 필요가 없다.

```python
# Example 11
from heapq import heappush

def add_book(queue, book):
    heappush(queue, book)
```

우선순위 큐에 들어갈 원소들이 서로 비교 가능하고 원소 사이에 자연스러운 정렬 순서가 존재해야 heapq 모듈이 제대로 작동할 수 있다. functools 내장 모듈이 제공하는 total_ordering 클래스 데코레이터를 사용하고 `__lt__` 특별 메서드를 구현하면 빠르게 Book 클래스에 비교 기능과 자연스러운 정렬 순서를 제공할 수 있다.

```python
# Example 13
import functools

@functools.total_ordering
class Book:
    def __init__(self, title, due_date):
        self.title = title
        self.due_date = due_date

    def __lt__(self, other):
        return self.due_date < other.due_date


# Example 14
queue = []
add_book(queue, Book('Pride and Prejudice', '2019-06-01'))
add_book(queue, Book('The Time Machine', '2019-05-30'))
add_book(queue, Book('Crime and Punishment', '2019-06-06'))
add_book(queue, Book('Wuthering Heights', '2019-06-12'))
print([b.title for b in queue])
```

또는 heapq.heapify 함수를 사용하면 선형 시간에 힙을 만들 수 있다.

```python
# Example 16
from heapq import heapify

queue = [
    Book('Pride and Prejudice', '2019-06-01'),
    Book('The Time Machine', '2019-05-30'),
    Book('Crime and Punishment', '2019-06-06'),
    Book('Wuthering Heights', '2019-06-12'),
]
heapify(queue)
print([b.title for b in queue])
```

대출 만기를 넘긴 책을 검사하려면 리스트의 마지막 원소가 아니라 첫 번째 원소를 살펴본 다음, list.pop 함수 대신에 heapq.heapop 함수를 사용한다.

```python
# Example 17
from heapq import heappop

def next_overdue_book(queue, now):
    if queue:
        book = queue[0]           # Most overdue first
        if book.due_date < now:
            heappop(queue)        # Remove the overdue book
            return book

    raise NoOverdueBooks


# Example 18
now = '2019-06-02'

book = next_overdue_book(queue, now)
print(book.title)

book = next_overdue_book(queue, now)
print(book.title)

try:
    next_overdue_book(queue, now)
except NoOverdueBooks:
    pass          # Expected
else:
    assert False  # Doesn't happen


# Example 19
def heap_overdue_benchmark(count):
    def prepare():
        to_add = list(range(count))
        random.shuffle(to_add)
        return [], to_add

    def run(queue, to_add):
        for i in to_add:
            heappush(queue, i)
        while queue:
            heappop(queue)

    tests = timeit.repeat(
        setup='queue, to_add = prepare()',
        stmt=f'run(queue, to_add)',
        globals=locals(),
        repeat=100,
        number=1)

    return print_results(count, tests)


# Example 20
baseline = heap_overdue_benchmark(500)
for count in (1_000, 1_500, 2_000):
    print()
    comparison = heap_overdue_benchmark(count)
    print_delta(baseline, comparison)


# Example 21
@functools.total_ordering
class Book:
    def __init__(self, title, due_date):
        self.title = title
        self.due_date = due_date
        self.returned = False  # New field

    def __lt__(self, other):
        return self.due_date < other.due_date


# Example 22
def next_overdue_book(queue, now):
    while queue:
        book = queue[0]
        if book.returned:
            heappop(queue)
            continue

        if book.due_date < now:
            heappop(queue)
            return book

        break

    raise NoOverdueBooks

queue = []

book = Book('Pride and Prejudice', '2019-06-01')
add_book(queue, book)

book = Book('The Time Machine', '2019-05-30')
add_book(queue, book)
book.returned = True

book = Book('Crime and Punishment', '2019-06-06')
add_book(queue, book)
book.returned = True

book = Book('Wuthering Heights', '2019-06-12')
add_book(queue, book)

now = '2019-06-11'

book = next_overdue_book(queue, now)
assert book.title == 'Pride and Prejudice'

try:
    next_overdue_book(queue, now)
except NoOverdueBooks:
    pass          # Expected
else:
    assert False  # Doesn't happen


# Example 23
def return_book(queue, book):
    book.returned = True

assert not book.returned
return_book(queue, book)
assert book.returned
```

... (생략)

- 리스트 연산을 사용해 우선순위 를 구현하면 큐 크기가 커짐에 따라 프로그램의 성능이 선형보다 더 빠르게 나빠진다.
- heapq 내장 모듈은 효율적으로 규모 확장이 가능한 우선순위 큐를 구현하는 데 필요한 모든 기능을 제공한다.
- heapq를 사용하려면 우선순위를 부여하려는 원소들이 자연스러운 순서를 가져야 한다. 이는 원소를 표현하는 클래스에 대해 `__lt__`와 같은 특별 메서드가 있어야 한다는 뜻이다.

## [74. bytes를 복사하지 않고 다루려면 memoryview와 bytearray를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_74.py)

```python
# Example 1
def timecode_to_index(video_id, timecode):
    return 1234
    # Returns the byte offset in the video data

def request_chunk(video_id, byte_offset, size):
    pass
    # Returns size bytes of video_id's data from the offset

video_id = ...
timecode = '01:09:14:28'
byte_offset = timecode_to_index(video_id, timecode)
size = 20 * 1024 * 1024
video_data = request_chunkv그램이 커다란 메모리를 빠르게 처리해야 하는 경우에 성능을 엄청나게 향상시킬 수 있다.

다음 코드는 앞의 예제에서 사용한 간단한 bytes 슬라이스를 memoryview로 바꿔서 마이크로 벤치마크를 수행한 결과다.

```python
# Example 5
video_view = memoryview(video_data)

def run_test():
    chunk = video_view[byte_offset:byte_offset + size]
    # Call socket.send(chunk), but ignoring for benchmark

result = timeit.timeit(
    stmt='run_test()',
    globals=globals(),
    number=100) / 100

print(f'{result:0.9f} seconds')
```

결과는 250나노초다. 이제 서버의 이론적인 최대 스루풋은 20MB / 250나노초 = 164TB초다. 병렬 클라이언트의 경우 이론적으로 최대 1 CPU-초 / 250 나노초 = 400만개 까지 지원할 수 있다. 엄청난 개선이다.

성능 개선으로 인해 이제 프로그램의 성능은 CPU 성능이 아니라 클라이언트 소켓 연결의 성능에 따라 전적으로 제한된다.

```python
socket = ...        # socket connection to the client
video_cache = ...   # Cache of incoming video stream
byte_offset = ...   # Incoming buffer position
size = 1024 * 1024  # Incoming chunk size
socket = FakeSocket()
video_cache = video_data[:]
byte_offset = 1234

chunk = socket.recv(size)
video_view = memoryview(video_cache)
before = video_view[:byte_offset]
after = video_view[byte_offset + size:]
new_cache = b''.join([before, chunk, after])
```

socket.recv 메서드는 bytes 인스턴스를 반환한다. 간단한 슬라이스 연산과 bytes.join 메서드를 사용하면 현재의 byte_offset에 있는 기존 캐시 데이터를 새로운 데이터로 스플라이스(splice)할 수 있다.

이런 연산의 성능을 확인하기 위해 또 다른 마이크로 벤치마크를 실행할 수 있다. 여기서는 가짜 소켓을 사용하기 때문에 이 성능 테스트는 I/O 상호작용을 테스트하지 않고 메모리 연산의 성능만 테스트한다.

```python
# Example 7
def run_test():
    chunk = socket.recv(size)
    before = video_view[:byte_offset]
    after = video_view[byte_offset + size:]
    new_cache = b''.join([before, chunk, after])

result = timeit.timeit(
    stmt='run_test()',
    globals=globals(),
    number=100) / 100

print(f'{result:0.9f} seconds')

```

이런 규모는 확장성이 없다.

더 나은 방법은 파이썬 내장 bytearray 타입과 memoryview를 같이 사용하는 것이다. bytes 인스턴스의 한 가지 단점은 읽기 전용이라 인덱스를 사용해 값을 변경할 수 없다는 것이다.

```python
# Example 8
try:
    my_bytes = b'hello'
    my_bytes[0] = b'\x79'
except:
    logging.exception('Expected')
else:
    assert False
```

bytearray 타입은 bytes에서 원사는 위치에 있는 값을 바꿀 수 있는 가변 버전과 같다. 인덱스를 사용해 bytearray의 내용을 바꿀 때는 바이트 문자열(한 글자짜리)이 아니라 정수를 대입한다.

```python
# Example 9
my_array = bytearray(b'hello')
my_array[0] = 0x79
print(my_array)

```

bytearray도 memoryview를 사용해 감쌀 수 있다. memoryview를 슬라이싱해서 객체를 만들고, 이 객체에 데이터를 대입하면 하부의 bytesarray 버퍼에 데이터가 대입된다.

이런 방법을 사용하면면 앞에서 bytes 인스턴스를 스플라이스해 클라이언트로부터 받은 데이터를 덧붙였던 것과 달리 데이터 복사에 드는 비용이 사라진다.

```python
# Example 10
my_array = bytearray(b'row, row, row your boat')
my_view = memoryview(my_array)
write_view = my_view[3:13]
write_view[:] = b'-10 bytes-'
print(my_array)


# Example 11
video_array = bytearray(video_cache)
write_view = memoryview(video_array)
chunk = write_view[byte_offset:byte_offset + size]
socket.recv_into(chunk)


# Example 12
def run_test():
    chunk = write_view[byte_offset:byte_offset + size]
    socket.recv_into(chunk)

result = timeit.timeit(
    stmt='run_test()',
    globals=globals(),
    number=100) / 100

print(f'{result:0.9f} seconds')
```
ideo_id, byte_offset, size)


# Example 2
class NullSocket:
    def __init__(self):
        self.handle = open(os.devnull, 'wb')

    def send(self, data):
        self.handle.write(data)

socket = ...             # socket connection to client
video_data = ...         # bytes containing data for video_id
byte_offset = ...        # Requested starting position
size = 20 * 1024 * 1024  # Requested chunk size
import os

socket = NullSocket()
video_data = 100 * os.urandom(1024 * 1024)
byte_offset = 1234

chunk = video_data[byte_offset:byte_offset + size]
socket.send(chunk)


# Example 3
import timeit

def run_test():
    chunk = video_data[byte_offset:byte_offset + size]
    # Call socket.send(chunk), but ignoring for benchmark

result = timeit.timeit(
    stmt='run_test()',
    globals=globals(),
    number=100) / 100

print(f'{result:0.9f} seconds')
```

문제는 기반 데이터를 bytes 인스턴스로 슬라이싱하려면 메모리를 복사해야 하는데, 이 과정이 CPU 시간을 점유한다는 점이다. 이 코드를 더 잘 작성하는 방법은 파이썬이 제공하는 memoryview 내장 타입을 사용하는 것이다.

memoryview는 CPython의 고성능 버퍼 프로토콜을 프로그램에 노출시켜준다. 버퍼 프로토콜은 파이썬 런타입과 C 확장이 bytes와 같은 객체를 통하지 않고 하부 데이터 버퍼에 접근할 수 있게 해주는 저수준 C API다.

memoryview 인스턴스의 가장 좋은 점은 슬라이싱을 하면 데이터를 복사하지 않고 새로운 memoryview 인스턴스를 만들어준다는 것이다.

```python
# Example 4
data = b'shave and a haircut, two bits'
view = memoryview(data)
chunk = view[12:19]
print(chunk)
print('Size:           ', chunk.nbytes)
print('Data in view:   ', chunk.tobytes())
print('Underlying data:', chunk.obj)
```

복사가 없는(zero-copy) 연산을 활성화함으로써 memoryview는 NumPy 같은 수치계산 C 확장이나 이 예제 프로그램 같은 I/O 위주 프로(
    comparison = list_append_benchmark(count)
    print_delta(baseline, comparison)

```

```python
# Example 8
def list_pop_benchmark(count):
    def prepare():
        return list(range(count))

    def run(queue):
        while queue:
            queue.pop(0)

    tests = timeit.repeat(
        setup='queue = prepare()',
        stmt='run(queue)',
        globals=locals(),
        repeat=1000,
        number=1)

    return print_results(count, tests)


# Example 9
baseline = list_pop_benchmark(500)
for count in (1_000, 2_000, 3_000, 4_000, 5_000):
    print()
    comparison = list_pop_benchmark(count)
    print_delta(baseline, comparison)
```

## Link

- [effective python](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=254321728)

