---
layout: post
title: "WAY 28. 커스텀 컨테이너 타입은 collections.abc의 클래스를 상속받게 만들자"
date: 2018-10-09 09:47:31
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-09-effective-python-way28
---

## 컨테이너
- 파이썬 프로그래밍의 대부분: 데이터를 담은 클래스들을 정의, 이 객체들이 연계되는 방법을 명시
- 모든 파이썬 클래스는 일종의 컨테이너:
  - 속성/기능을 함께 캡슐화
- 파이썬은 데이터 관리용 `내장 컨테이너 타입(리스트, 튜플, 세트, 딕셔너리)`도 제공

## 내장 컨테이너 타입 상속
- 시퀀스(`sequence`)처럼 쓰임새가 간단한 클래스를 설계할 때는 파이썬의 내장 `list` 타입에서 상속받으려고 하는 것이 당연
  - 멤버의 빈도를 세는 메서드를 추가로 갖춘 커스텀 리스트 타입 예제
  
```python
class FrequencyList(list):
    def __init__(self, members):
        super().__init__(members)
        
    def frequency(self):
        counts = {}
        for item in self:
            counts.setdefault(item, 0)
            counts[item] += 1
        return counts
```

- `FrequencyList`는 `list`의 표준 기능을 모두 갖춰서 파이썬 프로그래머에게 익숙한 시맨틱(`semantic`)을 유지
- 또한 추가한 메서드로 필요한 커스텀 동작을 더할 수 있음

```python
foo = FrequencyList(["a", "b", "a", "c", "b", "a", "d"])
print("Length is", len(foo))
foo.pop()
print("After pop:", repr(foo))
print("Frequency:", foo.frequency())

>>>
Length is 7
After pop: ["a", "b", "a", "c", "b", "a"]
Frequency: {"a": 3, "c": 1, "b": 2}
```

## 시퀀스 시맨틱을 갖는 커스텀 컨테이너의 정의
- `list`의 서브클래스는 아니지만 인덱스로 접근할 수 있게 해서 `list`처럼 보이는 객체를 제공하고 싶은 경우
  - 바이너리 트리 클래스에 (`list`나 `tuple` 같은) 시퀀스 시맨틱을 제공

```python
class BinaryNode(object):
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right
```

- 이 클래스가 시퀀스 타입처럼 동작하게 하려면?
  - 파이썬은 특별한 이름을 붙인 인스턴스 메서드로 컨테이너 동작을 구현

```python
bar = [1, 2, 3]
bar[0]
```

- 위와 같이 시퀀스의 아이템을 인덱스로 접근하면 다음과 같이 해석:

```python
bar.__getitem__(0)
```

- `BinaryNode` 클래스가 시퀀스처럼 동작하게 하려면:
  - 객체의 트리를 깊이 우선으로 탐색하는 `__getitem__`을 구현하면 됨

```python
class IndexableNode(BinaryNode):
    def _search(self, count, index):
        # ...
        # (found, count) 반환
        
    def __getitem__(self, index):
        found, _ = self._search(0, index)
        if not found:
            raise IndexError("Index out of range")
        return found.value
```

- 이 바이너리 트리는 평소처럼 생성하면 됨

```python
tree = IndexableNode(
    10,
    left=IndexableNode(
        5,
        left=IndexableNode(2),
        right=IndexableNode(
            6, right=IndexableNode(7))),
    right=IndexableNode(
        15, left=IndexableNode(11)))
```

- 트리 탐색은 물론이고 `list`처럼 접근할 수도 있음

```python
print("LBR =", tree.left.right.right.value)
print("Index 0 =", tree[0])
print("Index 1 =", tree[1])
print("l1 is the tree?", l1 in tree)
print("l7 is the tree?", l7 in tree)
print("Tree is", list(tree))

>>>
LBR = 7
Index 0 = 2
Index 1 = 5
l1 in the tree? True
l7 in the tree? False
Tree is [2, 5, 6, 7, 10 ,11, 15]
```

- 여기에는 이것만을 가지고도 기대하는 시퀀스 시맨틱을 모두 제공하지 못함
  - 예를 들면 `len`

```python
len(tree)

>>>
TypeError: object of type "IndexableNode" has no len()
```

- 내장 함수 `len`을 쓰려면 커스텀 시퀀스 타입에 맞게 구현한 `__len__`이라는 또 다른 특별한 메서드가 필요

```python
class SequenceNode(IndexableNode):
    def __len__(self):
        _, count = self._search(0, None)
        return count

tree = SequenceNode(
    # ...
)

print("Tree has %d nodes" % len(tree))

>>>
Tree has 7 nodes
```

- 아직도 부족함:
  - `list`나 `tuple` 같은 시퀀스 타입에서 기대할 `count`와 `index` 메서드가 빠졌음
  - 즉, 커스텀 컨테이너 타입을 정의하는 일은 보기보다 어려움
  
## collections.abc 모듈을 이용하여 커스텀 컨테이너 정의
- `collections.abc` 모듈?
  - 각 컨테이너 타입에 필요한 일반적인 메서드를 모두 제공하는 추상 기반 클래스들을 정의
  - 이 추상 기반 클래스들에서 상속받아 서브클래스를 만들다가 깜빡 잊고 필수 메서드를 구현하지 않으면, 모듈이 뭔가 잘못되었다고 알려줌

```python
from collections.abc import Sequence

class BadType(Sequence):
    pass

foo = BadType()

>>>
TypeError: Can't instantiate abstract class BadType with abstract methods __getitem__, __len__
```

- 앞에서 다룬 `SequenceNode`처럼 추상 기반 클래스가 요구하는 메서드를 모두 구현:
  - 별도 작업없이 클래스가 `index`, `count` 같은 부가적인 메서들를 모두 제공

```python
class BetterNode(SequenceNode, Sequence):
    pass
    
tree = BetterNode(
    # ...
)

print("Index of 7 is", tree.index(7))
print("Count of 10 is", tree.count(10))

>>>
Index of 7 is 3
Index of 10 is 1
```

- `Set`과 `MutableMapping`처럼 파이썬의 관례에 맞춰 구현해야 하는 특별한 메서드가 많은 더 복잡한 타입을 정의할 때 이런 추상 기반 클래스를 사용하는 이점은 더욱 커짐
