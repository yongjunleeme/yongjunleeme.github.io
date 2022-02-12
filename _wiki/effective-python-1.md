---
layout  : wiki
title   : 
summary : 
date    : 2022-02-12 23:51:40 +0900
updated : 2022-02-13 00:28:50 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## get으로 3개 값을 염두에 둘 때

```python
red = my_values.get('빨강', '') or 0

# 위에는 가독성이 나쁘니 아래로

def get_first_int(values, key, defulat=0):
    found = values.get(key, '')
    if found:
        return int(found[0])
    return default
```

## f-string

- <10s → 간격 맞추기

```python
pantry = [
    ('avocados', 1.25),
    ('bananas', 2.5),
    ('cherries', 15),
]

# Example 27
for i, (item, count) in enumerate(pantry):
    print(f'#{i+1}: '
          f'{item.title():<10s} = '
          f'{round(count)}')
```

## zip - zip_longest

- zip 제너레이터은 각 이터레이터의 다음 값이 들어 있는 튜플을 반환
- 자신이 감싼 이터레이터 중 어느 하나가 끝날 때까지 튜플을 내놓는다.
- zip_longest는 존재하지 않는 값을 fillvalue로 대신, 디폴트 fillvalue는 None

```python
names = ['Cecilia', 'Lise', 'Marie']
counts = [len(n) for n in names]

import itertools

for name, count in itertools.zip_longest(names, counts):
    print(f'{name}: {count}')
```

## 09. 루프 뒤 마친 후에 else 블록을 사용하지 말라

- 루프 마친 후에 else를 쓰면 바로 실행된다(이 블록이 실행되지 않으념 이 블록을 실행하라는 뜻이 있어서)
- 그러나 가독성에 좋지 않으니 else를 쓰지 말고 return을 쓰자

```python
# Example 1
for i in range(3):
    print('Loop', i)
else:
    print('Else block!')

def coprime(a, b):
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            return False
    return True

assert coprime(4, 9)
assert not coprime(3, 6)
```

## [10. 왈러스 연산자를 사용해 반복을 피하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_10.py)

```python
# Example 9
if (count := fresh_fruit.get('banana', 0)) >= 2:
    pieces = slice_bananas(count)
else:
    pieces = 0
```

## 13.슬라이싱보다는 나머지를 모두 잡아내는 언패킹을 사용하라

```python
car_ages = [0, 9, 4, 8, 7, 20, 19, 1, 6, 15]
car_ages_descending = sorted(car_ages, reverse=True)
oldest, second_oldest = car_ages_descending

oldest, second_oldest, *others = car_ages_descending
print(oldest, second_oldest, others)
```

## 클래스의 인스턴스를 딕셔너리로 쓰고 싶을 때?

```python
class MyClass:
    def __init__(self):
        self.alligator = 'hatchling'
        self.elephant = 'calf'

a = MyClass()
for key, value in a.__dict__.items():
    print(f'{key} = {value}')
```

## 참조를 통해 대입 없이 값을 변경

```python
votes = {
    'baguette': ['Bob', 'Alice'],
    'ciabatta': ['Coco', 'Deb'],
}

key = 'brioche'
who = 'Elmer'

if key in votes:
    names = votes[key]
else:
    votes[key] = names = []

names.append(who)
print(votes)
```

```python
try:
    names = votes[key]
except KeyError:
    votes[key] = names = []

names.append(who)

print(votes)
```

```python
if (names := votes.get(key)) is None:
    votes[key] = names = []

names.append(who)
```

## 키가 없는 경우의 로직 커스텀 `__missing__`

```python
class Pictures(dict):
    def __missing__(self, key):
        value = '원하는 디폴트 값'
        self[key] = value
        return value

pictures = Pictures()
handle = pictures[path]
```

## [14. 복잡한 기준을 사용해 정렬할 떄는 key 파라미터를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_14.py)

```python
# Example 2
class Tool:
    def __init__(self, name, weight):
        self.name = name
        self.weight = weight

    def __repr__(self):
        return f'Tool({self.name!r}, {self.weight})'

tools = [
    Tool('level', 3.5),
    Tool('hammer', 1.25),
    Tool('screwdriver', 0.5),
    Tool('chisel', 0.25),
]


# Example 3
try:
    tools.sort()
except:
    logging.exception('Expected')
else:
    assert False
```

정렬에 사용하고 싶은 애트리뷰트가 객체에 들어 있는 경우가 많다. 이런 상황을 지원하기 위해 sort에는 key라는 파라미터가 있다. key는 함수여야 한다. key 함수에는 정렬 중인 리스트의 원소가 전달된다. key 함수가 반환하는 값은 원소 대신 정렬 기준으로 사용할, 비교 가능한(즉, 자연스러운 순서가 정의된) 값이어야만 한다.

```python
print('Unsorted:', repr(tools))
tools.sort(key=lambda x: x.name)
print('\nSorted:  ', tools)

# Example 5
tools.sort(key=lambda x: x.weight)
print('By weight:', tools)
```

이 예제처럼 key로 전달된 람다 함수 내부에서는 원소 애트리뷰트에 접근하거나, 인덱스를 써서 값을 얻거나(원소가 시퀀스, 튜플, 딕셔너리인 경우), 제대로 작동하는 다른 모든 식을 사용할 수 있다.

심지어 문자열 같은 기본 타입인 경우에는 정렬하기 전에 key 함수를 사용해 원소 값을 변형할 수도 있다. 다음 예제는 lower 메서드를 사용해 리스트에 들어 있는 장소 이름을 소문자로 변환함으로써 첫 글자가 대문자든 소문자든 구분하지 않고 알파벳순으로 비교한다(이렇게 변환하는 이유는 문자열의 자연스로운 순서에서는 대문자가 소문자보다 더 앞에 오기 때문이다.)

```python
# Example 6
places = ['home', 'work', 'New York', 'Paris']
places.sort()
print('Case sensitive:  ', places)
places.sort(key=lambda x: x.lower())
print('Case insensitive:', places)
```

때로는 여러 기준을 사용해 정렬해야 할 수도 있다. 
파이썬에서 가장 쉬운 해법은 tuple 타입을 쓰는 것이다. 튜플은 임의의 파이썬 값을 넣을 수 있는 불변 값이다. 튜플은 기본적으로 비교 가능하며 자연스러운 순서가 정해져 있다. 이는 sort에 필요한 `__lt__` 정의가 들어 있다는 뜻이다.

이 특별 비교 메서드는 튜플의 각 위치를 이터레이션하면서 각 인덱스에 해당하는 원소를 한 번에 하나씩 비교하는 방식으로 구현돼 있다. 


```python
# Example 7
power_tools = [
    Tool('drill', 4),
    Tool('circular saw', 5),
    Tool('jackhammer', 40),
    Tool('sander', 4),
]


# Example 8
saw = (5, 'circular saw')
jackhammer = (40, 'jackhammer')
assert not (jackhammer < saw)  # Matches expectations

# Example 9
drill = (4, 'drill')
sander = (4, 'sander')
assert drill[0] == sander[0]  # Same weight
assert drill[1] < sander[1]   # Alphabetically less
assert drill < sander         # Thus, drill comes first
```

튜플 비교 동작 방식을 활용해 전동 공구 리스트를 weight로 먼저 정렬하고 그 후 name으로 정렬할 수 있다.

```python
# Example 10
power_tools.sort(key=lambda x: (x.weight, x.name))
print(power_tools)
```

튜플을 반환하는 key 함수의 한 가지 제약 사항은 모든 비교 기준의 정렬 순서가 같아야 한다는 점(모두 오름차순이거나 모두 내림차순)이다. sort 메서드에 reverse 파라미터를 넘기면 튜플에 들어 있는 두 기준의 정렬 순서가 똑같이 영향을 받는다.

