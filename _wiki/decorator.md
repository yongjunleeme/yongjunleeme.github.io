---
layout  : wiki
title   : decorator
summary : 
date    : 2020-02-08 13:25:49 +0900
updated : 2020-02-20 18:36:35 +0900
tags    : 
toc     : true
public  : true
parent  : python
latex   : false
---
* TOC
{:toc}

## Notice
당최 데코레이터가 무엇인지 모르겠어서 [The Power of Python Decorator](chrome-extension://cbnaodkpfinfiipjblikofhlhlcickei/src/pdfviewer/web/viewer.html?file=https://static.realpython.com/guides/the-power-of-python-decorators.pdf?__s=wbgek1ohelz1ifi6f59y)
와[The Power of Decorator]([https://realpython.com/primer-on-python-decorators/](https://realpython.com/primer-on-python-decorators/))를 읽으며 일부 적었는데 제 생각도 같이 적었습니다;
하지만 아직도 잘 모르겠습니다.
데코레이터 좀 알려주세요..


## The Power of Decorator

```python
def greet():
	return 'hello!'
   
greet = null_decorator(greet)

>>> greet()
'Hello!'
```

(greet 입장에서는 네가 먼저 실행하되 나를 넣어서 실행해줘~?)

null_decorator함수를 실행하면서 greet함수를 정의하고 데코레이트한 것.
일부러 유용하지 않은 함수를 보여준 것이고 
아래 함수는 유용할텐데 파이썬의 @문법이 매우 편하다네.
```
@null_decorator
def greet():
	return 'Hello!'
    
>>> greet()
'Hello!'
```

```
def uppercase(func):
	def wrapper():
    	original_result = func()
        modiried_result = original_result.upper()
        return modified_result
    return wrapper
```
(왜 wrapper로 싸는 것이여? 단순히 보안 때문이여 뭐여?)

tack이라네. 재사용할 블록을 빌딩한다. 로깅과 같은 도구를 쓸 때 유용.

```
def strong(func):
	def wrapper():
    	return '<strong>' + func() + '</strong>'
    return wrapper
    
    
def emphasis(func)
	def wrapper():
    	return '<em>' + func() + '</em>'
    return wrapper
```

```
@strong
@emphasis
def greet():
	return 'Hello!'
```

어떤 순서대로 실행이 될까?

```
>>> greet()
'<strong><em>Hello!</em></strong>'
```

from bottom to top!
why? 첫째로 인풋 함수는 @emhasis 데코레이터에 쌓여 있기 때문이고 그 결과가 다시 @strong 데코레이터에 쌓여 있기 때문이다.
쉽게 기억하려고 이를 decorator stacking이라 부른다. 


```python
decorated_greet = strong(emphasis(greet))
```

사실 지금까지 본 예는 whatsoever 인자도 없는 단순한 것들이었다. arbitrary 인자들을 취하려면 어떻게 데코레이트를 해야 할까? 어떤 인자도 받을 수 있는 proxy decorator다.
```python
def proxy(func):
	def wrapper(*args, **kwargs):
    	return func(*args, **kwargs)
    return wrapper
```


유용한 proxy decorator로 기술에 laid out한 김에 더 확장을 해보자.
아래는 trace decorator다.
```python
def trace(func):
	def waapper(*args, **kwargs):
    	print(f'TRACE: calling {func.__name__}() '
        	  f'with {args}, {kwargs}')
        
        original_result = func(*args, **kwargs)
        
        print(f'TRACE: {func.__name__}() '
        	  f'returned {original_result!r}')
        
        return original_result
    return wrapper
```

```python
@trace
def say(name, line):
	return f'{name}: {line}'

>>> say('Jane', 'Hello World')
'TRACE: calling say() with ("Jane", "Hello, World"), {}'
'TRACE: say() returned "Jane: Hello, World"'
'Jane: Hello, World'
```

데코레이터를 dubuggable하게 만드는 방법

오리지널 함수 이름, docstring, 파라미터 리스트는 래퍼 클로저에 의해 숨겨져 있다.
함수 메타데이터에 접근하려면 래퍼 클로저의 메타데이터를 봐야 한다.

```python
>>> greet.__name__
'greet'
>>> greet.__doc__
'Return a friendly greeting.'

>>> decorated_greet.__name__
'wrapper'
>>> decorated_greet.__doc__
None
```

파이썬 표준 라이브러리를 써서 빠르게 고칠 수 있다.

```python
import functools

def uppercase(func):
	@functools.wraps(func)
    def wrapper():
    	return func().upper()
    return wrapper
```

```python
@uppercase
def greet()
	"""Return a friendly greeting."""
    return "Hello!"

>>> greet.__name__
'greet'
>>> greet.__doc__
'Return a friendly greeting.'
```

## Key Takeaways
- Decorators define reusable building blocks you can apply to acallable to modify its behavior without permanently modifyingthe callable itself.

- The Power of Decorators• The@syntax is just a shorthand for calling the decorator onan input function. Multiple decorators on a single function areapplied bottom to top (decorator stacking).•  

- As a debugging best practice, use thefunctools.wrapshelperin your own decorators to carry over metadata from the undec-orated callable to the decorated one.

- Just like any other tool in the software development toolbox,decorators are not a cure-all and they should not be overused.It’s important to balance the need to “get stuff done” with thegoal of “not getting tangled up in a horrible, unmaintainablemess of a code base.”85


여기부터는 [리얼 파이썬 데코레이터]([https://realpython.com/primer-on-python-decorators/](https://realpython.com/primer-on-python-decorators/))의 내용

## Functions
```python
    def say_hello(name):
        return f"Hello {name}"
    
    def be_awesome(name):
        return f"Yo {name}, together we are the awesomest!"
    
    def greet_bob(greeter_func):
        return greeter_func("Bob")
```

```python
    >>> greet_bob(say_hello)
    'Hello Bob'
    
    >>> greet_bob(be_awesome)
    'Yo Bob, together we are the awesomest!'
```

(greet_bob왈: 나는 너(함수)를 인자로 받아서 함수로 리턴할게, 그런데 리턴할 때 인자는 내 걸로 써)
(맞나 모르겠다..)

```python
    def parent(num):
        def first_child():
            return "Hi, I am Emma"
    
        def second_child():
            return "Call me Liam"
    
        if num == 1:
            return first_child
        else:
            return second_child
``` 

```python
    >>> first = parent(1)
    >>> second = parent(2)
    
    >>> first
    <function parent.<locals>.first_child at 0x7f599f1e2e18>
    
    >>> second
    <function parent.<locals>.second_child at 0x7f599dad5268>
``` 
(함수를 대입한 변수로 변수가 다른 함수의 내부 함수를 호출하는 것인가?)

- 괄호 없이 사용되는 함수(first_child)
- 함수에 관한 참조를 리턴하는 것?

The somewhat cryptic output simply means that the first variable refers to the local first_child() function inside of parent(), while second points to second_child().

(왜....?)

```python
    >>> first()
    'Hi, I am Emma'
    
    >>> second()
    'Call me Liam'
```
- first와 second를 일반 함수처럼 사용

## Simple Decorators

```python
    def my_decorator(func):
        def wrapper():
            print("Something is happening before the function is called.")
            func()
            print("Something is happening after the function is called.")
        return wrapper
    
    def say_whee():
        print("Whee!")
    
    say_whee = my_decorator(say_whee)
```
( 1번 함수의 인자에 2번 함수를 대입하고 2번 함수에 1번 함수를 할당한다? )
( 그러고 나서 1번 함수의 인자를 내부 함수에서 함수로 호출을 한다? )

```python
    >>> say_whee()
    Something is happening before the function is called.
    Whee!
    Something is happening after the function is called.

    say_whee = my_decorator(say_whee)
```
so-called(이른바) 이게 데코레이터라네?

wrapper()가 오리지널 say_whee()를 참조

```python
    def my_decorator(func):
        def wrapper():
            print("Something is happening before the function is called.")
            func()
            print("Something is happening after the function is called.")
        return wrapper
    
    @my_decorator
    def say_whee():
        print("Whee!")
```
왜 인자로 받은 함수는 그 내부에서 호출하는 것임?  
왜 내부함수는 리턴을 하는 것인지?

- [파이문법]([https://www.python.org/dev/peps/pep-0318/#background](https://www.python.org/dev/peps/pep-0318/#background))의 데코레이터
- @my_decorator는 say_whee = my_decorator(say_whee)

## Reusing Decorators
```python
    def do_twice(func):
        def wrapper_do_twice():
            func()
            func()
        return wrapper_do_twice
```

위와 같은 데코레이터를 다른 파일에서 사용 가능

```python
    from decorators import do_twice
    
    @do_twice
    def say_whee():
        print("Whee!")

    >>> say_whee()
    Whee!
    Whee!
```

위는 되는데 아래는 안 되는 모양

```python
    from decorators import do_twice
    
    @do_twice
    def greet(name):
        print(f"Hello {name}")

    >>> greet("World")
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    TypeError: wrapper_do_twice() takes 0 positional arguments but 1 was given
```

wrapper_do_twice() 함수가 인자를 받지 않아서 에러가 나는 것 같은데
name="World"는 된다.
wrapper_do_twice()에 인자를 만들어주면 고칠 수 있다네.
하지만 그러면 또 say_whee()를 쓰면 에러가 난다.

*args and **kwargs를 써서 해결

```python
    def do_twice(func):
        def wrapper_do_twice(*args, **kwargs):
            func(*args, **kwargs)
            func(*args, **kwargs)
        return wrapper_do_twice
```
## Returning Values From Decorated Functions
```
    from decorators import do_twice
    
    @do_twice
    def return_greeting(name):
        print("Creating greeting")
        return f"Hi {name}"
```

```python
    >>> hi_adam = return_greeting("Adam")
    Creating greeting
    Creating greeting
    >>> print(hi_adam)
    None
```
Oops, your decorator ate the return value from the function.

Because the `do_twice_wrapper()` doesn’t explicitly return a value, the call `return_greeting("Adam")` ended up returning `None`.

( Wrapper 함수가 데코레이션 함수의 값을 리턴하는 것을 확실히 알아야 한다고? )
아래와 같이 바꿔야 한다.

```python
    def do_twice(func):
        def wrapper_do_twice(*args, **kwargs):
            func(*args, **kwargs)
            return func(*args, **kwargs)  # 여기를 바꾼 듯
        return wrapper_do_twice
```

```python
    >>> return_greeting("Adam")
    Creating greeting
    Creating greeting
    'Hi Adam'
```

## Links
[Real Python](chrome-extension://cbnaodkpfinfiipjblikofhlhlcickei/src/pdfviewer/web/viewer.html?file=https://static.realpython.com/guides/the-power-of-python-decorators.pdf?__s=wbgek1ohelz1ifi6f59y)
[Real Python]([https://realpython.com/primer-on-python-decorators/](https://realpython.com/primer-on-python-decorators/))



