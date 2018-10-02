---
layout: post
title: "WAY 11. 이터레이터를 병렬로 처리하려면 zip을 사용하자"
date: 2018-09-18 18:48:37
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-18-effective-python-way11
---

파이썬에서 관련 객체로 구성된 리스트를 많이 사용한다. 서로 다른 리스트를 병렬로 처리하기 위해서 파이썬에서는 `zip`이라는 내장함수를 제공한다. `zip`을 활용했을 때 리스트 컴프리헨션 및 `enumerate`를 사용했을 경우보다 더 간편하다는 점을 확인해보자.

이번 포스팅은 다음의 링크를 참조하였다.
- [출처1](http://excelsior-cjh.tistory.com/100)

## 리스트 컴프리헨션을 이용한 예제
- 파생 리스트의 아이템과 소스 리스트의 아이템은 서로의 인덱스로 연관
- 소스 리스트인 names의 길이만큼 순회하면 두 리스트를 병렬로 순회 가능

```python
names = ["Cecilia", "Lise", "Marie"]
letters = [len(n) for n in names]

longest_name = None
max_letters = 0

for i in range(len(names)):
    count = letters[i]
    if count > max_letters:
        longest_name = names[i]
        max_letters = count

print(longest_name)

>>>
Cecilia
```

## enumerate를 이용한 예제
- 위의 예제는 루프의 인덱스 i로 배열에 접근하는 동작이 두 번 일어남
- 따라서 전체 루프문이 별로 보기 안좋음
- enumerate를 이용하면 약간 개선 가능

```python
names = ["Cecilia", "Lise", "Marie"]
letters = [len(n) for n in names]

for i, name in enumerate(names):
    count = letters[i]
    if count > max_letters:
        longest_name = name
        max_letters = count
```

## zip을 이용한 예제
- `zip`은 지연 제너레이터로 이터레이터 두 개 이상을 감싸안음
- `zip`은 각 이터레이터들로부터 다음 값을 담은 튜플을 반환함
- `zip`을 이용한 예제

```python
names = ["Cecilia", "Lise", "Marie"]
letters = [len(n) for n in names]

for name, count in zip(names, letters):
    if count > max_letters:
        longest_name = name
        max_letters = count
```

## zip의 문제점
- 파이썬2에서는 `zip`이 제너레이터가 아니라서 이터레이터를 완전히 순회하여 생성한 모든 튜플을 반환함
    - 따라서 메모리 문제가 발생할 수 있음
    - 메모리 문제로부터 자유롭기 위해서는 `izip`을 사용하여야 함
- 입력 이터레이터들의 길이가 다르면 `zip`이 이상하게 동작함

```python
names = ["Cecilia", "Lise", "Marie", "Rosalind"]    # names와 letters의 길이가 다른 상황
letters = [7, 4, 5]

for name, count in zip(names, letters):
    print(name)

>>>
Cecilia                                             # Rosalind의 결과는 출력되지 않음
Lise
Marie
```

- 실행할 리스트의 길이가 같다고 확실한 수 있을 때만 `zip`을 사용
- 그렇지 않은 경우는 `itertools`의 `zip_longest`를 사용 (파이썬2는 `izip_longest`)
- `zip_longest` 예제

```python
from itertools import zip_longest

x = [1, 2, 3]
y = [4, 5, 6, 7]

zipped = zip_longest(x, y)
print(list(zipped))

zipped = zip_longest(x, y, fillvalue=0)     # fillvalue 인자에 값을 지정
print(list(zipped))

>>>
[(1, 4), (2, 5), (3, 6), (None, 7)]
[(1, 4), (2, 5), (3, 6), (0, 7)]
```

