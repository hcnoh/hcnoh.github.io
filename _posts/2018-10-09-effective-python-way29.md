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
