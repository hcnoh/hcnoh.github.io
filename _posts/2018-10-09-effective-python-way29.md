---
layout: post
title: "WAY 29. 게터와 세터 메서드 대신에 일반 속성을 사용하자"
date: 2018-10-09 12:03:51
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-09-effective-python-way29
---

## 게터와 세터 메서드
- 다른 언어에서 파이썬으로 넘어온 프로그래머들은 자연스레 클래스에 게터(`getter`)와 세터(`setter`) 메서드를 명시적으로 구현하려고 함
    - 이런 게터와 세터를 사용하는 방법은 간단하지만 파이썬답지 않음

```python
class OldResistor(object):
    def __init__(self, ohms):
        self._ohms = ohms
    
    def get_ohms(self):
        return self._ohms
    
    def set_ohms(self, ohms):
        self._ohms = ohms

r0 = OldResistor(50e3)
print("Before: %5r" % r0.get_ohms())
r0.set_ohms(10e3)
print("After: %5r" % r0.get_ohms())

>>>
Before: 50000.0
After: 10000.0
```

- 게터와 세터 메서드는 특히 즉석에서 증가시키기 같은 연산에는 사용하기 어려움

```python
r0.set_ohms(r0.get_ohms() + 5e3)
```

- 이런 유틸리티 메서드는 클래스의 인터페이스를 정의하는 데 도움이 됨
- 기능을 캡슐화하고 사용법을 검증하고 경계를 정의하기 쉽게 해줌
>- 이런 요소는 클래스가 시간이 지나면서 발전하더라도 호출하는 쪽 코드를 절대 망가뜨리지 않도록 설계할 때 중요한 목표가 됨

- 파이썬에서는 명시적인 게터/세터를 구현할 일이 거의 없음
    - 대신 항상 간단한 공개 속성부터 구현하기 시작해야 함

```python
class Resistor(object):
    def __init__(self, ohms):
        self.ohms = ohms
        self.voltage = 0
        self.current = 0

r1 = Resistor(50e3)
r1.ohms = 10e3

r1.ohms += 5e3      # 즉석에서 증가시키기 같은 연산이 자연스럽고 명확
```

- 나중에 속성을 설정할 때 특별한 동작이 일어나야 하면 `@property` 데코레이터(`decorator`)와 이에 대응하는 `setter` 속성을 사용하는 방법으로 바꿀 수 있음
    - 여기서는 `Resistor`의 새 서브클래스를 정의하여 `voltage` 프로퍼티를 할당하면 `current` 값이 바뀌게 해봄

```python
class VoltageResistance(Resistor):
    def __init__(self, ohms):
        super().__init__(ohms)
        self._voltage = 0
    
    @property
    def voltage(self):
        return self._voltage
        
    @voltage.setter                     # voltage에 맞게 current 프로퍼티를 업데이트하게끔 setter 메서드 설정
    def voltage(self, voltage):
        self._voltage = voltage
        self.current = self._voltage / self.ohms

r2 = VoltageResistance(1e3)
print("Before: %5r amps" % r2.current)
r2.voltage = 10                         # voltage에 할당 => setter 메서드 실행
print("After: %5r amps" % r2.current)

>>>
Before: 0 amps
After: 0.01 amps
```

- 다음은 모든 저항값이 0옴보다 큼을 보장하는 클래스

```python
class BoundedResistance(Resistor):
    def __init__(self, ohms):
        super().__init__(ohms)
        
    @property
    def ohms(self):
        return self._ohms
        
    @ohms.setter
    def ohms(self, ohms):
        if ohms <= 0:
            raise ValueError("%f ohms must be > 0" % ohms)
        self._ohms = ohms
```

- 속성에 올바르지 않은 저항값을 할당하면 예외 발생

```python
r3 = BoundedResistance(1e3)
r3.ohms = 0

>>>
ValueError: 0.000000 ohms must be > 0
```

- 생성자에 올바르지 않은 값을 넘겨도 예외 발생
    - `BoundedResistance.__init__`가 `self.ohms = -5`를 할당하는 `Resistor.__init__`를 호출하기 때문
        - 여기에서 `BoundedResistance`의 `@ohms.setter` 메서드가 호출되어 검증 코드가 실행됨

```python
BoundedResistance(-5)

>>>
ValueError: -5.000000 ohms must be > 0
```

- 부모 클래스의 속성을 불변(`immutable`)으로 만드는 데도 `@property`를 사용할 수 있음

```python
class FixedResistance(Resistor):
    # ...
    @property
    def ohms(self):
        return self._ohms
        
    @ohms.setter
    def ohms(self, ohms):
        if hasattr(self, "_ohms"):
            raise AttributeError("Can't set attribute")
        self._ohms = ohms
```

- 이 객체를 생성하고 나서 프로퍼티에 할당하려고 하면 예외가 발생

```python
r4 = FixedResistance(1e3)
r4.ohms = 2e3

>>>
AttributeError: Can't set attribute
```
