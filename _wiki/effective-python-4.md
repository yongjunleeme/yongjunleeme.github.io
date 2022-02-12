---
layout  : wiki
title   : 
summary : 
date    : 2022-02-12 23:52:42 +0900
updated : 2022-02-12 23:52:42 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}
## [80.pdb를 사용해 대화형으로 디버깅하라](https://github.com/yongjunleeme/effectivepython/tree/master/example_code/item_80/debugging)

파이썬에서 디버거를 사용하는 가장 편한 방법은 문제를 조사하기에 가장 적합한 지점에서 프로그램이 디버거를 초기화할 수 있게 프로그램을 변경하는 것이다. 이는 파이썬 프로그램을 일반적인 상태로 시작하는 경우와 디버거로 시작하는 경우에 차이가 없다는 뜻이다.

디버거를 초기화하기 위해 해야 할 일은 breakpoint 내장 함수를 호출하는 것뿐이다. 이 함수는 pdb  내장 모듈을 임포트하고 set_trace 함수를 실행하는 것과 똑같은 일을 한다.

```python
import math

def compute_rmse(observed, ideal):
    total_err_2 = 0
    count = 0
    for got, wanted in zip(observed, ideal):
        err_2 = (got - wanted) ** 2
        breakpoint()  # Start the debugger here
        total_err_2 += err_2
        count += 1

    mean_err = total_err_2 / count
    rmse = math.sqrt(mean_err)
    return rmse

result = compute_rmse(
    [1.8, 1.7, 3.2, 6],
    [2, 1.5, 3, 5])
print(result)
```

- pdb 프롬프트에서는 모듈을 임포트하거나, 새로운 객체를 만들거나, help 내장 함수를 실행하거나, 심지어는 프로글매의 일부를 변경할 수도 있다.
- `p <이름>`으로 지역 변수 이름을 입력하면 변수에 저장된 값을 출력할 수 있다.
- locals 내장 함수를 호출하면 모든 지역 변수 목록을 볼 수 있다.
- where: 현재 실행 중인 프로그램의 호출 스택을 출력
- up: 실행 호출 스택에서 현재 관찰 중인 함수를 호출한 쪽(위)으로 호출 스택 영역을 한 단계 이동해서 해당 함수의 지역 변수를 관찰할 수 있다. up 대신 u만 써도 된다.
- down: up의 반대. d만 써도 된다.

현재 상태를 살펴본 후에는 다음 다섯 가지 디버거 명령을 사용해 프로그램 실행을 다양한 방식으로 제어할 수 있다.

- step: 프로그램을 다음 줄까지 실행한 다음 제어를 디버거로 돌려서 디버거 프롬프트를 표시. 소스 코드 다음 줄에 함수를 호출하면 함수 첫 줄에서 디버거 제어가 돌아온다.
- next: 프로그램을 다음 줄까지 실행한 다음 제어를 디버거로 돌려서 디버거 프롬프트를 표시. 소스 코드 다음 줄에 함수를 호출하면 함수가 반환된 다음 제어가 디버거로 돌아온다.
- return: 현재 함수에서 반환될 때까지 프로그램을 실행한 후 제어가 디버거로 돌아온다.
- continue: 다음 중단점에 도달할 때까지 프로그램을 계속 실행한다. 실행하는 중에 중단점을 만나지 못하면 프로그램 실행이 끝날 때까지 프로그램을 계속 실행한다.

디버깅하려는 문제가 어떤 구체적인 조건에서만 발생한다는 사실을 알고 있다면, 해당 조건을 만족시킨 후 breakpoint를 호출하는 평범한 파이썬 코드를 추가할 수 있다.

```python
import math
def compute_rmse(observed, ideal):
    total_err_2 = 0
    count = 0
    for got, wanted in zip(observed, ideal):
        err_2 = (got - wanted) ** 2
        if err_2 >= 1:  # Start the debugger if True
            breakpoint()
        total_err_2 += err_2
        count += 1
    mean_err = total_err_2 / count
    rmse = math.sqrt(mean_err)
    return rmse

result = compute_rmse(
    [1.8, 1.7, 3.2, 7],
    [2, 1.5, 3, 5])
```

사후 디버깅을 활용하면 예외가 발생하거나 프로그램에 문제가 생겨 중단된 뒤에 프로그램을 디버깅할 수 있다.

```python
import math

def compute_rmse(observed, ideal):
    total_err_2 = 0
    count = 0
    for got, wanted in zip(observed, ideal):
        err_2 = (got - wanted) ** 2
        total_err_2 += err_2
        count += 1

    mean_err = total_err_2 / count
    rmse = math.sqrt(mean_err)
    return rmse

result = compute_rmse(
    [1.8, 1.7, 3.2, 7j],  # Bad input
    [2, 1.5, 3, 5])
print(result)
```

