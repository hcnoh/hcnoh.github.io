---
layout: post
title: "WAY 32. 지연 속성에는 __getattr__, __getattributet__, setattr__을 사용하자"
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

## __getattr__ 메서드
- 파이썬이 동적 동작 기능 제공이 가능하게끔 하는 메서드
- 클래스에 `__getattr__` 메서드를 정의하면 객체의 인스턴스 딕셔너리에서 속성을 찾을 수 없을 때마다 이 메서드가 호출

```python
class LazyDB(object):
    def __init__(self):
        self.exists = 5
        
    def __getattr__(self, name):
        value = "Value for %s" % name
        setattr(self, name, value)
        return value
```

