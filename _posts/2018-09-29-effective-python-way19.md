---
layout: post
title: "[Effective Python] WAY 19. 키워드 인수로 선택적인 동작을 제공하자"
date: 2018-09-29 15:51:2
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-29-effective-python-way19
---

## 위치 인수 / 키워드 인수

```python
def remainder(number, divisor):
    return number % divisor

remainder(20, 7)                  # 위치 인수
remainder(20, divisor=7)          # 위치 인수 + 키워드 인수
remainder(number=20, divisor=7)   # 키워드 인수
remainder(divisor=7, number=20)   # 키워드 인수
```

- 위치 인수는 키워드 인수 앞에 지정해야 함

```python
remainder(number=20, 7)

>>>
SytaxError: non-keyword arg after keyword arg
```

- 각 인수는 한 번만 지정할 수 있음

```python
remainder(20, number=7)

>>>
SytaxError: remainder() got multiple values for argument "number"
```

## 키워드 인수의 유연성의 이점
- 코드를 처음 보는 사람이 함수 호출을 더 명확하게 이해할 수 있다는 점
- 함수를 정의할 때 기본 값을 설정할 수 있다는 점
    - 큰 통에 들어가는 액체의 유속을 계산하는 예제

```python
def flow_rate(weight_diff, time_diff):
    return weight_diff / time_diff

weight_diff = 0.5
time_diff = 3
flow = flow_rate(weight_diff, time_diff)
print("%.3f kg per second" % flow)

>>>
0.167 kg per second
```

- 보통은 초당 킬로그램 단위로 유속을 아는게 좋음
- 하지만 더 큰 시간 단위로 계산하는 게 좋을 떄도 있음
- 기간 환산 계수를 추가

```python
def flow_rate(weight_diff, time_diff, period):
    return (weight_diff / time_diff) * period
```

- 위 경우 항상 `period`를 설정해줘야 한다는 단점이 있음
    - 기본값을 설정하여 더 깔끔하게 해결 가능

```python
def flow_rate(weight_diff, time_diff, period=1):
    return (weight_diff / time_diff) * period
```

- `period`가 선택적인 인수가 되었음
- 키워드 인수의 세 번째 이점은 기존 호출 코드와 호환성을 유지하면서도 함수의 파라미터를 확장할 수 있다는 점
    - 킬로그램 단위는 물론 다른 무게 단위로도 유속을 계산하려고 앞의 예제를 확장

```python
def flow_rate(weight_diff, time_diff, period=1, units_per_kg=1):
    return ((weight_diff / units_per_kg) / time_diff) * period

pounds_per_hour = flow_rate(weight_diff, time_diff, period=3600, units_per_kg=2.2)
```

- 이 방법의 유일한 문제점:
    - 선택적인 키워드 인수(`period`, `units_per_kg`)를 여전히 위치 인수로도 넘길 수 있다는 점

```python
def flow_rate(weight_diff, time_diff, period=1, units_per_kg=1):
    return ((weight_diff / units_per_kg) / time_diff) * period

pounds_per_hour = flow_rate(weight_diff, time_diff, 3600, 2.2)
```

- 위와 이 한다면 혼동을 일으킬 수 있음:
    - 항상 키워드 이름으로 선택적인 인수를 지정하고 위치 인수로는 아예 넘기지 않아야 함
