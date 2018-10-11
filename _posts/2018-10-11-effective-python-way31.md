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

galileo = Homework()    # 쉽게 사용 가능
galileo.grade = 95
```

- 학생들의 시험 성적을 매기는 경우: 시험은 여러 과목, 과목별로 점수

```python
class Exam(object):
    def __init__(self):
        self._writing_grade = 0
        self._math_grade = 0
    
    @staticmethod
    def _check_grade(value):
        if not (0 <= value <= 100):
            raise ValueError("Grade must be between 0 and 100")
```

- 이런 식의 구현은 코드가 너무 장황해짐:
  - 시험 영역마다 새 `@property`와 관련 검증이 필요하기 때문
  - 즉, 범용성이 좋지 못함

```python
    @property
    def writing_grade(self):
        return self._writing_grade
        
    @writing_grade.setter
    def writing_grade(self, value):
        self._check_grade(value)
        self._writing_grade = value
    
    @property
    def math_grade(self):
        return self._math_grade
    
    @math_grade.setter
    def math_grade(self, value):
        self._check_grade(value)
        self._math_grade = value
```

>- 위와 다르게 과제와 시험 이외의 항목에도 북분율 검증을 재사용하고 싶다면:
>  - 디스크립터 이용

## 디스크립터 프로토콜(descriptor protocol)
- 속성에 대한 접근을 언어에서 해석할 
