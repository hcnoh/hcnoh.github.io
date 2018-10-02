---
layout: post
title: "WAY 1. 사용중인 파이썬의 버전을 알자"
date: 2018-09-09 20:3:59
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-09-effective-python-way01
---

## 자연스럽지 않은 None 사용
- 파이썬 프로그래머들은 유틸리티 함수를 작성할 때 반환 값 `None`에 특별한 의미를 부여하는 경향이 있음
- 하지만 다음과 같은 경우에는 문제가 발생할 수 있음: 어떤 숫자를 다른 숫자로 나누는 헬퍼 함수 예제

```python
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        return None
```

- 이 경우 0으로 나누는 경우에는 결과가 정의되어 있지 않기 때문에 `None`을 반환하는 것이 자연스러워 보임

```python
result = divide(x, y)
if result is None:
    print("Invalid inputs")
```

- 하지만 분자가 0이되면 문제가 발생
    - 반환 값도 0이 되어버리기 때문
    - `if` 문과 같은 조건에서 결과를 평가할 때 문제가 될 수 있음
    - 아래 예제처럼 오류인지 확인하기 위해 `None` 대신 실수로 `False`에 해당하는 값을 검사할 수도 있음

```python
x, y = 0, 5
result = divide(x, y)
if not result:
    print("Invalid inputs")
```

- 이러한 점들이 함수에서 `None`을 반환하면 오류가 일어나기 쉬운 이유

## None으로 인한 오류를 줄이는 방법
- 반환 값을 두 개로 나눠서 튜플에 담는 방법
    - 튜플의 첫 번째 부분은 작업의 성공 유무를 알려줌
    - 튜플의 두 번째 부분은 계산된 실제 결과
    - 하지만 여전히 잘못된 사용을 할 가능성이 존재

```python
def divide(a, b):
    try:
        return True, a / b
    except ZeroDivisionError:
        return False, None

success, result = divide(x, y)
if not success:
    print("Invalid inputs")

# 잘못된 사용
_, result = divide(x, y)
if not result:
    print("Invalid inputs")
```

- `None`을 반환하지 않는 방법
    - 호출하는 쪽에 예외를 일으켜서 예외 처리

```python
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError as e:
        raise ValueError("Invalid inputs") from e

x, y = 5, 2
try:
    result = divide(x, y)
except VallueError:
    print("Invalid inputs")
else:
    print("Result is %.1f" % result)

>>>
Result is 2.5
```

[Go to the Home Page]({{ site.url }}{{ site.baseurl }})
