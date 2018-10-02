---
layout: post
title: "WAY 10. range보다는 enumerate를 사용하자"
date: 2018-09-17 21:51:32
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-17-effective-python-way10
---

종종 리스트를 순회하거나 리스트의 현재 아이템의 인덱스를 알고 싶은 경우가 있다. 이 경우에 `range`를 사용할 수도 있지만 파이썬에서는 더욱 간편한 `enumerate`를 제공한다.

## range를 사용하는 예제

```python
for i in range(len(flavor_list)):
    flavor = flavor_list[i]
    print("%d: %s" % (i + 1, flavor))
```

- 위의 코드는 세련되지 못한 모습임
- `enumerate`를 이용하면 훨씬 간편

## enumerate란?
- 지연 제너레이터(lazy generator)로 이터레이터를 감싸안음
- 이 제너레이터는 루프 인덱스와 다음 값을 한 쌍으로 가져와 넘겨줌
- `enumerate` 예제

```python
for i, flavor in enumerate(flavor_list)
    print("%d: %s" % (i + 1, flavor))
>>>
1: vanilla
2: chocolate
3: pecan
4: strawberry
```

- `enumerate`로 세기 시작할 숫자를 지정할 수도 있음

```python
for i, flavor in enumerate(flavor_list, 1)
    print("%d: %s" % (i, flavor))
>>>
1: vanilla
2: chocolate
3: pecan
4: strawberry
```

