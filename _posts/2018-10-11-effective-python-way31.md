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

>- 위와 다르게 과제와 시험 이외의 항목에도 백분율 검증을 재사용하고 싶다면:
>  - 디스크립터 이용

## 디스크립터 프로토콜(descriptor protocol)
>- 속성에 대한 접근을 언어에서 해석할 방법을 정의
>  - 디스크립터를 이용하면 한 클래스의 서로 다른 속성에 같은 로직을 재사용할 수 있음
- `__get__`/`__set__` 메서드를 갖춤:
  - 파이썬은 이 클래스가 디스크립터 프로토콜을 따른다고 가정
  - 반복 코드 없이도 성적 검증 동작을 재사용할 수 있게 해줌
- 믹스인보다 좋은 방법
- 디스크립터 속성에 접근할 때 파이썬이 무슨 일을 하는지 이해하기 위한 예제

```python
class Grade(object):      # Grade 클래스는 디스크립터 프로토콜을 구현
    def __get__(*args, **kwargs):
        # ...
    
    def __set__(*args, **kwargs):
        # ...

class Exam(object):       # Grade 인스턴스를 클래스 속성으로 포함하는 새로은 Exam 클래스 정의
    # 클래스 속성
    math_grade = Grade()
    writing_grade = Grade()
    science_grade = Grade()

# 프로퍼티 할당
exam = Exam()
exam.writing_grade = 40
```

- 위의 프로퍼티 할당 부분은 다음과 같이 해석됨:

```python
Exam.__dict__["writing_grade"].__set__(exam, 40)
```

- 이번에는 다음과 같이 프로퍼티를 얻어옴

```python
print(exam.writing_grade)
```

- 위의 코드는 다음과 같이 해석됨:

```python
print(Exam.__dict__["writing_grade"]).__get__(exam, Exam)
```

- 이런 방식의 동작을 간단히 설명하면:
  - `Exam` 인스턴스에 `writing_grade` 속성이 없으면 파이썬은 대신 `Exam` 클래스의 속성을 이용

## 디스크립터 구현
- 첫 번째 시도:

```python
class Grade(object):
    def __init__(self):
        self._value = 0
    
    def __get__(self, instance, instance_type):
        return self._value
    
    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError("Grade must be between 0 and 100")
        self._value = value
```

- 위의 시도는 잘못됨:
  - 하나의 `Exam` 인스턴스에 있는 여러 속성에 접근하는 것은 잘 동작
  - 여러 `Exam` 인스턴스에 있는 여러 속성에 접근하는 것은 제대로 동작하지 않음
  
```python
# 하나의 인스턴스에 접근
first_exam = Exam()
first_exam.writing_grade = 82
first_exam.science_grade = 99
print("Writing", first_exam.writing_grade)
print("Science", first_exam.science_grade)

>>>
Writing 82
Science 99
```

```python
# 여러개의 인스턴스에 접근
second_exam = Exam()
second_exam.writing_grade = 75
print("Second", second_exam.writing_grade, "is right")
print("First", first_exam.writing_grade, "is wrong")

>>>
Second 75 is right
First 75 is wrong
```

- 즉, 한 `Grade` 인스턴스가 모든 `Exam` 인스턴스의 `writing_grade` 클래스 속성으로 공유됨
  - `Exam` 클래스를 처음 정의할 때 한 번만 생성!
  - `Exam` 인스턴스를 생성할 때마다 생성되지 않음!

- 두 번째 시도: 문제 해결을 위해 각 `Exam` 인스턴스별로 값을 추적하는 `Grade` 클래스 작성

```python
class Grade(object):
    def __init__(self):
        self._values = {}
        
    def __get__(self, instance, instance_type):
        if instance is None: return self
        return self._values.get(instance, 0)
    
    def __set__(self, instance, value):
        if not (0 <= value <= 100):
            raise ValueError("Grade must be between 0 and 100")
        self._values[instance] = value
```

- 위의 예제도 문제가 있음:
  - 메모리 누수 문제:
    - `_values` 딕셔너리는 프로그램의 수명 동안 `__set__`에 전달된 모든 `Exam` 인스턴스의 참조를 저장
    - 결국 인스턴스의 참조 개수가 절대로 0이 되지 않아 가비지 컬렉터가 정리되지 못하게 됨
  - 파이썬 내장 모듈 `weakref`를 이용하여 이 문제 해결
    - `_values`에 사용한 간단한 딕셔너리를 대체할 수 있는 `WeakKeyDictionary`라는 특별한 클래스를 제공
  - `WeakKeyDictionary`:
    - 런타임에 마지막으로 남은 `Exam` 인스턴스의 참조를 갖고 있다는 사실을 알면 키 집합에서 `Exam` 인스턴스를 제거함
    - 파이썬이 대신 참조를 관리해주고 모든 `Exam` 인스턴스가 더는 사용되지 않으면 `_values` 딕셔너리가 비어 있게 함
    
  ```python
  class Grade(object):
      def __init__(self):
          self._values = WeakKeyDictionary()    # WeakKeyDictionary 사용
      #...
  
  class Exam(object):
      math_grade = Grade()
      writing_grade = Grade()
      science_grade = Grade()
  
  first_exam = Exam()
  first_exam.writing_grade = 82
  second_exam = Exam()
  second_exam.writing_grade = 75
  print("First", first_exam.writing_grade, "is right")
  print("Second", second_exam.writing_grade, "is right")
  
  >>>
  First 82 is right
  Second 75 is right
  ```