숫자 값의 경우 단항(unary) 부호 반전(-) 연산자를 사용해 정렬 방향을 혼합할 수 있다. 부호 반전 연산자는 반환되는 튜플에 들어가는 값 중 하나의 부호를 반대로 만들기 떄문에 결과적으로 나머지 값의 정렬 순서는 그대로 둔 채로 반전된 값의 정렬 순서를 반대로 만든다.

```python
# Example 13
try:
    power_tools.sort(key=lambda x: (x.weight, -x.name),
                     reverse=True)
except:
    logging.exception('Expected')
else:
    assert False
```

아쉽지만 모든 타입에 부호 반전을 사용할 수는 없다. 

파이썬에서는 이런 상황을 위해 안정적인 정렬 알고리즘을 제공한다. 리스트 타입의 sort 메서드는 key 함수가 반환하는 값이 서로 같은 경우 리스트에 들어 있던 원래 순서를 그대로 유지해준다. 이는 같은 리스트에 대해 서로 다른 기준으로 sort를 여러 번 호출해도 된다는 뜻이다. 

```python
# Example 14
power_tools.sort(key=lambda x: x.name)   # Name ascending

power_tools.sort(key=lambda x: x.weight, # Weight descending
                 reverse=True)

print(power_tools)
```

다만 최종적으로 리스트에서 얻어내고 싶은 정렬 기준은 우선순위의 역순으로 정렬을 수행해야 한다는 사실을 꼭 기억해야 한다. 위 예제에서는 weight에 의해 내림차순으로 정렬하고 그 후 name에 의해 오름차순으로 정렬된 리스트를 원했으므로 먼저 name을 사용해 오름차순으로 정렬하고 그 후 weight을 사용해 내림차순으로 정렬해야 한다.

## 클로저

- 자신이 정의된 영역 밖의 변수를 참조하는 함수. 클로저로 인해 도우미 함수가 sort_priority 함수의 group 인자에 접근할 수 있다.

```python
# Example 1
def sort_priority(values, group):
    def helper(x):
        if x in group:
            return (0, x)
        return (1, x)
    values.sort(key=helper)

# Example 2
numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)
```

## 변수를 참조할 때 파이썬 인터프리터는 이 참조를 해결하기 위해 다음 순서로 영역을 뒤진다.

1. 현재 함수의 영역
2. 현재 함수를 둘러싼 영역
3. 현재 코드가 들어 있는 모듈의 영역(전역)
4. 내장 영역(built-in scope)(len, str 등)

변수에 값을 대입하는 것은 다른 방식으로 작동한다. 변수가 현재 영역에 이미 정의돼 있다면 그 변수의 값만 새로운 값으로 바뀐다. 하지만 변수가 현재 영역에 정의돼 있지 않다면 파이썬은 변수 대입을 변수 정의로 취급한다. 결정적으로 이렇게 새로 정의된 변수의 영역은 해당 대입문이나 식이 들어 있던 함수가 된다.

```python
def sort_priority2(numbers, group):
    found = False         # Scope: 'sort_priority2'
    def helper(x):
        if x in group:
            found = True  # Scope: 'helper' -- Bad!
            return (0, x)
        return (1, x)
    numbers.sort(key=helper)
    return found
```

이 동작의 의도는 함수에서 사용한 지역 변수가 그 함수를 포함하고 있는 모듈 영역을 더럽히지 못하게 막는 것이다. 이런 식으로 처리하지 않으면 함수 내에서 사용한 모든 대입문이 전역 모듈 영역에 쓰레기 변수를 추가하게 된다.

```python
def sort_priority3(numbers, group):
    found = False
    def helper(x):
        nonlocal found  # Added
        if x in group:
            found = True
            return (0, x)
        return (1, x)
    numbers.sort(key=helper)
    return found
```

nonlocal 문은 대입할 데이터가 클로저 밖에 있어서 다른 영역에 속한다는 사실을 분명히 알려준다. 이 문장은 변수 대입 시 직접 모듈 영역(전역 영역을 사용해야 한다고 지정 하는 global 문을 보완해준다.) nonlocal의 유일한 한계점은 (전역 영역을 더럽히지 못하도록) 모듈 수준 영역까지 변수 이름을 찾아 올라가지 않는다는 것뿐이다.

하지만 간단한 함수 외에는 nonlocal 사용하지 말라. 지정한 변수와 대입이 이뤄지는 위치의 거리가 멀면 함수 동작을 이해하기 힘들다.
위치인자(positional argument) = 가변인자(varargs) = 스타 인자(star args) = *args

위치인자, 가변인자, 키워드인자
`*` 연산자는 파이썬이 시퀀스의 원소들을 함수의 위치 인자로 넘길 것을 명령한다.

함수에 전달되기 전에 항상 튜플로 변환 이는 함수를 호출하는 쪽에서 제너레이터 앞에 * 연산자를 사용하면 제너레이터의 모든 원소를 얻기 위해 반복한다는 뜻이다. 메모리를 많이 소비할 수 있다는 의미. `*args`를 받는 함수는 인자 목록에서 가변적인 부분에 들어가는 인자의 개수가 처리하기 좋을 정도로 충분히 작다는사실을 이미 알고 있는 경우에 가장 적합. 여러 리터럴이나 변수 이름을 함께 전달하는 함수 호출에 이상적 *args를 받아들이는 함수를 확장할 떄는 키워드 기반의 인자만 사용해야 한다. 더 방어적으로 프로그래밍하려면 타입 애너테이션을 사용

```python
def log(message, *values):  # The only difference
    if not values:
        print(message)
    else:
        values_str = ', '.join(str(x) for x in values)
        print(f'{message}: {values_str}')

log('My numbers are', 1, 2)
log('Hi there')  # Much better
```

## 23. 키워드 인자로 선택적인 기능을 제공하라

```python
# Example 1
def remainder(number, divisor):
    return number % divisor

assert remainder(20, 7) == 6

# Example 2
remainder(20, 7)
remainder(20, divisor=7)
remainder(number=20, divisor=7)
remainder(divisor=7, number=20)
```

`**` 연산자는 딕셔너리의 각 값에 대응하는 키를 키워드로 사용하도록 명령한다.

```python
my_kwargs = {
	'number': 20,
	'divisor': 7,
}
assert remainder(**my_kwargs) == 6
```

아무 키워드 인자나 받는 함수를 만들고 싶다면, 모든 키워드 인자에 dict를 모아주는 **kwargs라는 파라미터를 사용한다. 함수 본문에서는 이 dict를 사용해 필요한 처리를 할 수 있다.
1. 코드를 처음 보는 사람들에게 함수 호출의 의미를 명확히 알려줄 수 있다.
2. 키워드 인자의 경우 함수 정의에서 디폴트 값을 지정할 수 있다.

- period가 선택적일 경우

```python
# Example 12
def flow_ratewty dictionary.
    """
    try:
        return json.loads(data)
    except ValueError:
        if default is None:
            default = {}
        return default
```

## 24.None과 독스트링을 사용해 동적인 디폴트 인자를 지정하라.

디폴트 인자의 값은 모듈이 로드될 떄 단 한 번만 평가→ 함수 정의되는 시점에 한 번만 호출되므로 타임스탬프가 같다.
이런 경우 디폴트 값으로 None을 지정하고 실제 동작을 독스트링에 문서화

```python
def log(message, when=None):
    """Log a message with a timestamp.
    Args:
        message: Message to print.
        when: datetime of when the message occurred.
            Defaults to the present time.
    """
    if when is None:
        when = datetime.now()
    print(f'{when}: {message}')
```

## 25. 위치로만 지정하는 인자

```python
# Example 13
def safe_division_d(numerator, denominator, /, *,  # Changed
                    ignore_overflow=False,
                    ignore_zero_division=False):
    try:
        return numerator / denominator
    except OverflowError:
        if ignore_overflow:
            return 0
        else:
            raise
    except ZeroDivisionError:
        if ignore_zero_division:
            return float('inf')
        else:
            raise
```

