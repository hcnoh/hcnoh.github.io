---
layout: post
title: "WAY 7. map과 filter 대신 리스트 컴프리헨션을 사용하자"
date: 2018-09-16 21:57:58
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-16-effective-python-way07
---

파이썬에서는 리스트 컴프리헨션 기능을 제공하는 문법이 있다. 이번 장에서는 리스트 컴프리헨션 기능과 관련된 문법을 정리해보도록 하자.

## 리스트 컴프리헨션
- list comprehension: 리스트 함축 표현식
- 한 리스트에서 다른 리스트를 만들어내는 간결한 문법
- 리스트 컴프리헨션 예제

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
squares = [x**2 for x in a]
print(squares)

>>>
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

## 파이썬 내장함수 map
- 리스트 컴프리헨션과 같은 기능 제공
- 리스트 컴프리헨션과 다르게 인수가 여러개인 경우도 적용 가능
- `lambda`함수를 생성해야 하기 때문에 리스트 컴프리헨션보다는 명확하지 못함
- map 예제

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
squares = map(lambda x: x ** 2, a)
```

## 조건식 추가
- 리스트 컴프리헨션은 map과 다르게 조건식을 추가할 수 있음
- 조건식 추가 예제

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_squares = [x**2 for x in a if x % 2 == 0]
print(even_squares)

>>>
[4, 16, 36, 64, 100]
```

- map도 filter과 연계해서 조건식 추가 가능: but 읽기 어려운 단점
- map 조건식 추가 예제

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10
alt = map(lambda x: x**2, filter(lambda x: x % 2 == a))
```

## 딕셔너리/세트 컴프리헨션
- 딕셔너리와 세트에도 컴프리헨션에 해당하는 문법 존재
- 컴프리헨션 문법을 사용하면 알고리즘을 작성할 때 파생되는 자료 구조를 쉽게 생성 가능
- 예제

```python
chile_ranks = {"ghost": 1, "habanero": 2, "cayenne": 3}
rank_dict = {rank: name for name, rank in chile_ranks.items()}
chile_len_set = {len(name) for name in rank_dict.values()}
print(rank_dict)
print(chile_len_set)

>>>
{1: "ghost", 2: "habanero", 3: "cayenne"}
{8, 5, 7}
```

