---
layout: post
title: "WAY 5. 시퀀스를 슬라이스하는 방법을 알자"
date: 2018-09-16 20:13:57
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-16-effective-python-way05
---
이번 장에서는 파이썬의 시퀀스 슬라이싱 문법을 정리한다. 슬라이싱의 대상이 되는 것은 파이썬 내장 타입인 `list`, `str`, `bytes`와 `__getitem__`과 `__setitem__`이라는 특별한 메서드를 구현하는 파이썬의 클래스이다.

이번 포스팅은 다음의 링크를 참조하였다.
- [출처1](https://blueshw.github.io/2016/01/20/2016-01-20-shallow-copy-deep-copy/)
- [출처2](https://wikidocs.net/16038)

## 슬라이싱의 기본 문법
- 슬라이싱 문법의 기본 형태:
  - `some_list[start:end]`
  - `start 인덱스`는 포함되고 `end 인덱스`는 제외
- 슬라이싱 예시:

```python
a = ["a", "b", "c", "d", "e", "f", "g", "h"]
print("Firts four:", a[:4])     # 리스트의 처음부터 슬라이스할 때는 인덱스 0 생략: assert a[:5] == a[0:5]
print("Last four:", a[-4:])     # 리스트의 끝까지 슬라이스 할 때는 마지막 인덱스 생략: assert a[5:] == a[5:len(a)]
print("Middle two:", a[3:-3])   # 리스트의 끝을 기준으로 오프셋 계산할 때는 음수로 슬라이스

>>>
First four: ["a", "b", "c", "d"]
First four: ["e", "f", "g", "h"]
Middle two: ["d", "e"]
```

## 슬라이싱 및 인덱싱 범위
- 슬라이싱은 `start 인덱스`/`end 인덱스`가 경계를 벗어나도 적절히 처리

```python
first_twenty_items = a[:20]
last_twenty_items = a[-20:]
```

- 인덱스 직접 접근은 경계를 벗어날 수 없음

```python
a[20]

>>>
IndexError: list index out of range
```

- 음수 인덱싱은 다음과 같은 주의사항이 있음

```python
a[-3:]    # 제대로 동작
assert a[-0:] == a[:]
```

## 리스트의 할당
- 슬라이싱의 결과는 완전히 새로운 리스트:
  - 주의) 슬라이싱은 Deep copy처럼 보이지만 사실은 Shallow copy

```python
a = ["a", "b", "c", "d", "e", "f", "g", "h"]
b = a[4:]
print("Before: ", a)
print("Before: ", b)
b[1] = 99
print("After: ", a)
print("After: ", b)

>>>
Before: ["a", "b", "c", "d", "e", "f", "g", "h"]
Before: ["e", "f", "g", "h"]
After: ["a", "b", "c", "d", "e", "f", "g", "h"]
After: ["e", 99, "g", "h"]
```

- 튜플 할당:

```python
a, b = c[:2]
```

- 슬라이스 할당:

```python
a = ["a", "b", "c", "d", "e", "f", "g", "h"]
print("Before :", a)
a[2:7] = [99, 22, 14]     # 할당의 길이가 달라도 됨
print("After: ", a)

>>>
Before: ["a", "b", "c", "d", "e", "f", "g", "h"]
After: ["a", "b", 99, 22, 14, "h"]
```

- 원본 리스트의 복사: Deep copy

```python
b = a[:]
assert b == a and b is not a    # 같은 내용을 담지만 다른 리스트 인스턴스
```

- 리스트의 할당: Shallow copy

```python
b = a
assert a is b                   # 같은 리스트 
```

## Shallow copy / Deep copy
- 단순 객체 복제
  - `list`와 같은 `mutable` 객체는 원본/사본이 동일한 객체를 참조
  - 숫자, 문자열 등의 `immutable` 객체는 원본/사본이 동일한 객체를 참조하지 않음

```python
a = [1, 2, 3, 4]  # list 객체 원본
b = a             # list 객체 사본: 원본과 동일한 객체를 참조
print(b)
b[2] = 100        # b의 item 수정
print(b)
print(a)

>>>
[1, 2, 3, 4]
[1, 2, 100, 4]
[1, 2, 100, 4]    # a도 수정됨
```

```python
a = 10            # 숫자 객체 원본
b = a             # 숫자 객체 사본: 원본과 동일한 객체를 참조하지 않음
print(b)
b = "abc"         # b를 수정
print(b)
print(a)

>>>
10
"abc"
10                # a는 수정되지 않음
```

- Shallow copy
  - 단숫 객체 복제/shallow copy의 차이점:
    - 복합 객체(`list`)는 별도 생성
    - 내용물은 원본 객체의 내용물을 참조
    - 슬라이싱도 Shallow copy이므로 `mutable`한 item에서는 문제가 발생

```python
import copy

a = [1, [1, 2, 3]]
b = copy.copy(a)        # shallow copy
print(b)
b[0] = 10
print(b)
print(a)

>>>
[1, [1, 2, 3]]
[10, [1, 2, 3]]
[1, [1, 2, 3]]          # b[0]은 immutable하기 때문에 원본/사본의 item은 서로 다른 객체
```

```python
import copy

a = [1, [1, 2, 3]]
c = copy.copy(a)        # shallow copy
c[1].append(4)          # list의 두 번째 item에 4를 추가
print(c)
print(a)

>>>
[1, [1, 2, 3, 4]]
[1, [1, 2, 3, 4]]       # c[1]은 mutable하기 때문에 원본/사본의 item이 같은 객체를 참조
```

```python
a = [[1, 2], [3, 4]]
b = a[:]
assert a is not b
assert a[0] is b[0]
```

- Deep copy
  - `mutable`한 내부 객체의 shallow copy 문제를 해결
  - 복합 객체(`list`) 및 내용물 전부 새로 생성

```python
import copy1
a = [1, [1, 2, 3]]
b = copy.deepcopy(a)    # deep copy
print(b)
b[0] = 10
b[1].append(4)
print(b)
print(a)

>>>
[10, [1, 2, 3, 4]]
[1, [1, 2, 3]]
```

