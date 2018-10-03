---
layout: post
title: "WAY 22. 딕셔너리와 튜플보다는 헬퍼 클래스로 관리하자"
date: 2018-10-03 22:42:58
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-03-effective-python-way22
---

## 딕셔너리 타입의 유용함
- 객체의 수명이 지속되는 동안 동적인 내부 상태를 관리하는 용도로 유용
  - 동적인 상황? 예상지 못한 식별자들을 관리해야 하는 상황
- 이름을 모르는 학생 집단의 성적을 기록하기 위한 예제

```python
class SimpleGradebook(object):
    def __init__(self):
        self._grades = {}
    
    def add_student(self, name):
        self._grades[name] = []
    
    def report_grade(self, name, score):
        self._grades[name].append(score)
    
    def average_grade(self, name):
        grades = self._grades[name]
        return sum(grades) / len(grades)

book = SimpleGradebook()
book.add_student("Isaac Newton")
book.report_grade("Isaac Newton", 90)
```
