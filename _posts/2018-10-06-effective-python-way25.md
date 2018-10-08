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
- 다이아몬드 상속(`diamond inheritance`):
    - 서브클래스가 게층 구조에서 같은 슈퍼클래스를 둔 서로 다른 두 클래스에서 상속받을 때 발생
    - 공통 슈퍼클래스의 `__init__` 메서드를 여러 번 실행하게 함 => 예상치 못한 동작을 일으킴
- 문제 예시: `MyBaseClass`에서 상속받는 자식 클래스 두 개

```python
class MyBaseClass(object):
    def __init__(self, value):
        self.value = value

class TimesFive(MyBaseClass):
    def __init__(self, value):
        MyBaseClass.__init__(self, value)
        self.value *= 5

class PlusTwo(MyBaseClass):
    def __init__(self, value):
        MyBaseClass.__init__(self, value)
        self.value += 2
```

- 다음으로 이 두 클래스 모두에서 상속받는 자식 클래스를 정의: MyBaseClass를 다이아몬드의 꼭대기로 만듦

```python
class ThisWay(TimesFive, PlusTwo):
    def __init__(self, value):
        TimesFive.__init__(self, value)
        PlusTwo.__init__(self, value)

foo = ThisWay(5)
print("Should be (5 * 5) + 2 = 27 but is", foo.value)

>>>
Should be (5 * 5) + 2 = 27 but is 7
```

- 이런 문제가 발생하는 이유:
    - 두 번째 부모 클래스의 생성자 `PlusTwo.__init__`를 호출하는 코드 => `MyBaseClass.__init__`가 두 번째 호출될 때 `self.value`를 다시 5로 리셋

## 파이썬2의 해결 방법
- 파이썬 2.2에서는 이 문제를 해결하기 위해서:
    - `super`라는 내장 함수를 추가
    - 메서드 해석 순서(`MRO, Method Resolution Order`)를 정의
        - `MRO`는 어떤 슈퍼클래스부터 초기화하는지를 정함(예를 들면 깊이 우선, 왼쪽에서 오른쪽으로)
        - 또한 다이아몬드 계층 구조에 있는 공통 슈퍼클래스를 단 한 번만 실행하게 해줌

```python
# 파이썬2
class MyBaseClass(object):
    def __init__(self, value):
        self.value = value
        
class TimesFiveCorrect(MyBaseClass):
    def __init__(self, value):
        super(TimesFiveCorrect, self).__init__(value)
        self.value *= 5

class PlusTwoCorrect(MyBaseClass):
    def __init__(self, value):
        super(PlusTwoCorrect, self).__init__(value)
        self.value += 2

class GoodWay(TimesFiveCorrect, PlusTwoCorrect):
    def __init__(self, value):
        super(GoodWay, self).__init__(value)

foo = GoodWay(5)
print "Should be 5 * (5 + 2) = 35 and is", foo.value

>>>
Should be 5 * (5 + 2) = 35 and is 35
```

- 이 순서는 뒤에서 시작함
- 순서를 바꾸는 방법은? 없음
    - `MRO`가 정의하는 순서와 일치
- `MRO` 순서는 `mro`라는 클래스 메서드로 확인 가능

```python
from pprint import pprint
pprint(GoodWay.mro())

>>>
[<class '__main__.GoodWay'>,
<class '__main__.TimesFiveCorrect'>,
<class '__main__.PlusTwoCorrect'>,
<class '__main__.MyBaseClass'>,
<class 'object'>]
```

- 호출 순서
    1. `GoodWay(5)`를 호출하면
    2. 이 생성자는 `TimesFivecorrect.__init__`를 호출
    3. 이는 `PlusTwoCorrect.__init__`를 호출
    4. 이는 다시 `MyBaseClass.__init__`를 호출

- 호출이 다이아몬드의 꼭대기에 도달하면, 모든 초기화 메서드는 실제 `__init__` 함수가 호출된 순서의 역순으로 실행됨
    1. `MyBaseClass.__init__`는 5라는 값을 `value`에 할당
    2. `PlusTwoCorrect.__init__`는 2를 더해서 `value`가 7이 됨
    3. `TimesFivecorrect.__init__`는 그 값을 5와 곱하여 `value`는 35가 됨

- `super`에는 두 가지 문제가 존재:
    - 문법이 장황:
        - 현재 정의하는 클래스, `self` 객체, 메서드 이름(보통 `__init__`)과 모든 인수를 설정해줘야 함
    - `super`를 호출하면서 현재 클래스의 이름을 지정해야 함
        - 클래스의 이름을 변경(클래스 계층 구조를 개선할 때 아주 흔히 하는 조치임)하면 `super`를 호출하는 모든 코드를 수정해야 함
        
## 파이썬3의 해결 방법
- 파이썬3에서는 `super`를 인수 없이 호출하면 `__class__`와 `self`를 인수로 넘겨서 호출한 것으로 처리하여 이 문제를 해결
    - 파이썬2에서는 `__class__`가 정의되어 있지 않아 제대로 동작하지 않음
- 어쨌든 파이썬3에서는 항상 `super`를 사용해야 함: 명확하고 간결하며 항상 제대로 동작하기 때문

```python
class Explicit(MyBaseClass):
    def __init__(self, value):
        super(__class__, self).__init__(value * 2)

class Implicit(MyBaseClass):
    def __init__(self, value):
        super().__init__(value * 2)

assert Explicit(10).value == Implicit(10).value
```
