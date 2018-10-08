---
layout: post
title: "WAY 27. 공개 속성보다는 비공개 속성을 사용하자"
date: 2018-10-08 18:36:28
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
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
```
