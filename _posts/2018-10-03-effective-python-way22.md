---
layout: post
title: "[Effective Python] WAY 22. 딕셔너리와 튜플보다는 헬퍼 클래스로 관리하자"
date: 2018-10-03 22:42:58
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Effective Python
tags:
- python
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
        by_subject = self._grades[name]
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
>   - 하지만 계층이 여러 계층으로 중첩되면(즉, 딕셔너리를 담은 딕셔너리는 쓰지 말아야 함) 코드 가독성 및 유지보수에 문제가 생김

## 클래스 리팩토링
- 의존 관계에서 가장 아래에 있는 성적부터 고려:
  - 성적은 변하지 않으니 튜플을 사용하는 것이 더 적절해 보임
  - 리스트 안에 성적을 기록하기 위해 (`score`, `weight`) 튜플을 사용

```python
grades = []
grades.append((95, 0.45))
# ...
total = sum(score * weight for score, weight in grades)
total_weight = sum(weight for _, weight in grades)
average_grade = total / total_weight
```

- 일반 튜플은 위치에 의존: 수정이 복잡함
- 튜플에서 세 번째 값을 `_`로 받아서 그냥 무시하도록 하는 예제(관례적으로 사용하지 않는 변수는 `_`을 사용)

```python
grades = []
grades.append((95, 0.45, "Great job"))
# ...
total = sum(score * weight for score, weight, _ in grades)
total_weight = sum(weight for _, weight, _ in grades)
average_grade = total / total_weight
```

- 튜플을 점점 더 길게 확장하는 방식은 좋지 않음: 다른 방법 고려
- `collections` 모듈의 `namedtuple` 타입을 활용할 수 있음
  - `namedtuple`을 사용하면 작은 `immutable data class`를 쉽게 정의 가능

```python
import collections
Grade = collections.namedtuple("Grade", ("score", "weight"))
```

- `namedtuple`의 특징:
  - `immutable data class`는 위치 인수나 키워드 인수로 생성 가능
  - 필드는 이름이 붙은 속성으로 접근 가능:
    - 나중에 요구 사항이 또 변하면 단순 데이터 컨테이너에 동작을 추가해야 할 때 `namedtuple`에서 직접 작성한 클래스로 쉽게 변경 가능
- `namedtuple`의 제약:
  - 기본 인수 값 설정 불가능:
    - 따라서 속성이 많아지면 다루기 힘들어 짐
    - 속성을 사용할 때는 클래스를 직접 정의하는 것이 나을 수 있음
  - 인스턴스의 속성 값을 여전히 숫자로 된 인덱스 및 순회 방법으로 접근 가능
    - 의도와 다르게 외부로 사용되었을 경우에는 수정이 더 어려워 질 수 있음
    - `namedtuple` 인스턴스를 사용하는 방식을 모두 제어할 수 없다면 클래스를 직접 정의하는 것이 좋음

- `namedtuple`을 이용하여 성적들을 담은 단일 과목을 표현하는 클래스를 작성할 수 있음

```python
class Subject(object):
    def __init__(self):
        self._grades = []
        
    def report_grade(self, score, weight):
        self._grades.append(Grade(score, weight))
    
    def average_grade(self):
        total, total_weight = 0, 0
        for grade in self._grades:
            total += grade.score * grade.weight
            total_weight += grade.weight
        return total / total_weight
```

- `namedtuple`을 이용하여 한 학생이 공부한 과목들을 표현하는 클래스 작성

```python
class Student(object):
    def __init__(self):
        self._subjects = {}
        
    def subject(self, name):
        if name not in self._subjects:
            self._subjects[name] = Subject()
        return self._subjects[name]
    
    def average_grade(self):
        total, count = 0, 0
        for subject in self._subjects.values():
            total += subject.average_grade()
            count += 1
        return total / count
```

- `namedtuple`을 이용하여 학생의 이름을 키로 사용해 동적으로 모든 학생을 담을 컨테이너 작성

```python
class Gradebook(object):
    def __init__(self):
        self._students = {}
    
    def student(self, name):
        if name not in self._students:
            self._students[name] = Student()
        return self._students[name]
```

- 세 코드를 합치면 이전에 구현한 코드보다 두 배는 길지만 가독성은 훨씬 좋음
- 또한 이 클래스들을 사용하는 방식도 훨씬 명확하고 확장이 쉬움

```python
book = Gradebook()
albert = book.student("Albert Einstein")
math = albert.subject("Math")
math.report_grade(80, 0.10)
# ...
print(albert.average_grade())

>>>
81.5
```
