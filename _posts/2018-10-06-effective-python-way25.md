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

- 기존에는 자식 클래스에서 부모 클래스의 `__init__` 메서드를 직접 호출하는 방법으로 부모 클래스를 초기화

```python
class MyBaseClass(object):
    def __init__(self, value):
        self.value = value
    
class MyChildClass(MyBaseClass):
    def __init__(self):
        MyBaseClass.__init__(self, 5)
```
