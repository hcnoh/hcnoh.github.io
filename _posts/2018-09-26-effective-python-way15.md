---
layout: post
title: "WAY 15. 클로저가 변수 스코프와 상호 작용하는 방법을 알자"
date: 2018-09-26 19:17:19
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-26-effective-python-way15
---

## 클로저를 이용한 예제
- 리스트 정렬 예제
  - 특정 그룹의 숫자들이 먼저 오도록 우선순위를 매기는 경우:
    - `sort` 메서드에 헬퍼 함수를 `key` 인수로 넘기는 방법

```python
def sort_priority(values, group):
    def helper(x):
        if x in group:
            return (0, x)
        return (1, x)
    values.sort(key=helper)

numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)

>>>
[2, 3, 5, 7, 1, 4, 6, 8]
```

## 위의 정렬 예제가 동작하는 이유
- 파이썬은 클로저(closure)를 지원
  - 클로저: 자신이 정의한 스코프에 있는 변수를 참조하는 함수
  - `helper` 함수가 `sort_priority`의 `group` 인수에 접근할 수 있는 것
- 함수는 파이썬에서 일급 객체(first-class object)
  - 일급 객체?
    - 변수에 할당 가능
    - 다른 함수의 인수로 전달 가능
    - 표현식과 `if` 문 등에서 비교 가능
  - 일급 객체이기 때문에 `sort` 메서드에서 클로저 함수를 `key` 인수로 받을 수 있는 것
- 파이썬에는 튜플을 비교하는 특정한 규칙 존재
  - 먼저 인덱스 0으로 아이템을 비교, 그 다음으로 인덱스 1, 그 다음은 인덱스 2와 같이 진행
  - 따라서 `(0, x)`가 `(1, x)` 보다 우선순위가 높음

## 정렬 예제 2: 우선순위가 높은 아이템을 발견했는지 여부를 추가적으로 확인

```python
def sort_priority2(numbers, group):
    found = False                 # 스코프: "sort_priority2"
    def helper(x):
        if x in group:
            found = True          # 스코프: "helper"
            return (0, x)
        return (1, x)
    numbers.sort(key=helper)
    return found

numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
found = sort_priority2(numbers, group)
print("Found: ", found)
print(numbers)

>>>
Found: False
[2, 3, 5, 7, 1, 4, 6, 8]
```

- 정렬 결과는 올바르지만 `found` 결과는 틀림
  - `found` 변수는 `helper` 클로저에서 `True`로 할당
  - 즉, `sort_priority2`에서 일어나는 할당이 아닌 `helper` 안에서 일어나는 새 변수 정의로 처리됨
  - 스코프 버그(scoping bug)라고도 불림
    - 사실은 버그가 아니고 함수의 지역 변수가 자신을 포함하는 모듈을 오염시키는 문제를 막아주는 기능

## 파이썬 인터프리터의 스코프 탐색 순서
- 이 중 어느 스코프에도 참조한 이름으로 된 변수가 정의되어 있지 않으면 `NameError` 예외가 일어남
  1. 현재 함수의 스코프
  2. (현재 스코프를 담고 있는 다른 함수 같은) 감싸고 있는 스코프
  3. 코드를 포함하고 있는 모듈의 스코프(전역 스코프라고도 함)
  4. (`len`이나 `str`같은 함수를 담고 있는) 내장 스코프

## 클로저에서 데이터를 얻어오기
- `nonlocal` 문
  - 특정 변수 이름에 할당할 때 스코프 탐색이 일어나야 함을 나타냄
  - 모듈 수준의 스코프까지는 탐색할 수 없다는 제약: 전역 변수의 오염을 피하기 위함
- `nonlocal`을 이용한 예제

```python
def sort_priority2(numbers, group):
    found = False
    def helper(x):
        nonlocal found
        if x in group:
            found = True
            return (0, x)
        return (1, x)
    numbers.sort(key=helper)
    return found
```

- `nonlocal` 문의 특징:
  - 데이터를 다른 스코프에 할당하는 시점을 알아보기 쉽게 함
  - `global` 문 보완
    - `global` 문은 변수 할당이 모듈 스코프에 직접 들어가게 함
  - 간단한 함수 이외에는 사용하지 않는 것이 좋음
    - `nonlocal` 문과 관련 변수에 대한 할당이 멀리 떨어진 긴 함수에서는 이해하기 더욱 어려움
    - 복잡해지기 시작하면 헬퍼 클래스로 상태를 감싸는 방법을 이용할 수 있음

```python
class Sorter(object):
    def __init__(self, group):
        self.group = group
        self.found = False
    
    def __call__(self, x):
        if x in self.group:
            self.found = True
            return (0, x)
        return (1, x)

sorter = Sorter(group)
numbers.sort(key=sorter)
assert sorter.found is True
```

## 파이썬2의 스코프
- 파이썬2는 `nonlocal` 키워드를 지원하지 않음
- 위의 예제와 비슷한 동작을 하는 파이썬2의 코드

```python
# 파이썬2
def sort_priority(numbers, group):
    found = [False]
    def helper(x):
        if x in group:
            found[0] = True
            return (0, x)
        return (1, x)
    numbers.sort(key=helper)
    return found[0]
```

- `found`가 `mutable`한 리스트임
- 따라서 클로저에서 일단 `found`를 받아온 후에는 내부 스코프에서 `found[0]=True`로 `found`의 상태를 바꿔서 데이터를 보낼 수 있음
- 리스트가 아니라 다른 `mutable`한 객체 또는 클래스를 활용할 수 있음(딕셔너리 또는 셋)
