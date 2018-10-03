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