인자 목록에서 /와 * 기호 사이에 있는 모든 파라미터는 위치를 사용해 전달할 수도 있고 이름을 키워드로 사용해 전달할 수도 있다.

```python
def safe_division_e(numerator, denominator, /,
                    ndigits=10, *,                # Changed
                    ignore_overflow=False,
                    ignore_zero_division=False):
    try:
        fraction = numerator / denominator        # Changed
        return round(fraction, ndigits)           # Changed
    except OverflowError:
        if ignore_overflow:
            return 0
        else:
            raise
    except ZeroDivisionError:
        if ignore_zero_division:
            return float('inf')
        else:
            raise

# Example 17
result = safe_division_e(22, 7)
print(result)

result = safe_division_e(22, 7, 5)
print(result)

result = safe_division_e(22, 7, ndigits=2)
print(result)

```

## 26. functool.wrap을 사용해 함수 데코레이터를 정의하라

데코레이터는 자신이 감싸고 있는 함수가 호출되기 전과 후에 코드를 추가로 실행해준다.

- 인트로스펙션은 실행 시점에 프로그램이 어떻게 실행되는지 관찰하는 것을 뜻한다. 이와 비슷하게 실행 시점에 사용되기 때문에 가끔 혼동할 수 있는 용어로는 리플렉션이 있다. 리플렉션은 실행 시점에 프로그램을 조작하는 것을 뜻한다.

```python
# Example 8
from functools import wraps

def trace(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        print(f'{func.__name__}({args!r}, {kwargs!r}) '
              f'-> {result!r}')
        return result
    return wrapper

@trace
def fibonacci(n):
    """Return the n-th Fibonacci number"""
    if n in (0, 1):
        return n
    return fibonacci(n - 2) + fibonacci(n - 1)
```

## 27. 컴프리헨션

컴프리헨션 조건식

```python
even_squares = [x**2 for x in a if x % 2 == 0]
print(even_squares)
```

딕셔너리

```python
even_squares_dict = {x: x**2 for x in a if x % 2 == 0}
```

다중 루프

```python
# Example 1
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [x for row in matrix for x in row]
print(flat)

# Example 2
squared = [[x**2 for x in row] for row in matrix]
print(squared)
```

여러 조건을 같은 수준의 루프에 사용하면 암시적으로 and 식을 의미한다. 다음 두 식은 같은 역할

```python
# Example 5
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
b = [x for x in a if x > 4 if x % 2 == 0]
c = [x for x in a if x > 4 and x % 2 == 0]
print(b)
print(c)
assert b and c
assert b == c
```

- 왈러스 연산자
- 조건이 아닌 부분에도 대입식을 사용할 수 있지만 피해야 하는 형태

```python
# Example 4
found = {name: batches for name in order
         if (batches := get_batches(stock.get(name, 0), 8))}
assert found == {'screws': 4, 'wingnuts': 1}, found
```

## 30. 리스트를 반환하기보다는 제너레이터를 사용하라

제너레이터가 반환하는 이터레이터를 리스트 내장 함수에 넘기면 필요할 때 제너레이터를 쉽게 리스트로 변환할 수 있다.
ex6 함수의 작업 메모리는 입력 중 가장 긴 줄의 길이로 제한된다.
제너레이터가 반환하는 이터레이터에 상태가 있기 때문에 호출할 때 재사용이 불가능하다.

```python
address = 'Four score and seven years ago our fathers brought forth on this continent a new nation, conceived in liberty, and dedicated to the proposition that all men are createdequal.'
result = index_words(address)

# Example 3
def index_words_iter(text):
    if text:
        yield 0
    for index, letter in enumerate(text):
        if letter == ' ':
            yield index + 1

# Example 4
it = index_words_iter(address)
print(next(it))
print(next(it))

# Example 5
result = list(index_words_iter(address))

# Example 6
def index_file(handle):
    offset = 0
    for line in handle:
        if line:
            yield offset
        for letter in line:
            offset += 1
            if letter == ' ':
                yield offset
```

# Generator(제네레이터)

## 31. 인자에 대해 이터레이션할 떄는 방어적이 돼라

제너레이터가 반환하는 이터레이터는 제너레이터 함수의 본문에서 yield가 반환하는 값들로 이뤄진 집합을 만들어낸다.
제너레이터를 사용하면 작업 메모리에 모든 입력과 출력을 저장할 필요가 없으므로 입력이 아주 커도 출력 

```python
# Example 3
def index_words_iter(text):
    if text:
        yield 0
    for index, letter in enumerate(text):
        if letter == ' ':
            yield index + 1

# Example 4
it = index_words_iter(address)
print(next(it))
print(next(it))

# Example 5
result = list(index_words_iter(address))
print(result[:10])
```

호출될 때마다 새로운 이터레이터를 반환하는 함수를 받는다

```python
# Example 8
def normalize_func(get_iter):
    total = sum(get_iter())   # New iterator
    result = [출해서 두 번째 이터레이터 객체를 만든다. 두 이터레이터는 서로 독립적으로 진행되고 소진된다. 이로 인해 두 이터레이터는 모든 입력 데이터값을 볼 수 있는 별도의 이터레이션을 만들어낸다. 이 접근 방법의 단점은 입력 데이터를 여러 번 읽는다는 것이다.

프로토콜에 따르면 이터레이터가 iter 내장 함수에 전달되는 경우에는 전달받은 이터레이터가 그대로 반환된다. 반대로 컨테이너 타입이 iter에 전달되면 매번 새로운 이터레이터 객체가 반환된다. 따라서 입력값이 이런 동작을 하는지 검사해서 반복적으로 이터레이션 할 수 없는 인자인 경우네는 TypeError를 발생시켜서 인자를 거부할 수 있다.

```python
def normalize_defensive(numbers):
    if iter(numbers) is numbers:  # An iterator -- bad!
        raise TypeError('Must supply a container')
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

visits = [15, 35, 80]
normalize_defensive(visits)
```

```python
from collections.abc import Iterator 

def normalize_defensive(numbers):
    if isinstance(numbers, Iterator):  # Another way to check
        raise TypeError('Must supply a container')
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

visits = [15, 35, 80]
normalize_defensive(visits)  # No error
```

## 32. 제네레이터 식

입력이 크면 메모리를 너무 많이 사용하기 때문에 리스트 컴프리헨션은 문제를 일으킬 수 있다.
제네레이터 식은 이터레이터처럼 한 번에 원소를 하나씩 출력하기 때문에 메모리 문제를 피할 수 있다.
서로 연결된 제너레이터 식은 매우 빠르게 실행되며 메모리도 효율적으로 사용한다.

```python
roots = ((x, x**0.5) for x in it)
```

## 33. yield from 

- yield from 식을 사용하면 여러 내장 제너레이터를 모아서 제너레이터 하나로 합성할 수 있다.
- 직접 내포된 제너레이터를 이터레이션함녀서 각 제너레이터의 출력을 내보내는 것보다 yield from을 사용하는 것이 성능 면에서 더 좋다.
- 제어를 부모 제너레이터에게 전달하기 전에 내포된 제너레이터가 모든 값을 내보낸다.
- yield from은 파이썬 인터프리터가 여러분 대신 for 루프를 내포시키고 yield식을 처리하도록 만든다. 이로 인해 성능도 더 좋아진다.

```python
def move(period, speed):
    for _ in range(period):
        yield speed

def pause(delay):
    for _ in range(delay):
        yield 0

def render(delta):
    print(f'Delta: {delta:.1f}')
    # Move the images onscreen

def run(func):
    for delta in func():
        render(delta)

# Example 4
def animate_composed():
    yield from move(4, 5.0)
    yield from pause(3)
    yield from move(2, 3.0)

run(animate_composed)
```

## [34.send로 제너레이터에 데이터를 주입하지 말라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_34.py)

> 함수의 인자를 이런 식으로 사용하는군..

```python
# Example 1
import math

