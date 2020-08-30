---
layout: post
title: "[Effective Python] WAY 16. 리스트를 반환하는 대신 제너레이터를 고려하자"
date: 2018-09-27 20:5:43
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-27-effective-python-way16
---

- 리스트를 반환하는 예제

```python
def index_words(text):
    result = []
    if text:
        result.append(0)
    for index, letter in enumerate(text):
        if letter == " ":
            result.append(index + 1)
    return result

address = "four score and seven years ago..."     # 샘플 입력이 적은 경우는 함수가 잘 동작
result = index_words(address)
print(result[:3])

>>>
[0, 5, 11]
```

- 위 예제의 문제점
  - 코드가 복잡하고 깔끔하지 않음
  - 반환하기 전 모든 결과를 리스트에 저장해야 한다는 점:
    - 메모리 문제
  - 제너레이터를 사용하여 더 좋은 코드를 구현할 수 있음

## 제너레이터란?
- `yield` 표현식을 사용하는 함수
- 호출되면 실제로 실행하지 않고 바로 이터레이터를 반환
- 내장 함수 `next`를 호출할 때마다 이터레이터는 제너레이터가 다음 `yield` 표현식으로 진행하게 함
- 위의 예시를 제너레이터를 이용하여 수정

```python
def index_words_iter(text):
    if text:
        yield 0
    for index, letter in enumerate(text):
        if letter == " ":
            yield index + 1
```

- 결과 리스트와 연동하는 부분이 모두 사라져서 훨씬 이해하기 쉬움
- 제너레이터 호출로 반환되는 이터레이터를 내장 함수 `list`에 전달: 손쉽게 리스트로 변환 가능

```python
result = list(index_words_iter(address))
```
