---
layout: post
title: "[Effective Python] WAY 8. 리스트 컴프리헨션에서 표현식을 두 개 넘게 쓰지 말자"
date: 2018-09-16 22:58:3
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-16-effective-python-way08
---

## 리스트 컴프리헨션의 다중 루프
- 리스트 컴프리헨션의 다중 루프 예제

```python
matrix = [[1, 2, 3], [4, 5, 6]]
flat = [x for row in matrix for x in row]
print(flat)

>>>
[1, 2, 3, 4, 5, 6]
```

- 위의 예제와 동치인 일반 루프문

```python
matrix = [[1, 2, 3], [4, 5, 6]]
flat = []
for row in matrix:
    for x in row:
        flat.append(x)
```

- 입력 리스트 레이아웃을 두 레벨로 중복하여 구성

```python
matrix = [[1, 2, 3], [4, 5, 6]]
squared = [[x**2 for x in row] for row in matrix]
print(squared)

>>>
[[1, 4, 9], [16, 25, 36]]
```

## 여러 줄 짜리 리스트 컴프리헨션
- 여러 줄의 리스트 컴프리헨션 예제

```python
my_lists = [
    [[1, 2, 3], [4, 5, 6]],
    # ...
]
flat = [x for sublist1 in my_lists
        for sublist2 in sublist1
        for x in sublist2]
```

- 위의 예제와 동치인 일반 루프문

```python
flat = []
for sublist1 in my_lists:
    for sublist2 in sublist1:
        flat.extend(sublist2)
```

## 다중 if 조건문 포함
- 예제

```python
a = [1, 2, 3, 4, 5, 6]
b = [x for x in a if x > 4 if x % 2 == 0]
c = [x for x in a if x > 4 and x % 2 == 0]
```

- 좀 더 복잡한 예제

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
filtered = [[x for x in row if x % 3 == 0]
            for row in matrix if sum(row) >= 10]
print(filtered)

>>>
[[6], [9]]
```

- 위의 예제는 매우 복잡함
- 이런 식의 복잡한 리스트 컴프리헨션은 피하는 것이 좋음
    - 가독성 문제
    - 조건 두 개, 로프 두 개, 혹은 조건 한 개 + 루프 한 개 정도가 적당함
    - 더 복잡해지면 헬퍼 함수 활용 (WAY 16. 리스트를 반환하는 대신 제너레이터를 고려하자)