def wave(amplitude, steps):
    step_size = 2 * math.pi / steps
    for step in range(steps):
        radians = step * step_size
        fraction = math.sin(radians)
        output = amplitude * fraction
        yield output


# Example 2
def transmit(output):
    if output is None:
        print(f'Output is None')
    else:
        print(f'Output: {output:>5.1f}')

def run(it):
    for output in it:
        transmit(output)

run(wave(3.0, 8))
```

send 메서드를 호출하면 제너레이터가 재개될 때 yield가 send에 전달된 파라미터 값을 반환한다. 하지만 방금 시작한 제너레이터는 아직 yield식에 도달하지 못했기 때문에 최초로 send를 호출할 때 인자로 전달할 수 있는 유일한 값은 None 뿐이다.


## [35. 제네레이터 안에서 throw로 상태를 변화시키지 말라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_35.py)

## [36. 이터레이터나 제너레이터를 다룰 때는 itertools를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_36.py)

### Chain

- 여러 이터레이터를 하나의 순차적인 이터레이터로 합치고 싶을 때 

```python
import itertools

# Example 2
it = itertools.chain([1, 2, 3], [4, 5, 6])
print(list(it))
```

### repeat

- 한 값을 계속 반복해 내놓고 싶을 때 repeat을 사용. 두 번쨰 인자로 최대 횟수를 지정

```python
# Example 3
it = itertools.repeat('hello', 3)
print(list(it))
```

### cycle

- 이터레이터가 내놓은 원소들을 계속 반복하고 싶을 때

```python
# Example 4
it = itertools.cycle([1, 2])
result = [next(it) for _ in range (10)]
print(result)
```

### tee

- 한 이터레터를 병렬 적으로 두 번쨰로 인자로 지정된 개수의 이터레이터로 만들고 싶을 때

```python
# Example 5
it1, it2, it3 = itertools.tee(['first', 'second'], 3)
print(list(it1))
print(list(it2))
print(list(it3))
```

### takewhile

- 주어진 술어가 False를 반환하는 첫 원소가 나타날 때까지 원소를 돌려준다.

```python
# Example 8
values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
less_than_seven = lambda x: x < 7
it = itertools.takewhile(less_than_seven, values)
print(list(it))
```

### dropwhile

- takewhile의 반대

### filterfalse

- filterfalse는 filter의 내장 함수로 주어진 이터레이터에서 술어가 False를 반환하는 모든 원소를 돌려준다.

```python
# Example 10
values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = lambda x: x % 2 == 0

filter_result = filter(evens, values)
print('Filter:      ', list(filter_result))

filter_false_result = itertools.filterfalse(evens, values)
print('Filter false:', list(filter_false_result))
```

# 클래스와 인터페이스

## 37. 내장 타입을 여러 단계로 내포시키기보다는 클래스를 합성하라

- [defaultdict 설명](https://dongdongfather.tistory.com/69)
- 딕셔너리가 과목(키)을 성적의 리스트(값)로 매핑하던 것을 (성적, 가중치) 튜플의 리스트로 매핑하도록 변경

```python
class WeightedGradebook:
    def __init__(self):
        self._grades = {}

    def add_student(self, name):
        self._grades[name] = defaultdict(list)

    def report_grade(self, name, subject, score, weight):
        by_subject = self._grades[name]
        grade_list = by_subject[subject]
        grade_list.append((score, weight))
```

내포 단계가 두 단계 이상이 되면 더이상 딕셔너리, 리스트, 튜플 계층을 추가하지 말아야 한다.
namedtuple과 클래스로 리팩토링

```python
# Example 12
class Subject:
    def __init__(self):
        self._grades = []

    def report_grade(self, score, weight):
        self._grades.append(Grade(score, weight))

    def average_grade(self):
        total, total_weight = 0, 0
        for grade in self._grades:
            total += grade.score * grade.weight
            total_weight += grade.weight
        return total / total_weight


# Example 13
class Student:
    def __init__(self):
        self._subjects = defaultdict(Subject)

    def get_subject(self, name):
        return self._subjects[name]

    def average_grade(self):
        total, count = 0, 0
        for subject in self._subjects.values():
            total += subject.average_grade()
            count += 1
        return total / count


# Example 14
class Gradebook:
    def __init__(self):
        self._students = defaultdict(Student)

    def get_student(self, name):
        return self._students[name]


# Example 15
book = Gradebook()
albert = book.get_student('Albert Einstein')
math = albert.get_subject('Math')
math.report_grade(75, 0.05)
math.report_grade(65, 0.15)
math.report_grade(70, 0.80)
gym = albert.get_subject('Gym')
gym.report_grade(100, 0.40)
gym.report_grade(85, 0.60)
print(albert.average_grade())
```

## 38. 간단한 인터페이스의 경우 클래스 대신 함수를 받아라

api가 실행하는 과정에서 함수를 실행하는 경우 이런 함수를 훅(hook)이라고 부른다.

```python
names = ['Socrates', 'Archimedes', 'Plato', 'Aristotle']
names.sort(key=len)
print(names)
```

파이썬에서는 일급 함수를 사용해 객체에 대한 CountMissing.missing 메서드를 직접 defaultdict의 디폴트 값 훅으로 전달할 수 있다.

```python
from collections import defaultdict

current = {'green': 12, 'blue': 3}
increments = [
    ('red', 5),
    ('blue', 17),
    ('orange', 9),
]

# Example 6
class CountMissing:
    def __init__(self):
        self.added = 0

    def missing(self):
        self.added += 1
        return 0


# Example 7
counter = CountMissing()
result = defaultdict(counter.missing, current)  # Method ref
for key, amount in increments:
    result[key] += amount
assert counter.added == 2
print(result)
```

- `__call__`을 사용하면 객체를 함수처럼 호출할 수 있다.



```python
# Example 8
class BetterCountMissing:
    def __init__(self):
        self.added = 0

    def __call__(self):
        self.added += 1
        return 0

counter = BetterCountMissing()
assert counter() == 0
assert callable(counter)


# Example 9
counter = BetterCountMissing()
result = defaultdict(counter, current)  # Relies on __call__
for key, amount in increments:
    result[key] += amount
assert counter.added == 2
print(result)
```

## [39. 객체를 제너릭하게 구성하려면 @classmethod를 통한 다형성을 활용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_39.py)

다형성을 사용하면 계층을 이루는 여러 클래스가 자신에게 맞는 유일한 메서드 버전을 구현할 수 있다.
클래스로 만들어낸 개별 객체에 적용되지 않고 클래스 전체에 적용

```python
# Example 10
class GenericInputData:
    def read(self):
        raise NotImplementedError

    @classmethod
    def generate_inputs(cls, config):
        raise NotImplementedError


# Example 11
class PathInputData(GenericInputData):
    def __init__(self, path):
        super().__init__()
        self.path = path

    def read(self):
        with open(self.path) as f:
            return f.read()

    @classmethod
    def generate_inputs(cls, config):
        data_dir = config['data_dir']
        for name in os.listdir(data_dir):
            yield cls(os.path.join(data_dir, name))
```

GenericWorker 클래스 안에 create_workers 도우미 메서드를 추가할 수 있다. 이 도우미 메서드는 GenericInputData의 하위 타입이어야 하는 input_class를 파라미터로 받는다. input_class는 필요한 입력을 생성해준다. GenericWorker의 구체적인 하위 타입의 인스턴스를 만들 떄는 (클래스 메서드인 create_workers가 첫 번째 파라미터로 받은) cls()를 제너릭 생성자로 사용한다.

```python
from threading import Thread

def execute(workers):
    threads = [Thread(target=w.map) for w in workers]
    for thread in threads: thread.start()
    for thread in threads: thread.join()

    first, *rest = workers
    for worker in rest:
        first.reduce(worker)
    return first.result

