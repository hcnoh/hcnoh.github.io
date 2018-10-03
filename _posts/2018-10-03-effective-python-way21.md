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

> - 호출하는 쪽에서 의도를 명확히 드러내도록 강제하는 방법:
>   - 파이썬3에서의 키워드 전용 인수(`keyord-only argument`)로 정의할 수 있음
  
