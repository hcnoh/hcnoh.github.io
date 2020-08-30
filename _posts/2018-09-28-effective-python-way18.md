---
layout: post
title: "[Effective Python] WAY 18. 가변 위치 인수로 깔끔하게 보이게 하자"
date: 2018-09-28 20:59:58
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-28-effective-python-way18
---

## 가변 개수의 위치 인수를 받는 예제

```python
def log(message, values):
    if not values:
        print(message)
    else:
        value_str = ", ".join(str(x) for x in values)
        print("%s: %s" % (message, values_str))

log("My numbers are", [1, 2])
log("Hi there", [])             # 빈 리스트를 넘겨야 하는 것이 불편함

>>>
My numbers are: 1, 2
Hi there
```

- 로그로 남길 값이 없을 때 빈 리스트를 넘겨야 하는 것이 불편함:
    - `*` 기호를 마지막 위치 파라미터 이름 앞에 붙이면 것으로 해결 가능

```python
def log(message, *values):  # 첫 번째 파라미터인 message는 필수, 그 다음에 나오는 위치 인수는 몇 개든 선택적임
    if not values:
        print(message)
    else:
        value_str = ", ".join(str(x) for x in values)
        print("%s: %s" % (message, values_str))

log("My numbers are", 1, 2)
log("Hi there")             # 호출하는 쪽만 수정

>>>
My numbers are: 1, 2
Hi there
```

- `*` 연산자를 이용하여 리스트를 입력으로 사용할 수 있음

```python
def log(message, *values):  # 첫 번째 파라미터인 message는 필수, 그 다음에 나오는 위치 인수는 몇 개든 선택적임
    if not values:
        print(message)
    else:
        value_str = ", ".join(str(x) for x in values)
        print("%s: %s" % (message, values_str))

favorites = [7, 33, 99]
log("Favorite colors", *favorites)

>>>
Favorite colors: 7, 33, 99
```

## 가변 개수의 위치 인수를 받는 방법의 문제점
- 가변 인수가 함수에 전달되기에 앞서 항상 튜플로 변환된다는 점
    - 함수를 호출하는 쪽에서 제너레이터에 `*` 연산자를 쓰면 제너레이터가 모두 소진할 때까지 순회됨
    - 결과로 만들어지는 튜플은 제너레이터로부터 생성되는 모든 값을 담게 됨
    - 따라서 메모리 문제가 발생할 수 있음

```python
def my_generator():
    for i in range(10):
        yield i

def my_func(*args):
    print(args)

it = my_generator()     # 제너레이터 생성
my_func(*it)            # 제너레이터를 모두 순회하며 생성되는 값들이 전부 튜플로 변환: 메모리 문제 발생 가능

>>>
(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

- 호출 코드를 모두 변경하지 않고서는 새 위치 인수를 추가할 수 없는 점
    - 인수 리스트의 앞쪽에 위치 인수를 추가하면 기존의 호출 코드가 수정 없이는 이상하게 동작함

```python
def log(sequence, message, *values):
    if not values:
        print("%s: %s" % (sequence, message))
    else:
        values_str = ", ".join(str(x) for x in values)
        print("%s: %s: %s" % (sequence, message, values_str))
log(1, "Favorites", 7, 33)      # 새로운 용법은 OK
log("Favorite numbers", 7, 33)  # 오래된 용법은 제대로 동작하지 않음

>>>
1: Favorites: 7, 33
Favorite numbers: 7: 33     # sequence 인수를 받지 못했기 때문에 7을 message 파라미터로 사용
```

- 이런 버그는 코드에서 예외를 일으키지 않고 계속 실행되므로 발견하기가 극히 여러움
- `*args`를 받는 함수를 확장할 때 키워드 전용 인수를 사용해야 함
    - "WAY 21. 키워드 전용 인수로 명료성을 강요하자"

