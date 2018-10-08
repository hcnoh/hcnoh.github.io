---
layout: post
title: "WAY 27. 공개 속성보다는 비공개 속성을 사용하자"
date: 2018-10-08 18:36:28
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-08-effective-python-way27
---

## 파이썬의 클래스 속성
- 클래스 속성의 가시성(`visibility`):
  - 공개(`public`)
    - 어디서든 객체에 점 연산자(`.`)를 사용하여 접근할 수 있음
  - 비공개(`private`)
    - 속성 이름 앞에 밑줄(`__`) 두 개를 붙여 지정
    - 같은 클래스에 속한 메서드에서는 비공개 필드에 직접 접근 가능
    - 하지만 클래스 외부에서 직접 비공개 필드에 접근하면 예외가 일어남

```python
class MyObject(object):
    def __init__(self):
        self.public_field = 5
        self.__private_field = 10
    
    def get_private_field(self):
        return self.__private_field

foo = MyObject()
assert foo.public_field == 5            # 직접 접근 가능
assert foo.get_private_field() == 10    # 클래스에 속한 메서드로 접근

foo.__private_field                     # 직접 접근하려고 하면 예외 발생

>>>
AttributeError: "MyObject" object has no attribute "__private_field"
```

- 클래스 메서드도 같은 `class` 블록에 선언되어 있음:
  - 비공개 속성에 접근 가능

```python
class MyOtherObject(object):
    def __init__(self):
        self.__private_field = 71
    
    @classmethod
    def get_private_field_of_instance(cls, instance):
        return instance.__private_field

bar = MyOtherObject()
assert MyOtherObject.get_private_field_of_instance(bar) == 71
```

- 서브클래스에서는 부모 클래스의 비공개 필드에 접근할 수 없음

```python
class MyParentObject(object):
    def __init__(self):
        self.__private_field = 71
        
class MyChildObject(MyParentObject):
    def get_private_field(self):
        return self.__private_field

baz = MyChildObject()
baz.get_private_field()

>>>
AttributeError: "MyChildObject" object has no attribute "_MyChildObject__private_field"
```

## 파이썬의 비공개 속성 구현 방식
- 비공개 속성은 간단하게 속성 이름을 변환하는 방식으로 구현
  1. 파이썬 컴파일러(`이 표현이 맞는지?`)가 `MyChildObject.get_private_field` 같은 메서드에서 비공개 속성에 접근하는 코드를 발견
  2. 코드를 변경: `__private_field` => `_MyChildObject__private_field`
- 즉, 위의 예제에서는 `__private_field`의 실제 이름은 `_MyParent)bject__private_field`가 됨
- 따라서 자식 클래스에서 부모의 비공개 속성에 접근하는 동작은 단순히 변환된 속성 이름이 일치하지 않아서 실패
- 아래와 같은 방식을 사용하면 어떤 클래스의 비공개 속성이든 쉽게 접근 가능

```python
assert baz._MyParentObject__private_field == 71
```

- 객체의 속성 딕셔너리를 들여다보면 실제로 비공개 속성이 변환 후의 이름으로 저장되어 있음을 확인 가능

```python
print(baz.__dict__)

>>>
{"_MyparentObject__private_field": 71}
```

- 파이썬에서 비공개 속성용 문법이 가시성을 엄격하게 강제하지 않는 이유?
  - 파이썬에서 자주 인용되는 좌우명: "우리 모두 성인이라는 사실에 동의합니다"
    - 개방으로 얻는 장점이 폐쇄로 얻는 단점보다 크다는 믿음 반영

## 비공개 속성의 효용성
- 파이썬에서는 특정 기능들("WAY 32. 지연 속성에는 `__getattr__`, `__getattribute__`, `__setattr__`을 사용하자" 참고)이 있으면 어ㅗㄴ제든 객체의 내부를 조작할 수 있음
  - 비공개 속성에 접근하는 것을 막는 것이 무슨 가치가 있음?