```python
$ python3 -m pdb -c continue postmortem_breakpoint.py
```

## [81. 프로그램이 메모리를 사용하는 방식과 메모리 누수를 이해하기 위해 tracemalloc을 사용하라](https://github.com/yongjunleeme/effectivepython/tree/master/example_code/item_81/tracemalloc)

파이썬 기본 구현인 CPython은 메모리 관리를 위해 참조 카운팅을 사용한다. 이로 인해 어떤 객체를 가리키는 참조가 모두 없어지면 참조된 객체도 메모리에서 삭제되고 메모리 공간을 다른 데이터에 내어줄 수 있다.

CPython에는 순환 탐지기(cycle detector)가 들어 있으므로 자기 자신을 참조하는(또는 A -> B -> C -> A처럼 고리 모양으로 서로를 참조하는) 객체의 메모리도 언젠가는 쓰레기 수집된다.

이는 이론적으로 대부분의 파이썬 개발자가 프로그램에서 메모리를 할당하거나 해제하는 것을 신경 쓰지 않아도 된다는 뜻이다. 파이썬 언어와 CPython 런타임이 알아서 메모리 관리를 해준다.

( 프로그램이 실행되는 시점인 실행 시점(Runtime)에 프로그램 실행과 관련한 모든 환경을 실행 환경이나 실행 시스템이라고 부른다. 그냥 영어 단어를 음차 표기해서 실행 시점을 런타임이라고 부르거나, 런타임 시스템을 줄여서 런타임이라고 부르는 경우도 자주 있다. 따라서 컨텍스트에 따라 런타임이 시간적인 실행 시점을 의미하는지 실행 환경을 의미하는지 구분해야 한다. )

하지만 실전에서는 더이상 사용하지 않는 쓸모없는 참조를 유지하기 때문에 언젠가 결국 메모리를 소진하게 되는 경우가 있다. 파이썬 프로그램이 누수시키는 메모리를 알아내기란 매우 어려운 일이다.

( 참조가 유지되는 것은 겨우 4바이트에서 8바이트의 포인터가 유지되는 것뿐이지만, 참조가 유지되면 이 참조가 가리키는 객체를 쓰레기 수집기가 수집하지 못한다는 사실을 기억해야 한다. 순환 참조를 감지하는 탐지기가 필요한 이유는 참조 카운팅 기법의 쓰레기 수집기를 쓰는 경우 순환 참조하는 객체들의 참조 개수가 절대로 0이 되지 못하기 때문에 이런 경우를 감지해 메모리를 재활용하기 위한 것이다. )

메모리 사용을 디버깅하는 첫 번째 방법은 gc 내장 모듈을 사용해 현재 쓰레기 수집기가 알고 있는 모든 객체를 나열시키는 것이다. 

tracemalloc은 객체를 자신이 할당된 장소와 연결시켜준다. 이 정보를 사용해 메모리 사용의 이전과 이후 스냅샷을 만들어 서로 비교하면 어떤 부분이 변경됐는지 알 수 있다.

```python
# waste_memory.py

import os

class MyObject:
    def __init__(self):
        self.data = os.urandom(100)

def get_data():
    values = []
    for _ in range(100):
        obj = MyObject()
        values.append(obj)
    return values

def run():
    deep_values = []
    for _ in range(100):
        deep_values.append(get_data())
    return deep_values
```

```python
import tracemalloc

tracemalloc.start(10)                      # Set stack depth
time1 = tracemalloc.take_snapshot()        # Before snapshot

import waste_memory

x = waste_memory.run()                     # Usage to debug
time2 = tracemalloc.take_snapshot()        # After snapshot

stats = time2.compare_to(time1, 'lineno')  # Compare snapshots
for stat in stats[:3]:
    print(stat)
```

출력에 있는 크기와 카운트 레이블을 보면 프로그램에서 메모리를 주로 사용하는 객체와 이런 객체를 할당한 소스 코드를 명확히 바로 알 수 있다.

tracemolloc 모듈은 각 할당의 전체 스택 트레이스(stack trace)를 출력한다(tracemalloc.strat 함수에 전달한 프레임 최대 개수만큼만 거슬러 올라가며 출력한다.). 다음 코드는 프로그램에서 메모리를 가장 많이 사용하는 곳의 스택 트레이스를 출력한다.

```python
import tracemalloc

tracemalloc.start(10)
time1 = tracemalloc.take_snapshot()

import waste_memory

x = waste_memory.run()
time2 = tracemalloc.take_snapshot()

stats = time2.compare_to(time1, 'traceback')
top = stats[0]
print('Biggest offender is:')
print('\n'.join(top.traceback.format()))
```

스택 트레이스는 프로그램에서 메모리를 소모하는 일반적인 함수나 클래스의 구체적인 용례를 파악할 때 중요한 도구다.

