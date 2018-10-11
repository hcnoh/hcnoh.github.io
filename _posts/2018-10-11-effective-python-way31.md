---
layout: post
title: "WAY 31. 재사용 가능한 @property 메서드에는 디스크립터를 사용하자"
date: 2018-10-11 13:45:17
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-11-effective-python-way31
---

## @property의 문제점
- 재사용성:
  - `@property`로 데코레이트하는 메서드를 같은 클래스에 속한 여러 속성에 사용하지 못함
  - 또한 관련 없는 클래스에서도 재사용할 수 없음

```python
class Homework(object):
    def __init__(self):
        self._grade = 0
        
    @property
    def grade(self):
        return self._grade
    
    @grade.setter
    def grade(self, value):
        if not (0 <= value <= 100):
            raise ValueError("Grade must be between 0 and 100")
        self._grade = value
```