# Example 12
class GenericWorker:
    def __init__(self, input_data):
        self.input_data = input_data
        self.result = None

    def map(self):
        raise NotImplementedError

    def reduce(self, other):
        raise NotImplementedError

    @classmethod
    def create_workers(cls, input_class, config):
        workers = []
        for input_data in input_class.generate_inputs(config):
            workers.append(cls(input_data))
        return workers
``` 

이 코드에서 input_class.generate_inputs 호출이 바로 여기서 보여주려는 클래스 다형성의 예다. create_workers가 `__init__` 직접 호출하지 않고 cls()를 호출함으로써 다른 방법으로 GenericWorker 객체를 만들 수 있다는 것도 알 수 있다.
이런 변경이 구체적인 GenericWorker 하위 클래스에 미치는 영향은 부모 클래스를 바꾸는 것 뿐이다.

```python
# Example 13
class LineCountWorker(GenericWorker):
    def map(self):
        data = self.input_data.read()
        self.result = data.count('\n')

    def reduce(self, other):
        self.result += other.result


# Example 14
def mapreduce(worker_class, input_class, config):
    workers = worker_class.create_workers(input_class, config)
    return execute(workers)


# Example 15
config = {'data_dir': tmpdir}
result = mapreduce(LineCountWorker, PathInputData, config)
print(f'There are {result} lines')
```

- @classmethod를 사용하면 클래스에 다른 생성자를 정의할 수 있다.
- *클래스 메서드 다형성을 활용하면 여러 구체적인 하위 클래스의 객체를 만들고 연결하는 제너릭한 방법을 제공할 수 있다.*

## [40. super로 부모 클래스를 초기화하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_40.py)

다이아몬드 상속이란 어떤 클래스가 두 가지 서로 다른 클래스를 상속하는데, 두 상위 클래스의 상속 계층을 거슬러 올라가면 같은 조상 클래스가 존재하는 경우를 뜻한다.
super와 표준메서드 결정순서(Method Resolution Order, MRO)
super를 사용하면 다이아몬드 계층의 공통 상위 클래스를 단 한 번만 호출하도록 보장

```python
# Example 9
class MyBaseClass:
    def __init__(self, value):
        self.value = value

class TimesSevenCorrect(MyBaseClass):
    def __init__(self, value):
        super().__init__(value)
        self.value *= 7

class PlusNineCorrect(MyBaseClass):
    def __init__(self, value):
        super().__init__(value)
        self.value += 9


# Example 10
class GoodWay(TimesSevenCorrect, PlusNineCorrect):
    def __init__(self, value):
        super().__init__(value)

foo = GoodWay(5)
print('Should be 7 * (5 + 9) = 98 and is', foo.value)
```

호출 순서는 이 클래스에 대한 MRO 정의를 따른다

```python
mro_str = '\n'.join(repr(cls) for cls in GoodWay.mro())
print(mro_str)
```

또한 super 함수에 두 가지 파라미터를 넘길 수 있다. 첫 번째 파라미터는 여러분이 접근하고 싶은 MRO 뷰를 제공할 부모 타입이고, 두 번째 파라미터는 첫 번째 파라미터로 지정한 타입의 MRO 뷰에 접근할 때 사용할 인스턴스다.

```python
class ExplicitTrisect(MyBaseClass):
    def __init__(self, value):
        super(ExplicitTrisect, self).__init__(value)
        self.value /= 3
assert ExplicitTrisect(9).value == 3
```

[41. 기능을 합성할 때는 믹스인 클래스를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_41.py)

다중 상속이 제공하는 편의와 캡슐화가 필요하지만 다중 상속으로 인해 발생할 수 있는 골치 아픈 경우는 피하고 싶다면 믹스인을 고려하라. 믹스인은 자식 클래스가 사용할 메서드 몇 개만 정의하는 클래스다. 믹스인 클래스에는 자체 애트리뷰트 정의가 없으므로 믹스인 클래스의 `__init__` 메서드를 호출할 필요도 없다.

```python
# Example 1
class ToDictMixin:
    def to_dict(self):
        return self._traverse_dict(self.__dict__)


# Example 2
    def _traverse_dict(self, instance_dict):
        output = {}
        for key, value in instance_dict.items():
            output[key] = self._traverse(key, value)
        return output

    def _traverse(self, key, value):
        if isinstance(value, ToDictMixin):
            return value.to_dict()
        elif isinstance(value, dict):
            return self._traverse_dict(value)
        elif isinstance(value, list):
            return [self._traverse(key, i) for i in value]
        elif hasattr(value, '__dict__'):
            return self._traverse_dict(value.__dict__)
        else:
            return value


# Example 3
class BinaryTree(ToDictMixin):
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```

믹스인의 가장 큰 장점은 제너릭 기능을 쉽게 연결할 수 있고 필요할 때기존 기능을 다른 기능으로 오버라이드해 변경할 수 있다는것이다. 예를 들어 다음 코드는 BinaryTree에 대한 참조를 저장하는 BinaryTree의 하위 클래스를 정의한다. 이런 순환 참조가 있으면 원래의 ToDictMixin.to_dict 구현은 무한 루프를 돈다.

...

...

# [44. 세터와 게터 메서드 대신 평범한 애트리뷰트를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_44.py)

클래스가 시간이 지남에 따라 진화하기 때문에 클래스를 설계할 때는 클래스를 호출하는 쪽에 영향을 미치지 않음을 보장하는 것이 중요하다.
나중에 애트리뷰트가 설정될 때 특별한 기능을 수행해야 한다면, 애트리뷰트를 @property 데코레이터와 대응하는 setter 애트리뷰트로 옮겨갈 수 있다. 다음 코드는 Registor라는 새 하위 클래스를 만든다. Registor에서 voltage 프로퍼티에 값을 대입하면 current 값이 바뀐다. 코드가 제대로 작동하려면 세터와 게터의 이름이 우리가 의도한 프로퍼티 이름과 일치해야 한다.

```python
class Resistor:
    def __init__(self, ohms):
        self.ohms = ohms
        self.voltage = 0
        self.current = 0

r1 = Resistor(50e3)
r1.ohms = 10e3
print(f'{r1.ohms} ohms, '
      f'{r1.voltage} volts, '
      f'{r1.current} amps')


# Example 6
class VoltageResistance(Resistor):
    def __init__(self, ohms):
        super().__init__(ohms)
        self._voltage = 0

    @property
    def voltage(self):
        return self._voltage

    @voltage.setter
    def voltage(self, voltage):
        self._voltage = voltage
        self.current = self._voltage / self.ohms


# Example 7
r2 = VoltageResistance(1e3)
print(f'Before: {r2.current:.2f} amps')
r2.voltage = 10
print(f'After:  {r2.current:.2f} amps')
```

프로퍼티에 대해 setter를 지정하면 타입을 검사하거나 클래스 프로퍼티에 전달된 값에 대한 검증을 수행할 수 있다. 

```python
# Example 8
class BoundedResistance(Resistor):
    def __init__(self, ohms):
        super().__init__(ohms)

    @property
    def ohms(self):
        return self._ohms

    @ohms.setter
    def ohms(self, ohms):
        if ohms <= 0:
            raise ValueError(f'ohms must be > 0; got {ohms}')
        self._ohms = ohms

# Example 9
try:
    r3 = BoundedResistance(1e3)
    r3.ohms = 0
except:
    logging.exception('Expected')
else:
    assert False
```

심지어 @property를 사용해 부모 클래스에 정의된 애트리뷰트를 불변으로 만들 수도 있다.


```python
# Example 11
class FixedResistance(Resistor):
    def __init__(self, ohms):
        super().__init__(ohms)

    @property
    def ohms(self):
        return self._ohms

    @ohms.setter
    def ohms(self, ohms):
        if hasattr(self, '_ohms'):
            raise AttributeError("Ohms is immutable")
        self._ohms = ohms


# Example 12
try:
    r4 = FixedResistance(1e3)
    r4.ohms = 2e3
