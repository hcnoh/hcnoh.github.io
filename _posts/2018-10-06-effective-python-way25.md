---
layout: post
title: "WAY 25. super로 부모 클래스를 초기화하자"
date: 2018-10-06 22:11:22
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-06-effective-python-way25
---

## 부모 클래스의 생성자 호출 문제
- 기존에는 자식 클래스에서 부모 클래스의 `__init__` 메서드를 직접 호출하는 방법으로 부모 클래스를 초기화

```python
class MyBaseClass(object):
    def __init__(self, value):
        self.value = value
    
class MyChildClass(MyBaseClass):
    def __init__(self):
        MyBaseClass.__init__(self, 5)
```

- 위의 방법은 간단한 계층 구조에는 잘 동작
- 하지만 많은 경우 제대로 동작하지 못함
    - 특히 다중 상속(보통은 피해야 함)의 영향을 받는다면 슈퍼클래스의 `__init__` 메서드를 직접 호출하는 행위는 문제가 발생할 수 있음

- 문제가 발생하는 예시

```python
class TimesTwo(object):
    def __init__(self):
        self.value *= 2

class PlusFive(object):
    def __init__(self):
        self.value += 5

class OneWay(MyBaseClass, TimesTwo, PlusFive):      # 이 클래스의 인스턴스를 생성하면 부모 클래스의 순서와 일치하는 결과가 생성
    def __init__(self, value):
        MyBaseClass.__init__(self, value)
        TimesTwo.__init__(self)
        PlusFive.__init__(self)

foo = OneWay(5)
print("First ordering is (5 * 2) + 5 =", foo.value)

>>>
First ordering is (5 * 2) + 5 = 15
```

- 다음은 같은 부모 클래스들을 다른 순서로 정의한 것
    - 이 클래스의 동작은 부모 클래스를 정의한 순서와 일치하지 않음

```python
class AnotherWay(MyBaseClass, PlusFive, TimesTwo):  # 부모 클래스들을 다른 순서로 정의
    def __init__(self, value):
        MyBaseClass.__init__(self, value)
        TimesTwo.__init__(self)
        PlusFive.__init__(self)

bar = AnotherWay(5)
print("Second ordering still is", bar.value)

>>>
Second ordering still is 15
```

## 다이아몬드 상속에서의 문제
- 다이아몬드 상속(diamond inheritance)은 서브클래스가 게층 구조에서 같은 슈퍼클래스를 둔 서로 다른 두 클래스에서 상속받을 때 발생
