---
layout: post
title: "WAY 32. 지연 속성에는 __getattr__, __getattribute__, setattr__을 사용하자"
date: 2018-10-17 13:08:31
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-17-effective-python-way32
---

## `__getattr__` 메서드
- 파이썬이 동적 동작 기능 제공이 가능하게끔 하는 메서드
>- 클래스에 `__getattr__` 메서드를 정의하면 객체의 인스턴스 딕셔너리에서 속성을 찾을 수 없을 때마다 이 메서드가 호출

```python
class LazyDB(object):
    def __init__(self):
        self.exists = 5
        
    def __getattr__(self, name):
        value = "Value for %s" % name
        setattr(self, name, value)
        return value
```

- 존재하지 않는 속성인 `foo`에 접근하는 경우:
    - 파이썬이 `__getattr__` 메서드를 호출 => 인스턴스 딕셔너리 `__dict__`를 변경
    
```python
data = LazyDB()
print("Before:", data.__dict__)
print("foo:", data.foo)
print("After:", data.__dict__)

>>>
Before: {"exists": 5}
foo: Value for foo
After: {"exists": 5, "foo": "Value for foo"}
```

- `__getattr__`이 실제 호출되는 시점을 보여주기 위하여 `LazyDB`에 로깅을 추가
    - 또한 무한 반복을 피하기 위하여 `super().__getattr__()`로 실제 프로퍼티 값을 얻어오는 부분을 추가

```python
class LoggingLazyDB(LazyDB):
    def __getattr__(self, name):
        print("Called __getattr__(%s)" % name)
        return super().__getattr__(name)

data = LoggingLazyDB()
print("exists:", data.exists)
print("foo:", data.foo)
print("foo:", data.foo)

>>>
exists: 5
Called __getattr__(foo)
foo: Value for foo
foo: Value for foo
```

- `exists` 속성은 원래 인스턴스 딕셔너리에 존재:
    - `__getattr__`이 절대 호출되지 않음
- `foo` 속성은 원래 인스턴스 딕셔너리에 존재하지 않음:
    - 처음에는 `__getattr__`이 호출
    - `foo`에 대응하는 `__getattr__` 호출은 `__setattr__`을 호출 => 인스턴스 딕셔너리에 `foo`를 저장
    - 따라서 두 번째로 접근하는 경우에는 `__getattr__`이 호출되지 않음

## 스키마리스 데이터
- 스키마리스 데이터(`schemaless data`): 구조가 정해지지 않은 데이터
- 스키마리스 데이터에 지연 접근하는 경우 위의 동작이 특히 도움이 됨
    - `__getattr__`이 프로퍼티 로딩이라는 어려운 작업을 한 번만 실행하면 다음 접근부터는 기존 결과를 가져옴

## `__getattribute__` 메서드
- 데이터베이스 시스템에서의 트랜잭션
    - 사용자가 다음 번에 속성에 접근할 때는 대응하는 데이터베이스의 로우가 여전히 유효한지, 트랜잭션이 여전히 열려 있는지 알고 싶은 경우
    - `__getattr__` 후크는 기존 속성에 빠르게 접근하기 위하여 객체의 인스턴스 딕셔너리를 사용 => 이 작업에 믿고 쓸 수 없음
    - 파이썬에서 이러한 쓰임새를 고려하여 `__getattribute__`라는 또 다른 후크를 제공

>- `__getattribute__`:
>    - 객체의 속성에 접근할 때마다 호출: 해당 속성이 속성 딕셔너리에 있건 없건 호출
>     이러한 특징을 이용하여 속성에 접근할 때마다 전역 트랜잭션 상태를 확인하는 작업 등에 사용 가능

- 여기서는 `__getattribute__`가 호출될 때마다 로그를 남기려고 `validatingDB`를 정의

```python
class ValidatingDB(object):
    def __init__(self):
        self.exists = 5
        
    def __getattribute__(self, name):
        print("Called __getattribute__(%s)" % name)
        try:
            return super().__getattribute__(name)
        except AttributeError:
            value = "Value for %s" % name
            setattr(self, name, value)
            return value

data = ValidatingDB()
print("exists:", data.exists)
print("foo:", data.foo)
print("foo:", data.foo)

>>>
Called __getattribute__(exists)
exists: 5
Called __getattribute__(foo)
foo: Value for foo
Called __getattribute__(foo)
foo: Value for foo
```