except:
    logging.exception('Expected')
else:
    assert False
```

@property 메서드를 사용해 세터와 게터를 구현할 때는 게터나 세터 구현이 예기치 않은 동작을 수행하지 않도록 만들어야 한다. 예를 들어 게터 프로터피 메서드 안에서 다른 애트리뷰트를 설정하면 안 된다.

```python
# Example 13
class MysteriousResistor(Resistor):
    @property
    def ohms(self):
        self.voltage = self._ohms * self.current
        return self._ohms

    @ohms.setter
    def ohms(self, ohms):
        self._ohms = ohms
```

게터나 세터를 정의할 때 가장 좋은 정책은 관련이 있는 객체 상태를 @property.settet 메서드 안에서만 변경하는 것이다. 시간이 오래걸리는 함수 호출 등을 하면 안 된다. 클래스를 사용하는 쪽에서는 애트리뷰트가 다른 일반적인 객체와 마찬가지로 빠르고 사용하기 쉬울 것이라고 예상한다.
@property의 가장 큰 단점은 애트리뷰트를 처리하는 메서드가 하위 클래스 사이에서면 공유될 수 있다는 것이다. 서로 관련이 없는 클래스 사이에 같은 (프로퍼티 게터나 세터) 구현을 공유할 수는 없다. 하지만 파이썬은 재사용 가능한 프로퍼티 로직을 구현 할 때는 물론 다른 여러 용도에도 사용할 수 있는 디스크립터를 제공한다.

- 새로운 클래스 인터페이스를 정의할 때는 간단한 공개 애트리뷰트에서 시작하고, 세터나 게터 메서드를 가급적 사용하지 말라.

## [46. 재사용 가능한 @property 메서드를 만들려면 디스크립터를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_46.py)

디스크립터 프로토콜은 파이썬 언어에서 애트리뷰트 접근을 해석하는 방법을 정의한다. 디스크립터 클래스는 `__get__`과 `__set__` 메서드를 제공한다.

```python
exam = Exam()
exam.writind_grade =40
```

은 다음과 같이 해석된다.

```python
Exam.__dict__['writing_grade'].__set__(exam, 40)
```

다음과 같이 프로터티를 읽으면

```python
exam.writing_grade
```

다음과 같이 해석된다.

```python
Exam.__dict__['writing_grade'].__get__(exam, Exam)
```

이런 동작을 이끌어내는 것은 오브젝트의 `__getattribute__` 메서드다. 간단히 말해, Exam 인스턴스에 riting_grade라는 이름의 애트리뷰트가 없으면 파이썬은 Exam 클래스의 애트리뷰트를 대신 사용한다. 이 클래스의 애트리뷰트가 `__get__`과 `__set__` 메서드가 정의된 객체라면 파이썬은 디스크립터 프로토콜을 따라야한다고 결정한다.

Grade 클래스가 각각의 유일한 Exam 인스턴스에 대해 따로 값을 추적하게 해야 한다. 인스턴스별 상태를 딕셔너리에 저장하면 이런 구성이 가능하다.

```python
# Example 13
class Grade:
    def __init__(self):
        self._values = {}

    def __get__(self, instance, instance_type):
        if instance is None:
            return self
        return self._values.get(instance, 0)

    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError(
                'Grade must be between 0 and 100')
        self._values[instance] = value
```

이 구현은 잘 동작하지만 메모리를 누수시킨다. `_values` 딕셔너리는 프로그램이 실행하는 동안 `__set__` 호출에 전달된 모든 Exam 인스턴스에 대한 참조를 저장하고 있다. 이로 인해 인스턴스에 대한 참조 카운터가 절대로 0이 될 수 없고, 따라서 쓰레기 수집기가 인스턴스 메모리를 결코 재활용하지 못한다.
이 문제를 해결하기 위해 파이썬 weakref 내장 모듈을 사용할 수 있다. WeakKeyDictionary의 독특한 부분은 딕셔너리에 객체를 저장할 때 일반적인 강한 참조 대신에 약한 참조를 사용한다는 점이다. 파이썬 쓰레기 수집기는 약한 참조로만 참조되는 객체가 사용 중인 메모리를 언제든지 재활용할 수 있다. 따라서 WeakDictionary를 사용해 `_values`에 저장된 Exam 인스턴스가 더 이상 쓰이지 않는다면(해당 객체를 가리키는 모든 강한 참조가 사라졌다면) 쓰레기 수집기가 해당 메모리를 재활용할 수 있으므로 더 이상 메모리 누수가 없다.

```python
# Example 14
from weakref import WeakKeyDictionary

class Grade:
    def __init__(self):
        self._values = WeakKeyDictionary()

    def __get__(self, instance, instance_type):
        if instance is None:
            return self
        return self._values.get(instance, 0)

    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError(
                'Grade must be between 0 and 100')
        self._values[instance] = value


# Example 15
class Exam:
    math_grade = Grade()
    writing_grade = Grade()
    science_grade = Grade()

first_exam = Exam()
first_exam.writing_grade = 82
second_exam = Exam()
second_exam.writing_grade = 75
print(f'First  {first_exam.writing_grade} is right')
print(f'Second {second_exam.writing_grade} is right')
```

## [지연 계산 애트리뷰트가 필요하면 __getattr__, __getarrtibute__, __setattr__을 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_47.py)


어떤 클래스 안에 `__getattr__` 메서드 정의가 있으면, 이 객체의 인스턴스 딕셔너리에서 찾을 수 없는 애트리뷰트에 접근할 때마다 `__getattr__`이 호출된다.

```python
# Example 1
class LazyRecord:
    def __init__(self):
        self.exists = 5

    def __getattr__(self, name):
        value = f'Value for {name}'
        setattr(self, name, value)
        return value


# Example 2
data = LazyRecord()
print('Before:', data.__dict__)
print('foo:   ', data.foo)
print('After: ', data.__dict__)
```

```python
# Example 3
class LoggingLazyRecord(LazyRecord):
    def __getattr__(self, name):
        print(f'* Called __getattr__({name!r}), '
              f'populating instance dictionary')
        result = super().__getattr__(name)
        print(f'* Returning {result!r}')
        return result

data = LoggingLazyRecord()
print('exists:     ', data.exists)
print('First foo:  ', data.foo)
print('Second foo: ', data.foo)
```

exists 애트리뷰트가 인스턴스 딕셔너리에 있으므로 `__getattr__`이 결코 호출되지 않는다. 반면 foo 애트리뷰트는 처음에 인스턴스 딕셔너리에 없으므로 맨 처음 foo에 접근하면 `__getattr__`이 호출된다. 하지만 foo에 접근하면 `__getattr__`이 호출되고, 안에서 setattr을 수행해 인스턴스 딕셔너리 안에 foo라는 애트리뷰트를 추가한다. 따라서 두 번째로 foo에 접근하면 `__getarrt__`이 호출되지 않는다는 사실을 로그에서 확인할 수 있다.
이러한 기능은 스키마가 없는 데이터에 지연 계산으로 접근하는 등의 활용이 필요할 때 유용하다. 스키마가 없는 데이터에 접근하면 `__getattr__`이 한 번 실행되면서 프로퍼티를 적재하는 힘든 작업을 모두 처리한다. 이후 모든 데이터 접근은 기존 결과를 읽게 된다.

이 데이터베이스 시스템 안에서 트랜잭션이 필요하다고 하자. 이제는 사용자가 프로퍼티에 접근할 때 상응하는 데이터베이스에 있는 레코드가 유효한지, 그리고 트랜잭션이 여전히 열려 있는지 판단해야 한다. 기존 애트리뷰트를 확인하는 빠른 경로로 객체의 인스턴스 딕셔너리를 사용하기 때문에 `__getattr__` 훅으로는 이런 기능을 안정적으로 만들 수 없다.

이와 같은 고급 사용법을 제공하기 위해 파이썬은 `__getattribute__`라는 다른 object 훅을 제공한다. 이 특별 메소드는 객체의 애트리뷰트에 접근할 때마다 호출된다. 심지어 애트리뷰트 디렉터리에 존재하는 애트리뷰트에 접근할 때도 이 훅이 호출된다. 이를 사용하면 프로퍼티에 접근할 때마다 항상 전역 트랜잭션 상태를 검사하는 등의 작업을 수행할 수 있다. 이런 연산은 부가 비용이 많이 들고 성능에 부정적인 영향을 끼치기도 하지만 때로는 이런 비용을 감수할 만한 가치를 지닌 경우도 이따는 점을 명심하자. 다름 코드는 `__getattribute__`가 호출될 때마다 로그를 남기는 ValidationRecord를 정의한다.

```python
# Example 4
class ValidatingRecord:
    def __init__(self):
        self.exists = 5

    def __getattribute__(self, name):
        print(f'* Called __getattribute__({name!r})')
        try:
            value = super().__getattribute__(name)
            print(f'* Found {name!r}, returning {value!r}')
            return value
        except AttributeError:
            value = f'Value for {name}'
            print(f'* Setting {name!r} to {value!r}')
            setattr(self, name, value)
            return value

