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
