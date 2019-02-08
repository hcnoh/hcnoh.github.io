---
layout: post
use_math: true
title: "[Python] 클래스 네임스페이스 개념 정리"
date: 2019-01-30 19:07:51
tagline: "파이썬의 클래스 내부의 지역 네임스페이스 및 외부 지역 네임스페이스에서의 클래스 이름에 대한 개념 정리"
categories:
- Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-30-python-class-namespace
---

지난번 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에 이어서 이번에는 파이썬의 클래스 네임스페이스에 대한 개념에 대해서 정리해보았다. 이번 포스팅은 다음의 링크를 참고하여 작성하였다:
- [링크1](https://wikidocs.net/1743)

## 파이썬의 네임스페이스 확인
파이썬에서 현재 스코프의 네임스페이스를 확인하는 방법으로는 `locals` 메서드를 사용하는 것을 이전 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에서 설명했던 것 같다. 한편 `locals` 메서드는 딕셔너리 형태의 네임스페이스를 복사해서 반환하는 메서드인데 여기서 네임스페이스의 Key들인 이름들만을 반환하는 `dir` 메서드가 있다. 이전 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에서 사용했던 예제들을 이용하여 `dir`을 사용하는 예제를 살펴보도록 하자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
    
    inner_func()

a = 10
outer_func()
print(locals())
print(dir())

>>>
{'__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7fa44f28bc18>, '__spec__': None, 'outer_func': <function outer_func at 0x7fa44f2c2f28>, '__name__': '__main__', '__package__': None, '__file__': 'namespace_example01.py', '__cached__': None, '__builtins__': <module 'builtins' (built-in)>, 'a': 10, '__doc__': None}                 
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'a', 'outer_func']
```

`locals` 메서드는 네임스페이스의 딕셔너리를 전부 반환하며 Key는 이름, Value는 객체로 구성되어 있는 것을 확인할 수 있다. 또한 `dir` 메서드는 네임스페이스의 이름들을 요소로 가진 리스트를 복사해서 반환하게 되는 것을 확인할 수 있다. 객체에 직접 접근한다던가 하는 목적이 있지 않은 이상 네임스페이스의 이름들만 확인하는 `dir` 메서드를 활용하는 것이 간편할 수 있다.

## 파이썬 클래스의 네임스페이스
파이썬의 함수와 마찬가지로 파이썬의 클래스도 선언을 하는 순간 객체를 생성하고 네임스페이스에 입력이 된다. 아래의 예제를 살펴보자.

```python
class ClassExample:
    a = 10


print(dir())

>>>
['ClassExample', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__']
```

기본적인 네임스페이스의 요소들을 제외하고 `ClassExample`라는 이름이 추가된 것을 확인할 수 있다. 클래스를 선언만 하여도 네임스페이스에 클래스의 이름이 새롭게 등록된다. 아래와 같이 예제를 조금 수정해보자.

```python
class ClassExample(object):
    a = 10
    print(dir())    # 현재 스코프: 클래스 내부의 네임스페이스


class_instance = ClassExample()
print(dir())

>>>
['__module__', '__qualname__', 'a']
['ClassExample', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'class_instance']
```

파이썬의 클래스는 모듈과 마찬가지로 각각의 네임스페이스를 갖는다. 클래스를 정의하는 동안에 `dir` 메서드는 현재 스코프인 클래스 내부의 네임스페이스 이름 리스트를 반환할 것이고 그에따라 클래스 내부의 지역 변수를 포함한 네임스페이스의 이름 리스트를 확인할 수 있을 것이다. 또한 추가적으로 모듈의 네임스페이스에 클래스의 인스턴스인 `class_instance`도 추가되어 있는 것도 확인해볼 수 있다.

## 클래스 네임스페이스 반환: 내장변수 \_\_dict\_\_ 활용
파이썬의 클래스는 각각의 네임스페이스를 갖는다. 또한 `dir` 메서드를 통해서 현재 네임스페이스의 이름 리스트를 반환받을 수 있다고 언급하였다. 만약 이름 리스트가 아닌 실제 네임스페이스의 사본을 반환받고 싶은 경우에는 파이썬 내장변수 `__dict__`를 사용하면 된다. 하지만 이 경우에 주의해야 할 사항들이 몇 가지 있다. 아래의 예제를 살펴보자.

```python
class ClassExample(object):
    a = 10
    b = 20


class_instance = ClassExample()

print(class_instance.__dict__)
print(ClassExample.__dict__)

>>>
{}
{'a': 10, '__dict__': <attribute '__dict__' of 'ClassExample' objects>, 'b': 20, '__doc__': None, '__module__': '__main__', '__weakref__': <attribute '__weakref__' of 'ClassExample' objects>}
```

내장변수 `__dict__`의 용법은 위와 같다. 클래스의 인스턴스에도 사용할 수 있고(`class_instance.__dict__`) 클래스 자체에서도 사용할 수(`ClassExample.__dict__`) 있다. 예상한 대로 인스턴스의 네임스페이스는 비어있으며 클래스 자체의 네임스페이스에 `a`와 `b`가 모두 정의되어 있는 것을 확인할 수 있다. 이것에 대한 확신을 위해서 예제를 아래처럼 조금 더 수정해보자.

```python
class ClassExample(object):
    a = 10
    b = 20
    
    def __init__(self):
        self.c = 30


class_instance = ClassExample()

print(class_instance.__dict__)
print(ClassExample.__dict__)

>>>
{'c': 30}
{'__module__': '__main__', '__dict__': <attribute '__dict__' of 'ClassExample' objects>, '__weakref__': <attribute '__weakref__' of 'ClassExample' objects>, 'b': 20, '__init__': <function ClassExample.__init__ at 0x7f1cae105840>, '__doc__': None, 'a': 10}
```

예상한 대로 인스턴스의 네임스페이스에 인스턴스 변수인 `c`가 추가되어 있는 것을 확인할 수 있다. 위의 예제들을 통해서 우리는 클래스의 네임스페이스는 클래스 자체의 네임스페이스 뿐 아니라 각각 인스턴스 별로 따로 네임스페이스를 관리하고 있을 것이라고 유추해볼 수 있다. 이것을 확인해보기 위해서 다음의 예제를 살펴보자.

```python
class ClassExample(object):
    a = 10
    b = 20
    
    def __init__(self):
        self.c = 30
    
    def some_func(self):
        self.d = 40


class_instance0 = ClassExample()
class_instance1 = ClassExample()

class_instance1.some_func()

print(class_instance0.__dict__)
print(class_instance1.__dict__)

>>>
{'c': 30}  
{'c': 30, 'd': 40}
```

두 인스턴스의 네임스페이스가 다른 것을 확인할 수 있다. 두 번째 인스턴스인 `class_instance1`은 `some_func` 메서드를 통해서 인스턴스 변수 `d`를 추가했기 때문에 인스턴스의 네임스페이스에 `d`가 추가되어 있을 것이다.

## 클래스의 스코프