data = ValidatingRecord()
print('exists:     ', data.exists)
print('First foo:  ', data.foo)
print('Second foo: ', data.foo)
```

이제 내 파이썬 객체에 값이 대입된 경우, 나중에 이 값을 데이터베이스에 다시 저장하고 싶다고 하자. 임의의 애트리뷰트에 값을 설정할 떄마다 호출되는 object 훅인 `__setattr__`을 사용하면, 이런 기능을 비슷하게 구현할 수 있다. `__getattr__`이나 `__getattribute__`로 값을 읽을 때와 달리 메서드가 두 개 있을 필요가 없다. `__setattr__`은 인스턴스의 애트리뷰트에 (직접 대입하든 setattr 내장 함수를 통해서든) 대입이 이뤄질 때마다 항상 호출된다.

```python
# Example 8
class SavingRecord:
    def __setattr__(self, name, value):
        # Save some data for the record
        pass
        super().__setattr__(name, value)
```

이 클래스에 속한 인스턴스의 애트리뷰트 값을 설정할 때마다 `__setattr__` 메서드가 항상 호출된다.

```python
# Example 9
class LoggingSavingRecord(SavingRecord):
    def __setattr__(self, name, value):
        print(f'* Called __setattr__({name!r}, {value!r})')
        super().__setattr__(name, value)

data = LoggingSavingRecord()
print('Before: ', data.__dict__)
data.foo = 5
print('After:  ', data.__dict__)
data.foo = 7
print('Finally:', data.__dict__)
```

`super().__getattribute__`를 호출해 인스턴스 애트리뷰트 딕셔너리에서 값을 가져면 재귀를 피할 수 있다.

```python
class DictionaryRecord:
    def __init__(self, data):
        self._data = data

    def __getattribute__(self, name):
        # Prevent weird interactions with isinstance() used
        # by example code harness.
        if name == '__class__':
            return DictionaryRecord
        print(f'* Called __getattribute__({name!r})')
        data_dict = super().__getattribute__('_data')
        return data_dict[name]

data = DictionaryRecord({'foo': 3})
print('foo: ', data.foo)
```

## [48. `__init_subclass__`를 사용해 하위 클래스를 검증하라]

검증에 메타클래스를 사용하면, 프로그램 시작 시 클래스가 정의된 모듈을 처음 임포트할 때와 같은 시점에 검증이 이뤄지기 때문에 예외가 훨씬 더 빨리 발생할 수 있다.
메타클래스는 type을 상속해 정의된다. 기본적인 경우 메타클래스는 `__new__` 메서드를 통해 자신과 연관된 클래스의 내용을 받는다.

```python
# Example 1
class Meta(type):
    def __new__(meta, name, bases, class_dict):
        global print
        orig_print = print
        print(f'* Running {meta}.__new__ for {name}')
        print('Bases:', bases)
        print = pprint
        print(class_dict)
        print = orig_print
        return type.__new__(meta, name, bases, class_dict)

class MyClass(metaclass=Meta):
    stuff = 123

    def foo(self):
        pass

class MySubclass(MyClass):
    other = 567

    def bar(self):
        pass
```

- name: 클래스 이름, bases: 클래스가 상속하는 부모 클래스들(bases), 
메타클래스는 클래스 이름, 클래스가 상속하는 부모클래스들(bases), class 본문에 정의된 모든 클래스 애트리뷰트에 접근할 수 있다.

연관된 클래스가 정의되기 전에 이 클래스의 모든 파라미터를 검증하려면 `Meta.__new__`에 기능을 추가해야 한다.

```python
# Example 2
class ValidatePolygon(type):
    def __new__(meta, name, bases, class_dict):
        # Only validate subclasses of the Polygon class
        if bases:
            if class_dict['sides'] < 3:
                raise ValueError('Polygons need 3+ sides')
        return type.__new__(meta, name, bases, class_dict)

class Polygon(metaclass=ValidatePolygon):
    sides = None  # Must be specified by subclasses

    @classmethod
    def interior_angles(cls):
        return (cls.sides - 2) * 180

class Triangle(Polygon):
    sides = 3

class Rectangle(Polygon):
    sides = 4

class Nonagon(Polygon):
    sides = 9

assert Triangle.interior_angles() == 180
assert Rectangle.interior_angles() == 360
assert Nonagon.interior_angles() == 1260
```

파이썬 3.6에서는 메타클래스를 정의하지 않고 같은 동작을 구현할 수 있는 더 단순한 구문(`__init_subclass__` 특별 클래스 메서드를 정의하는 방식)이 추가됐다.

```python
# Example 4
class BetterPolygon:
    sides = None  # Must be specified by subclasses

    def __init_subclass__(cls):
        super().__init_subclass__()
        if cls.sides < 3:
            raise ValueError('Polygons need 3+ sides')

    @classmethod
    def interior_angles(cls):
        return (cls.sides - 2) * 180

class Hexagon(BetterPolygon):
    sides = 6

assert Hexagon.interior_angles() == 720
```

코드가 훨씬 짧아졌고, ValidatePolygon 메타클래스가 완전히 사라졌다. 그리고 `class['side']`를 통해 딕셔너리에서 sides를 가져올 필요가 없다. `__init_subclass__` 안에서는 cls 인스턴스에서 sides 애트리뷰트를 직접 가져올 수 있으므로 코드를 이해하기도 훨씬 쉬워졌다.


또한 표준 파이썬 메타클래스 방식의 또 다른 문제점은 클래스 정의마다 메타클래스를 단 하나만 지정할 수 있다는 것이다. `__init_subclass__` 특별 클래스 메서드를 사용하면 이 문제도 해결할 수 있다. super 내장 함수를 사용해 부모 형제자매 클래스의 `_init_subclass_`를 호출해주는 한 여러 단계로 이뤄진 `__init_subclass__`를 활용하는 클래스 계층 구조를 쉽게 정의할 수 있다. 

```python
# Example 12
class Filled:
    color = None  # Must be specified by subclasses

    def __init_subclass__(cls):
        super().__init_subclass__()
        if cls.color not in ('red', 'green', 'blue'):
            raise ValueError('Fills need a valid color')


# Example 13
class RedTriangle(Filled, BetterPolygon):
    color = 'red'
    sides = 3

ruddy = RedTriangle()
assert isinstance(ruddy, Filled)
assert isinstance(ruddy, BetterPolygon)

# Example 14
try:
    print('Before class')
    
    class BlueLine(Filled, BetterPolygon):
        color = 'blue'
        sides = 2
    
    print('After class')
except:
    logging.exception('Expected')
else:
    assert False
