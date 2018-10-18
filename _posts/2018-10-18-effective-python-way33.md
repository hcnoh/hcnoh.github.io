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
        print((meta, name, bases, clasS_dict))
        return type.__new__(meta, name, bases, class_dict)

class MyClass(object, metaclass=Meta)
```