- 동적으로 접근한 프로퍼티가 존재하지 않아야 하는 경우:
    - `AttributeError`를 일으켜 `__getattr__`, `__getattribute__`에 속성이 없는 경우의 파이썬 표준 동작이 일어나게 함
    
```python
class MissingPropertyDB(object):
    def __getattr__(self, name):
        if name == "bad_name":
            raise AttributeError("%s is missing" % name)
        # ...

data = MissingPropertyDB()
data.bad_name

>>>
AttributeError: bad_name is missing
```

- 파이썬 코드로 범용적인 기능 구현:
    - 내장 함수 `hasattr`로 프로퍼티가 있는지 확인 => 내장 함수 `getattr`로 프로퍼티 값을 가져옴

```python
data = LoggingLazyDB()
print("Before:", data.__dict__)
print("foo exists:", hasattr(data, "foo"))
print("After:", data.__dict__)
print("foo exists:", hasattr(data, "foo"))

>>>
Before: {"exists": 5}
Called __getattr__(foo)
foo exists: True
After: {"exists": 5, "foo": "Value for foo"}
foo exists: True
```

- 위의 예제에서 `__getattr__`은 한 번만 호출
- 반면 `__getattribute__`를 구현한 클래스인 경우:
    - `hasattr`이나 `getattr`을 호출할 때마다 `__getattribute__`가 실행됨
    
```python
data = ValidatingDB()
print("foo exists:", hasattr(data, "foo"))
print("foo exists:", hasattr(data, "foo"))

>>>
Called __getattribute__(foo)
foo exists: True
Called __getattribute__(foo)
foo exists: True
```

## `__setattr__` 메서드
- 객체에 값을 할당할 때 지연 방식으로 데이터를 데이터베이스에 집어넣고 싶은 경우: `__setattr__` 언어 후크로 가능

>- `__setattr__`:
>    - 인스턴스의 속성이 할당을 받을 때마다 직접 혹은 내장 함수 `setattr`을 통해 호출

```python
class SavingDB(object):
    def __setattr__(self, name, value):
        # 몇몇 데이터를 DB 로그로 저장함
        # ...
        super().__setattr__(name, value)
```

- `SavingDB`의 로깅용 서브클래스 정의

```python
class LoggingSavingDB(SavingDB):
    def __setattr__(self, name, value):
        print("Called __setattr__(%s, %r)" % (name, value))
        super().__setattr__(name, value)

data = LoggingSavingDB()
print("Before:", data.__dict__)
data.foo = 5
print("After:", data.__dict__)
data.foo = 7
print("Finally:", data.__dict__)

>>>
Before: {}
Called __setattr__(foo, 5)
After: {"foo": 5}
Called __setattr__(foo, 7)
Finally: {"foo": 7}
```

## `__getattribute__`/`__setattr__`의 문제점
- 객체의 속성에 접근할 때마다 호출된다는 점
    - 문제는 원하지 않는 경우에도 호출됨
- 문제가 되는 예시: 객체의 속성에 접근하면 실제로 연관 딕셔너리에서 키를 찾게 하고 싶은 경우

```python
class BrokenDictionaryDB(object):
    def __init__(self, data):
        self._data = {}
    
    def __getattribute__(self, name):
        print("Called __getattribute__(%s)" % name)
        return self._data[name]
```

- 위와 같이 구현하면 파이썬이 스택의 한계에 도달할 때까지 재귀 호출을 하게 됨

```python
data = BrokenDictionaryDB({"foo": 3})
data.foo

>>>
Called __getattribute__(foo)
Called __getattribute__(_data)
Called __getattribute__(_data)
...
Traceback ...
RuntimeError: maximum recursion depth exceeded
```

- `__getattribute__`가 `self._data`에 접근 => `__getattribute__`가 다시 실행 => 다시 `self._data`에 접근 => ...

>- 해결책?
>    - 인스턴스에서 `super().__getattribute__` 메서드로 인스턴스 속성 딕셔너리에서 값을 얻어오는 것
    
```python
class DictionaryDB(object):
    def __init__(self, data):
        self._data = data
    
    def __getattribute__(self, name):
        data_dict = super().__getattribute__("_data")
        return data_dict[name]
```

- 마찬가지 이유로 `__setattr__` 메서드에서도 `super().__setattr__`을 사용해야 함
