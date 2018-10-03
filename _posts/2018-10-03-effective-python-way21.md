---
layout: post
title: "WAY 21. 키워드 전용 인수로 명료성을 강요하자"
date: 2018-10-03 08:58:14
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-03-effective-python-way21
---

- 나누기 예제
  - `ZeroDivisionError` 예외를 무시하고 무한대 값을 반환하기를 원함
  - `OverflowError` 예외를 무시하고 0을 반환하기를 원함

```python
def safe_division(number, divisor, ignore_overflow, ignore_zero_division):
    try:
        return number/ divisor
    except OverflowError:
        if ignore_overflow:
            return 0
        else:
            raise
    except ZeroDivisionError:
        if ignore_zero_division:
            return float("int")
        else:
            raise

result = save_division(1, 10**500, True, False)
print(result)                                     # 오버플로우를 무시하교 0을 반환
result = safe_divisoion(1, 0, False, True)
print(result)                                     # 0으로 나누면서 일어나는 오류를 무시하고 무한대를 반환

>>>
0.0
inf
```

- 위 예제의 단점은 두 불 인수의 위치를 혼동하기 쉽다는 점
- 코드 가독성을 높이는 방법: 키워드 인수 활용

```python
# 기본 동작을 덮어쓰고 무시할 플래그 지정
def safe_division(number, divisor, ignore_overflow=False, ignore_zero_division=False):
    try:
        return number/ divisor
    except OverflowError:
        if ignore_overflow:
            return 0
        else:
            raise
    except ZeroDivisionError:
        if ignore_zero_division:
            return float("int")
        else:
            raise

result = save_division(1, 10**500, ignore_overflow=True)
print(result)                                             # 오버플로우를 무시하교 0을 반환
result = safe_divisoion(1, 0, ignore_zero_division=True)
print(result)                                             # 0으로 나누면서 일어나는 오류를 무시하고 무한대를 반환

>>>
0.0
inf
```

- 이런 키워드 인수가 선택적인 동작:
  - 호출하는 쪽에 키워드 인수로 의도를 명확하게 드러내라고 강요할 방법이 없음
  - 즉, 아래처럼 사용이 가능

```python
save_difision(1, 10***500, True, False)
```

## 파이썬3의 키워드 전용 인수
> - 호출하는 쪽에서 의도를 명확히 드러내도록 강제하는 방법:
>   - 파이썬3에서의 키워드 전용 인수(`keyord-only argument`)로 정의할 수 있음

```python
def safe_division(number, divisor, *, ignore_overflow=False, ignore_zero_division=False):
    try:
        return number/ divisor
    except OverflowError:
        if ignore_overflow:
            return 0
        else:
            raise
    except ZeroDivisionError:
        if ignore_zero_division:
            return float("int")
        else:
            raise

safe_division(1, 10**500, True, False)          # 위치 인수를 사용하는 함수 호출은 동작하지 않음

>>>
TypeError: save_division() takes 2 positional arguments but 4 were given
```

```python
safe_division(1, 0, ignore_zero_division=True)  # 키워드 인수와 그 기본값은 잘 동작

try:
    safe_division_c(1, 0)
except ZeroDivisionError:
    pass
```

## 파이썬2의 키워드 전용 인수
- 키워드 전용 인수를 지정하는 명시적인 문법은 없음
  - `**` 연산자를 사용하여 올바르지 않은 함수 호출을 할 때 `TypeError`를 일으키는 방법을 사용할 수 있음

```python
# 파이썬2
def print_args(*args, **kwargs):          # *args: 가변 개수의 위치 인수
    print "Positional:", args             # **kwargs: 가변 개수의 키워드 인수
    print "Keyword:", kwargs

print_args(1, 2, foo="bar", stuff="meep")

>>>
Positional: (1, 2)
Keyword: {"foo": "bar", "stuff:" "meep"}
```

- 파이썬2에서 키워드 인수를 처리하는 방법
  - `**kwargs`를 받게 만들어서 키워드 전용 인수를 받게 함
  - 그 다음 `pop` 메서드로 `kwargs` 딕셔너리에서 원하는 키워드 인수를 꺼냄
  - 마지막으로 `kwargs`에 더는 남아있는 키워드가 없음을 확인

```python
# 파이썬2
def safe_division(number, divisor, **kwargs):
    ignore_overflow = kwargs.pop("ignore_overflow", False)
    ignore_zero_div = kwargs.pop("ignore_zero_division", False)
    if kwargs:
        raise TypeError("Unexpected **kwargs: %r" % kwargs)
    # ...
```

- 이제 키워드 인수를 넘기든 안 넘기든 함수 호출 가능

```python
safe_division(1, 10)
safe_division(1, 0, ignore_zero_division=True)
safe_division(1, 10**500, ignore_overflow=True)
```

```python
safe_division(1, 0, False, True)      # 키워드 전용 인수를 위치로 넘기면 에러

>>>
TypeError: safe_division() takes 2 positional arguments but 4 were given
```

```python
safe_division(0, 0, unexpected=True)  # 원치 않는 키워드 인수를 넘겨도 에러

>>>
TypeError: Unexpected **kwargs: {"unexpected": True}
```
