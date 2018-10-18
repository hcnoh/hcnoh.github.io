---
layout: post
title: "WAY 33. 메타클래스로 서브클래스를 검증하자"
date: 2018-10-18 13:38:15
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-18-effective-python-way33
---

## 메타클래스의 활용
- 클래스를 올바르게 정의했는지 검증
  - 스타일 강제, 메서드 오버라이드 강제, 클래스 속성 사이에 철저한 관계 정의 등

## 메타클래스의 동작 방식
- 메타클래스가 표준 객체에는 어떻게 동작하는지 이해할 필요가 있음
  - `type`을 상속하여 정의
  - 자체의 `__new__` 메서드에서 연관된 `class` 문의 콘텐츠를 받음

```python
class Meta(type):
    def __new__(meta, name, bases, class_dict):
        print((meta, name, bases, class_dict))
        return type.__new__(meta, name, bases, class_dict)

class MyClass(object, metaclass=Meta):
    stuff = 123
    
    def foo(self):
        pass
```

- 메타클래스가 접근 가능한 것들:
  - 클래스의 이름
  - 클래스가 상속하는 부모 클래스
  - `class` 본문에서 정의한 모든 클래스 속성

```python
>>>
(<class "__main__.Meta">,
"MyClass",
(<class "object">,),
{"__module__": "__main__",
"__qualname__": "MyClass",
"foo": <function MyClass.foo at 0x102c7dd08>,
"stuff": 123})
```

## 파이썬2에서의 문법
- 약간 다르게 `__metaclass__` 클래스 속성으로 메타클래스를 지정
- 하지만 `Meta.__new__` 인터페이스는 동일

```python
# 파이썬2
class Meta(type):
    def __new__(meta, name, bases, class_dict):
        # ...
        
class MyClassInPython2(object):
    __metaclass__ = meta
        # ...
```

## 클래스의 파라미터 검증
- 클래스가 정의되기 전에 클래스의 모든 파라미터를 검증하려면:
  - `Meta.__new__` 메서드에 기능을 추가하면 됨

- 여러 면으로 이루어진 다각형을 어떤 타입이든 표현하고 싶은 경우:
  - 검증용 메타클래스를 정의 => 다각형 클래스 계층의 기반 클래스에 사용
  - 기반 클래스에는 같은 검증을 적용하지 말아야 한다는 점 유의

```python
class ValidatePolygon(type):
    def __new__(meta, name, bases, class_dict):
        # 추상 Polygon 클래스는 검증하지 않음
        if bases != (object,):
            if class_dict["sides"] < 3:
                raise ValueError("Polygons need 3+ sides")
        return type.__new__(meta, name, bases, class_dict)
```