## 82. 커뮤니티에서 만든 모듈을 어디서 찾을 수 있는지 알아두라

`python3 -m pip`를 사용해 pip를 호출하면 패키지가 시스템에 설치된 파이썬 버전에 맞게 설치되도록 보장할 수 있다.

PyPI에 들어 있는 각 모듈은 서로 다른 라이선스로 제공된다. 대부분의 패키지, 특히 유명한 패키지들은 보통 자유로운 오픈소스 라이선스(https://opensource.org)로 제공된다.

## [84. 모든 함수, 클래스, 모둘에 독스트링을 작성하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_84.py)

파이썬 프로그램에서 독스트링을 가져오려면 `__doc__` 특별 애트리뷰트를 사요하면 된다.

```python
print(repr(palindrome.__doc__))
```

또 명령줄에서 내장 pydoc 모듈을 사용해 로컬 웹 서버를 실행할 수 있다. 이 서버는 여러분이 작성한 모듈을 비롯해 인터프리터에서 찾을 수 있는 모든 파이썬 문서를 제공한다.

```python
$ python3 -m pydoc -p 1234
```

- 문서 생성 도구 [스핑크스](https://www.sphinx-doc.org)
- 파이썬 오픈소스 문서 무료 호스팅 [리드더독스](https://readthedocs.org)
- 독스트링 문서 지침 [PEP 257](https://www.python.org/dev/pep-0257/)

### 모듈

독스트링의 첫 줄은 모듈의 목적을 설명하는 한 문장이어야 한다. 다음에 오는 단락에는 모듈 사용자들이 모듈의 동작에 대해 알아둬야 하는 세부 사항을 적어야 한다. 모듈 독스트링은 모듈에서 찾을 수 있는 중요한 클래스와 함수를 강조해 알려주는 모듈 소개이기도 한다.

```python
## words.py
"""단어와 언어 패턴을 찾을 떄 쓸 수 있는 라이브러리

여러 단어가 서로 어떤 연관 관계에 있는지 검사하는 것이 어려울 떄가 있다.
이 모듈은 단어가 가지는 특별한 특성ㅇ르 쉽게 결정할 수 있게 해준다.

사용 가능 함수:
- palindome: ..
- check_anagram: ..

```

### 클래스

- 공개 애트리뷰트와 메서드를 강조해 표시
- 이 클래스를 상위 클래스로 상속하는 하위 클래스가 보호 애트리뷰트나 메서드와 상호작용하는 방법 안내

```python
class Player:
    """Represents a player of the game.

    Subclasses may override the 'tick' method to provide
    custom animations for the player's movement depending
    on their power level, etc.

    Public attributes:
    - power: Unused power-ups (float between 0 and 1).
    - coins: Coins found during the level (integer).
    """
```

### 함수

첫 줄은 함수가 하는 일을 설명한다. 다음 단락부터는 함수 인자나 함수의 동작에 대해 구체적으로 설명한다. 반환 값이 있으면 이에 대해서도 설명해야 한다. 함수의 인터페이스에 속해 있으며 함수를 호출하는 쪽에서 꼭 처리해야 하는 예외도 설명해야 한다.

```python
def aaa(word, dictionary):
    """주어진 단어의 모든 어구전철을 찾는다.
   
    이 함수는 .... 실핸된다.

    Args:
        word: 대상 단어. 문자열
        dictiionary: 모든 단어가 들어 있는 collections.abc.Container 컬렉션.
   
    Returns:
        찾은 어구전철들로 이뤄진 리스트. 아무것도 찾지 못한 경우 Empty.
```

- 함수에 인자가 없고 반환 값만 있다면 설명은 한 줄로도 충분할 것이다.
- 함수가 아무 값도 반환하지 않는다면 'return None'이나 'None을 반환함'이라고 쓰는 것보다 아예 반환 값에 대한 설명을 제외하는 편이 더 낫다.
- 함수 인터페이스에 예외 발생이 포함된다면 발생하는 예외와 예외가 발생하는 상황에 대한 설명을 함수 독스트링에 반드시 포함시켜야 한다.
- 일반적인 동작 중에 함수가 예외를 발생시키지 않을 것으로 예상한다면 예외가 발생하지 않는다는 사실을 적지 말라.
- 함수가 가변 인자나 키워드 인자를 받는다면 문서화한 목록에 *args나 **kwargs를 사용하고 각각의 목적을 설명하라.
- 함수가 제너레이터라면, 독스트링에는 이 제너레이터를 이터레이션할 떄 어떤 값이 발생하는지 기술해야 한다.
- 함수가 비동기 코루틴이라면, 독스트링에 언제 이 코루틴의 비동기 실행이 중단되는지 설명해야 한다.

## Link

- [effective python](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=254321728)