- 무분별하게 객체의 내부에 접근하는 위험을 최소화하기 위해 스타일 가이드에 정의된 명명 관례를 따름
  - `_protected_field` 처럼 앞에 밑줄 한 개를 붙인 필드는 보호(`protected`) 필드로 취급 => 신중히 다뤄야 함을 의미
- 하지만 파이썬을 처음 접하는 많은 프로그래머가 서브클래스나 외부에서 접근하면 안 되는 내부 API를 비공개 필드로 나타냄

```python
class MyClass(object):
    def __init__(self, value):
        self.__value = value
    
    def get_value(self):
        return str(self.__value)

foo = MyClass(5)
assert foo.get_value() == "5"
```

- 위의 방식은 잘못됨:
  - 누군가 클래스에 새 동작을 추가하거나 기존 메서드의 결함을 해결하기 위하여 서브클래스를 만들 것임
  - 비공개 속성을 선택하면 서브클래스의 오버라이드(`override`)와 확장(`extension`)을 다루기 어렵고 불안정
  
```python
class MyIntegerSubclass(MyClass):
    def get_value(self):
        return int(self._MyClass__value)    # 꼭 필요하다면 여전히 비공개 필드에 접근 가능

foo = MyIntegerSubclass(5)
assert foo.get_value() == 5
```

- 나중에 클래스의 계층이 변경되면 `MyIntegerSubclass` 같은 클래스는 비공개 참조가 더는 유효하지 않게 되어 제대로 동작하지 않음
  - `MyIntegerSubclass` 클래스의 직계 부모인 `MyClass`에 `MyBaseClass`라는 또다른 부모 클래스를 추가

```python
class MyBaseClass(object):
    def __init__(self, value):
        self.__value = value    # __value가 속한 곳이 새로운 부모클래스로 변경
    # ...
    
class MyClass(MyBaseClass):
    # ...

class MyIntegerSubclass(MyClass):
    def get_value(self):
        return int(self._MyClass__value)

foo = MyIntegerSubclass(5)
foo.get_value()                 # 더 이상 동작하지 않음

>>>
AttributeError: "MyIntegerSubclass" object has no attribute "_MyClass_value"
```

- 일반적으로 보호 속성을 사용하여 서브클래스가 더 많은 일을 할 수 있게 하는 편이 나음
  - 각각의 보호 필드를 문서화 => 서브클래스에서 내부 API 중 어느 것을 쓸 수 있고 어느 것을 그대로 둬야 하는지 설명

```python
class MyClass(object):
    def __init__(self, value):
        # 사용자가 객체에 전달한 값을 저장한다.
        # 문자열로 강제할 수 있는 값이어야 하며,
        # 객체에 할당하고 나면 불변으로 취급해야 한다.
        self._value = value

```

- 비공개 속성을 사용할지 진지하게 고민할 시점: 서브클래스와 이름이 충돌할 염려

```python
class ApiClass(object):
    def __init__(self):
        self._value = 5
    
    def get(self):
        return self._value
        
class Child(ApiClass):
    def __init__(self):
        super().__init__()
        self._value = "hello"   # 자식 클래스가 부모 클래스에서 이미 정의한 속성을 정의할 때 충돌 발생

a = Child()
print(a.get(), "and", a._value, "should be different")

>>>
hello and hello should be different
```

- 이런 충돌은 속성 이름이 `value`처럼 아주 일반적일 때 일어날 확률이 높음:
  - 부모 클래스에서 비공개 속성을 사용하여 자식 클래스와 속성 이름이 겹치지 않도록 함

```python
class ApiClass(object):
    def __init__(self):
        self.__value = 5
    
    def get(self):
        return self.__value

class Child(ApiClass):
    def __init__(self):
        super().__init__()
        self._value = "hello"   # OK

a = child()
print(a.get(), "and", a._value, "are different")

>>>
5 and hello are different
```
