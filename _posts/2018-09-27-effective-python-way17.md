---
layout: post
title: "[Effective Python] WAY 17. 인수를 순회할 때는 방어적으로 하자"
date: 2018-09-27 20:16:38
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-27-effective-python-way17
---

## 파라미터로 객체의 리스트를 받는 함수 예제
- 정규화 함수
    - 미국 텍사스주의 여행자 수를 분석
    - 데이터 집합은 각 도시의 방문자 수, 각 도시에서 전체 여행자 중 몇 퍼센트를 받아들이는지 확인

```python
def normalize(numbers):
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

visits = [15, 35, 80]
percentages = normalize(visits)
print(percentages)

>>>
[11.538461538461538, 26.923076923076923, 61.53846153846154]
```

- 리스트를 확대하려면 텍사스주의 모든 도시가 들어 있는 파일에서 데이터를 읽어야 함:
    - 이 작업을 수행하는 제너레이터를 정의

```python
def normalize(numbers):
    total = sum(numbers)                    # 이터레이터 한 번 소모
    result = []
    for value in numbers:                   # 이미 순회가 끝난 이터레이터는 순회를 해도 결과가 나오지 않음
        percent = 100 * value / total
        result.append(percent)
    return result
    
def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

it = read_visits("/tmp/my_numbers.txt")
percentages = normalize(it)
print(percentages)

>>>
[]
```

- 위의 예제에서 제너레이터의 반환 값에 `normalize`를 호출하면 아무 결과도 생성되지 않음
    - 이터레이터가 결과를 한 번만 생성하기 때문
    - 이미 `StopIteration` 예외를 일으킨 이터레이터나 제너레이터를 순회하면 어떤 결과도 얻을 수 없음

```python
it = read_visits("/tmp/my_numbers.txt")
print(list(it))
print(list(it))     # 이미 소진함

>>>
[15, 35, 80]
[]
```

- 이미 소진한 이터레이터를 순회하더라도 오류가 일어나지는 않음
- 많은 함수들이 결과가 없는 이터레이터와 결과가 있었지만 이미 소진한 이터레이터의 차이를 알려주지 않음

## 문제점 해결
- 입력 이터레이터를 명시적으로 소진하고 전체 콘텐츠의 복사본을 리스트에 저장하여 해결 가능
- 위의 예제와 동일하지만 입력 이터레이터를 방어적으로 복사하는 함수 예제

```python
def normalize_copy(numbers):
    numbers = list(numbers)     # 이터레이터를 복사
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

it = read_visits("/tmp/my_numbers.txt")
percentages = normalize(it)
print(percentages)

>>>
[11.538461538461538, 26.923076923076923, 61.53846153846154]
```

- 위의 예제는 이터레이터를 리스트로 복사하는 과정에서 메모리 소모가 클 수 있음
- 메모리 문제를 피하는 방법은 호출할 때마다 새 이터레이터를 반환하는 함수를 받게 하는 것

```python
def normalize_func(get_iter):
    total = sum(get_iter())         # 새 이터레이터
    result = []
    for value in get_iter():
        percent = 100 * value / total
        result.append(percent)
    return result

percentages = normalize_func(lambda: read_visits(path))     # 제너레이터를 호출하여 매번 새 이터레이터를 생성하는 람다 표현식을 넘겨줌
print(percentages)

>>>
[11.538461538461538, 26.923076923076923, 61.53846153846154]
```

- 위의 예제도 잘 돌아가기는 하지만 람다 함수를 넘겨주는 방법은 세련되지 못함
- 같은 결과를 얻을 수 있는 더 좋은 방법: 이터레이터 프로토콜을 구현한 새 컨테이너 클래스를 제공하는 것

## 이터레이터 프로토콜
- `for` 루프와 관련 표현식이 컨테이너 타입의 콘텐츠를 탐색하는 방법
- 파이썬은 `for x in foo`같은 문장을 만나면 실제로는 `iter(foo)`를 호출
    - `iter()` 함수: `iterable`하지만 이터레이터는 아닌 `list`를 `listiterator` 타입으로 변경하는 함수
    - `iter(foo)`를 호출하면 특별한 메서드인 `foo.__iter__`를 호출
    - `__iter__` 메서드: (`__next__`라는 특별한 메서드를 구현하는) 이터레이터를 반환
    - 마지막으로 `for` 루프는 이터레이터를 모두 소진할 때까지 (`StopIteration` 예외를 일으킬 때까지) 이터레이터 객체의 내장 함수 `next`를 계속 호출
- 결국 `__iter__` 메서드를 제너레이터로 구현하면 이렇게 동작하게 구현이 가능
- 다음은 여행자 데이터를 담은 파일을 읽는 이터러블 컨테이너 클래스

```python
class ReadVisits(object):               # 이 클래스의 구현 방식이 이터레이터 프로토콜
    def __init__(self, data_path):
        self.data_path = data_path
    
    def __iter__(self):
        with open(self.data_path) as f:
            for line in f:
                yield int(line)

def normalize(numbers):                 # 원래 함수에 수정을 가하지 않고 넘겨도 제대로 동작
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

path = "/tmp/my_numbers.txt"
visits = ReadVisits(path)
percentages = normalize(visits)
print(percentages)

>>>
[11.538461538461538, 26.923076923076923, 61.53846153846154]
```

- 이 코드가 동작하는 이유:
    - `normalize`의 `sum` 메서드가 새 이터레이터 객체를 할당하려고 `ReadVisits.__iter__`를 호출하기 때문
    - 숫자를 정규화하는 `for` 루프도 두 번째 이터레이터 객체를 할당할 때 `__iter__`를 호출함
    - 두 이터레이터는 독립적으로 동작: 각각의 순회 과정에서 모든 입력 데이터 값을 얻을 수 있음
    - 유일한 단점: 입력 데이터를 여러 번 읽는다는 점
- 위의 예제에 파라미터가 단순한 이터레이터가 아님을 보장하는 함수를 작성해야 함
    - 내장함수 `iter`에 이터레이터를 넘기면: 이터레이터 자체 반환
    - 내장함수 `iter`에 컨테이너 타입을 넘기면: 매번 새 이터레이터 객체가 반환

```python
def normalize_defensive(numbers):
    if iter(number) is iter(numbers):       # 이터레이터는 거부하는 조건
        raise TypeError("Must supply a container")
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result

path = "/tmp/my_numbers.txt"
visits = [15, 35, 80]
normalize_defensive(visits)     # 오류 없음
visits = ReadVisits(path)
normalize_defensive(visits)     # 오류 없음
it = iter(visits)
normalize_defensive(it)         # 오류 발생: iterable하더라도 컨테이너가 아니기 때문
```

- `list`와 `ReadVisits`은 컨테이너이므로 입력으로 잘 동작
- 그 외 이터레이터 프로토콜을 따르는 어떤 컨테이너 타입에 대해서도 잘 동작
