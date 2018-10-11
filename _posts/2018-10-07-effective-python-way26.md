---
layout: post
title: "WAY 26. 믹스인 유틸리티 클래스에만 다중 상속을 사용하자"
date: 2018-10-07 21:05:12
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-07-effective-python-way26
---

## 파이썬의 다중 상속
- 파이썬은 다중 상속을 다루기 쉽게 하는 기능을 내장한 객체 지향 언어
- 하지만 다중 상속은 아예 안하는 것이 좋음
  - 다중 상속으로 얻는 편리함과 캡슐화가 필요한 경우: 대신 믹스인(`mix-in`)을 작성하는 방안

## 믹스인이란?
- 클래스에서 제공해야 하는 추가적인 메서드만 정의하는 작은 클래스
- 자체의 인스턴스 속성(`attribute`)을 정의하지 않음
- `__init__` 생성자를 호출하도록 요구하지 않음

## 파이썬의 믹스인
- 파이썬에서는 타입과 상관없이 객체의 현재 상태를 간단하게 조사할 수 있어서 믹스인을 쉽게 작성할 수 있음
- 동적 조사(`dynamic inspection`)를 이용
  - 많은 클래스에 적용할 수 있는 범용 기능을 믹스인에 한 번만 작성하면 됨
- 믹스인을 조합하고 계층으로 구성하면 반복 코드를 최소화하고 재사용성을 극대화할 수 있음
- 믹스인 예제
  - 파이썬 객체를 메모리 내부 표현에서 직렬화(`serialization`)용 딕셔너리로 변환하는 기능이 필요
  - 이 기능을 모든 클래스에서 사용할 수 있게 범용으로 작성하기를 원함

```python
class ToDictMixin(object):          # 딕셔너리로 변환하는 기능을 포함하는 믹스인
    def to_dict(self):
        return self._traverse_dict(self.__dict__)
    
    def _traverse_dict(self, instance_dict):
        output = {}
        for key, value in instance_dict.items():
            output[key] = self._traverse(key, value)
        return output
    
    def _traverse(self, key, value):
        if isinstance(value, ToDictMixin):
            return value.to_dict()
        elif isinstance(value, dict):
            return self._traverse_dict(value)
        elif isinstance(value, list):
            return [self._traverse(key, i) for i in value]
        elif hasattr(value, "__dict__"):
            return self._traverse_dict(value.__dict__)
        else:
            return value
```

- 다음은 바이너리 트리(`binary tree`)를 딕셔너리로 표현하려고 믹스인을 사용하는 예제 클래스

```python
class BinaryTree(ToDictMixin):      # 믹스인을 상속받아서 믹스인이 제공하는 추가적인 메서드를 사용할 수 있음
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

tree = BinaryTree(10, left=BinaryTree(7, right=BinaryTree(9)), right=BinaryTree(13, left=BinaryTree(11)))
print(tree.to_dict())               # 믹스인이 제공하는 추가적인 메서드 사용

>>>
{"left": {"left": None, "right": {"left": None, "right": None, "value": 9}, "value": 7}, "right": {"left": {"left": None, "right": None, "value": 11}, "right": None, "value": 13}, "value": 10}
```

## 믹스인의 장점
- 범용 기능을 교체할 수 있게 만듦
  - 필요할 때 동작을 오버라이드 할 수 있음
- 부모 노드에 대한 참조를 저장하는 `BinaryTree`의 서브클래스
  - 이 순환 참조(`circular reference`)는 `ToDictMixin.to_dict`의 기본 구현이 무한 루프에 빠지게 만듦
  
```python
class BinaryTreeWithParent(BinaryTree):
    def __init__(self, value, left=None, right=None, parent=None):
        super().__init__(value, left=left, right=right)
        self.parent = parent
```

- 해결책?
  - `BinaryTreeWithParent` 클래스에서 `ToDictMixin._traverse` 메서드를 오버라이드해서 믹스인이 순환에 빠지지 않도록 필요한 값만 처리하게 하는 것
  - `_traverse` 메서드를 오버라이드해서 부모를 탐색하지 않고 부모의 숫자 값만 꺼내오게 만든 예제

```python
class BinaryTreeWithParent(BinaryTree):
    def __init__(self, value, left=None, right=None, parent=None):
        super().__init__(value, left=left, right=right)
        self.parent = parent
        
    def _traverse(self, key, value):
        if (isinstance(value, BinaryTreeWithParent) and key == "parent"):
            return value.value      # 순환 방지
        else:
            return super()._traverse(key, value)

root = BinaryTreeWithParent(10)
root.left = BinaryTreeWithParent(7, parent=root)
root.left.right = BinaryTreeWithParent(9, parent=root.left)
print(root.to_dict())

>>>
{"left": {"left": None, "parent": 10, "right": {"left": None, "parent": 7, "right": None, "value": 9}, "value": 7}, "parent": None, "right": None, "value": 10}
```
- `BinaryTreeWithParent._traverse`를 정의한 덕분에 `BinaryTreeWithParent` 타입의 속성이 있는 클래스라면 무엇이든 자동으로 `ToDictMixin`으로 동작할 수 있게 됨

```python
class NamedSubTree(ToDictMixin):
    def __init__(self, name, tree_with_parent):
        self.name = name
        self.tree_with_parent = tree_with_parent

my_tree = NamedSubTree("foobar", root.left.right)
print(my_tree.to_dict())      # 무한 루프를 돌지 않음

>>>
{"name": "foobar", "tree_with_parent": {"left": None, "parent": 7, "right": None, "value": 9}}
```

- 믹스인 조합도 가능
  - 어떤 클래스에도 동작하는 범용 `JSON` 직렬화를 제공하는 믹스인이 필요
  - 이 믹스인은 클래스에 `to_dict` 메서드(`ToDictMixin` 클래스에서 제공할 수도 있고 그렇지 않을 수도 있음)가 있다고 가정하고 만들면 됨
  
```python
class JsonMixin(object):
    @classmethod
    def from_json(cls, data):
        kwargs = json.loads(data)
        return cls(**kwargs)
    
    def to_json(self):
        return json.dumps(self.to_dict())
```

- `JsonMixin` 클래스가 어떻게 인스턴스 메서드와 클래스 메서드를 둘 다 정의하는지 주목:
  - 클래스에 `to_dict` 메서드가 있고
  - 해당 클래스의 `__init__` 메서드에서 키워드 인수를 받음
- 이 믹스인을 이용하면 짧은 반복 코드로 `JSON`으로 직렬화하고 `JSON`에서 역직렬화하는 유틸리티 클래스의 계층 구조를 간단하게 생성할 수 있음
  - 데이터센터 토폴로지를 구성하는 부분들을 표현하는 데이터 클래스의 계층 예제

```python
class DatacenterRack(ToDictMixin, JsonMixin):
    def __init__(self, switch=None, machines=None):
        self.switch = Switch(**switch)
        self.machines = [Machine(**kwargs) for kwargs in machines]

class Switch(ToDictMixin, JsonMixin):
    # ...

class Machine(ToDictMixin, JsonMixin):
    # ...

# 데이터가 직렬화와 역직렬화를 통해 원래 상태가 되는지 검증
serialized = """{
    "switch": {"ports": 5, "speed": 1e9},
    "machines": [
        {"cores": 8, "ram": 32e9, "disk": 5e12},
        {"cores": 4, "ram": 16e9, "disk": 1e12},
        {"cores": 2, "ram": 4e9, "disk": 500e9}
    ]
}"""

deserialized = DatacenterRack.from_json(serialized)
roundtrip = deserialized.to_json()
assert json.loads(serialized) == json.loads(roundtrip)
```