```

## [49. `__init_subclass__를 사용해 클래스 확장을 등록하라`](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_49.py)

메타클래스의 다른 용례로 프로그램이 자동으로 타입을 등록하는 것이 있다. 간단한 식별자를 이용해 그에 해당하는 클래스를 찾는 역검색을 하고 싶을 때 이런 등록 기능이 유용하다.

JSON으로 직렬화할 클래스가 아주 많더라도 JSON 문자열을 적당한 파이썬 object로 역직렬화 하는 함수는 공통으로 하나만 있는 것이 이상적이다. 이런 공통 함수를 만들고자 객체의 클래스 이름을 직렬화해 JSON 데이터에 포함시킬 수 있다.

```python
# Example 5
class BetterSerializable:
    def __init__(self, *args):
        self.args = args

    def serialize(self):
        return json.dumps({
            'class': self.__class__.__name__,
            'args': self.args,
        })

    def __repr__(self):
        name = self.__class__.__name__
        args_str = ', '.join(str(x) for x in self.args)
        return f'{name}({args_str})'
```

그러면 클래스 이름을 객체 생성자로 다시 연결해주는 매핑을 유지할 수 있다. 

```python
# Example 6
registry = {}

def register_class(target_class):
    registry[target_class.__name__] = target_class

def deserialize(data):
    params = json.loads(data)
    name = params['class']
    target_class = registry[name]
    return target_class(*params['args'])

# Example 7
class EvenBetterPoint2D(BetterSerializable):
    def __init__(self, x, y):
        super().__init__(x, y)
        self.x = x
        self.y = y

register_class(EvenBetterPoint2D)


# Example 8
before = EvenBetterPoint2D(5, 3)
print('Before:    ', before)
data = before.serialize()
print('Serialized:', data)
after = deserialize(data)
print('After:     ', after)
```

그러나 register_class를 호출하는 것을 잊어버리면 BetterSerializable이 제공하는 기능을 제대로 활용할 수 없다. 
프로그래머가 BetterSerializable을 사용한다는 의도를 감지하고 적절한 동작을 수행해 항상 제대로 register_class를 호출해줄 수 있다면 어떨까? 메타클래스는 하위 클래스가 정의될 때 class 문을 가로채서 이런 동작을 수행할 수 있다.

```python
# Example 11
class Meta(type):
    def __new__(meta, name, bases, class_dict):
        cls = type.__new__(meta, name, bases, class_dict)
        register_class(cls)
        return cls

class RegisteredSerializable(BetterSerializable,
                             metaclass=Meta):
    pass


# Example 12
class Vector3D(RegisteredSerializable):
    def __init__(self, x, y, z):
        super().__init__(x, y, z)
        self.x, self.y, self.z = x, y, z

before = Vector3D(10, -7, 3)
print('Before:    ', before)
data = before.serialize()
print('Serialized:', data)
print('After:     ', deserialize(data))
```

더 좋은 접근 방법은 `__init_subclass__` 특별 클래스 메서드를 사용하는 것이다. 

```python
# Example 13
class BetterRegisteredSerializable(BetterSerializable):
    def __init_subclass__(cls):
        super().__init_subclass__()
        register_class(cls)

class Vector1D(BetterRegisteredSerializable):
    def __init__(self, magnitude):
        super().__init__(magnitude)
        self.magnitude = magnitude

before = Vector1D(6)
print('Before:    ', before)
data = before.serialize()
print('Serialized:', data)
print('After:     ', deserialize(data))
```

## [50. `__set_name__`으로 클래스 애트리뷰트를 표시하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_50.py)

클래스가 정의될 때마다 파이선은 해당 클래스 안에 들어 있는 디스크립터 인스턴스의 `__set_name__`을 호출한다. `__set_name__`은 디스크립터 인스턴스를 소유 중인 클래스와 디스크립터 인스턴스가 대입될 애트리뷰트 이름을 인자로 받는다.

```python
# Example 11
class Field:
    def __init__(self):
        self.name = None
        self.internal_name = None

    def __set_name__(self, owner, name):
        # Called on class creation for each descriptor
        self.name = name
        self.internal_name = '_' + name

    def __get__(self, instance, instance_type):
        if instance is None:
            return self
        return getattr(instance, self.internal_name, '')

    def __set__(self, instance, value):
        setattr(instance, self.internal_name, value)


# Example 12
class FixedCustomer:
    first_name = Field()
    last_name = Field()
    prefix = Field()
    suffix = Field()

cust = FixedCustomer()
print(f'Before: {cust.first_name!r} {cust.__dict__}')
cust.first_name = 'Mersenne'
print(f'After:  {cust.first_name!r} {cust.__dict__}')
```

## [51. 합성 가능한 클래스 확장이 필요하면 메타클래스보다는 클래스 데코레이터를 사용하라](https://github.com/yongjunleeme/effectivepython/blob/master/example_code/item_51.py)

메타클래스를 사용하면 클래스 생성을 다양한 방법으로 커스텀화할 수 있지만 여전히 메타클래스로 처리할 수 없는 경우가 있다. 예를 들어 어떤 클래스의 모든 메서드를 감싸서 메서드에 전달되는 인자 반환 값, 발생한 예외를 모두 출력하고 싶다고 하자. 다음 코드는 이런 디버깅 데코레이터를 정의한다.


```python
from functools import wraps

def trace_func(func):
    if hasattr(func, 'tracing'):  # Only decorate once
        return func

    @wraps(func)
    def wrapper(*args, **kwargs):
        result = None
        try:
            result = func(*args, **kwargs)
            return result
        except Exception as e:
            result = e
            raise
        finally:
            print(f'{func.__name__}({args!r}, {kwargs!r}) -> '
                  f'{result!r}')

    wrapper.tracing = True
    return wrapper


# Example 2
class TraceDict(dict):
    @trace_func
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

    @trace_func
    def __setitem__(self, *args, **kwargs):
        return super().__setitem__(*args, **kwargs)

    @trace_func
    def __getitem__(self, *args, **kwargs):
        return super().__getitem__(*args, **kwargs)


# Example 3
trace_dict = TraceDict([('hi', 1)])
trace_dict['there'] = 2
trace_dict['hi']
try:
    trace_dict['does not exist']
except KeyError:
    pass  # Expected
else:
    assert False
```

클래스 데코레이터. 클래스 데코레이터는 함수 데코레이터처럼 사용할 수 있다. 즉, 클래스 선언 앞에 @ 기호와 데코레이터 함수를 적으면 된다. 이때 데코레이터 함수는 인자로 받은 클래스를 적절히 변경해서 재생성해야 한다.

```python
# Example 8
def my_class_decorator(klass):
    klass.extra_param = 'hello'
    return klass

@my_class_decorator
class MyClass:
    pass

print(MyClass)
print(MyClass.extra_param)
```


```python
import types

trace_types = (
    types.MethodType,
    types.FunctionType,
    types.BuiltinFunctionType,
    types.BuiltinMethodType,
    types.MethodDescriptorType,
    types.ClassMethodDescriptorType)


# Example 9
def trace(klass):
    for key in dir(klass):
        value = getattr(klass, key)
        if isinstance(value, trace_types):
            wrapped = trace_func(value)
            setattr(klass, key, wrapped)
    return klass

# Example 10
@trace
class TraceDict(dict):
    pass

trace_dict = TraceDict([('hi', 1)])
trace_dict['there'] = 2
trace_dict['hi']
try:
    trace_dict['does not exist']
except KeyError:
    pass  # Expected
else:
    assert False


# Example 11
class OtherMeta(type):
    pass

@trace
class TraceDict(dict, metaclass=OtherMeta):
    pass

trace_dict = TraceDict([('hi', 1)])
trace_dict['there'] = 2
trace_dict['hi']
try:
    trace_dict['does not exist']
except KeyError:
    pass  # Expected
else:
    assert False
```

## Link

- [effective python](https://github.com/yongjunleeme/effectivepython/tree/master/example_code)

