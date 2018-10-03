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

이번 포스팅은 다음의 링크를 참고하여 작성하였다.
- [출처 1](https://www.tutorialspoint.com/python/dictionary_setdefault.htm)

## 딕셔너리 타입의 유용함
- 객체의 수명이 지속되는 동안 동적인 내부 상태를 관리하는 용도로 유용
  - 동적인 상황? 예상지 못한 식별자들을 관리해야 하는 상황
- 이름을 모르는 학생 집단의 성적을 기록하기 위한 클래스 예제

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
print(book.average_grade("Isaac Newton"))

>>>
90.0
```

## 딕셔너리 타입의 문제점
- 사용하기 쉬움
  - 과도하게 사용하다 코드를 취약하게 작성할 위험이 존재
- 모든 성적을 과목별로 저장하는 클래스 예제

```python
class BySubjectGradebook(object):
    def __init__(self):
        self._grades = {}
    
    def add_student(self, name):
        self._grades[name] = {}
    
    def report_grade(self, name, subject, grade):
        by_subject = self._grades[name]
        grade_list = by_subject.setdefault(subject, [])
        grade_list.append(grade)
    
    def average_grade(self, name):
        by_subject = self._grades[name]
        total, count = 0. 0
        for grades in by_subject.values():
            totall += sum(grades)
            count += len(grades)
        return total / count

book = BySubjectGradebook()
book.add_student("Albert Einstein")
book.report_grade("Albert Einstein", "Math", 75)
book.report_grade("Albert Einstein", "Math", 65)
book.report_grade("Albert Einstein", "Gym", 90)
book.report_grade("Albert Einstein", "Gym", 95)
```

- `setdefault()` 메서드:
  - `key`를 입력받아 해당되는 `value` 반환, `key`가 없으면 두 번째 인자를 `default`로 반환
  - 예제
  
```python
dict = {"Name": "Zara", "Age": 7}
print("Value: %s" % dict.setdefault("Age", None))
print("Value: %s" % dict.setdefault("Sex", None))

>>>
Value: 7
Value: None
```

- 각 점수가 차지하는 비중을 매겨서 평균을 구할 수 있는 클래스 예제
  - 과목(`key`)을 성적(`value`)에 매핑하지 않고, 성적과 비중을 담은 튜플(`score`, `weight`)에 매핑

```python
class WeightedGradebook(object):
    def __init__(self):
        self._grades = {}
    
    def add_student(self, name):
        self._grades[name] = {}
    
    def report_grade(self, name, subject, score, weight):
        by_subject = self._grades[name]
        grade_list = by_subject.setdefault(subject, [])
        grade_list.append((score, weight))
    
    def average_grade(self, name):
        by_subject = selff._grades[name]
        score_sum, score_count = 0, 0
        for subject, scores in by_subject.items():
            subject_avg, total_weight = 0, 0
            for score, weight in scores:
                # ...
        return score_sum / score_count

book.report_grade("Albert Einstein", "Math", 80, 0.10)
```

- `average_grade` 메서드가 매우 복잡해짐
- 클래스 사용 방법 역시 복잡함
  - 위치 인수에 있는 숫자들이 무엇을 의미하는지도 명확하지 않음

> - 이렇게 복잡한 경우에는 딕셔너리와 튜플 대신 클래스의 계층 구조를 사용하는 것이 좋음
>   - 내장 딕셔너리와 튜플 타입을 사용하면 내부 관리용으로 층층이 타입 추가가 용이
>   - 하지만 계층이 여러 계층으로 중첩하면(즉, 딕셔너리를 담은 딕셔너리는 쓰지 말아야 함) 코드 가독성 및 유지보수에 문제가 생김
