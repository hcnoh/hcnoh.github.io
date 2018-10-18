---
layout: post
title: "WAY 34. 메타클래스로 클래스의 존재를 등록하자"
date: 2018-10-18 15:58:27
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-18-effective-python-way34
---

## 메타클래스의 활용
- 프로그램에 있는 타입을 자동으로 등록
- 등록(`registration`):
  - 간단한 식별자(`identifier`)를 대응하는 클래스에 매핑하는 역방향 조회(`reverse lookup`)를 수행할 때 유용

- 파이썬 객체를 직렬화한 표현을 `JSON`으로 구현하는 예제
  - 객체를 얻어와서 `JSON` 문자열로 변환할 방법이 필요
- 생성자 파라미터를 저장하고 `JSON` 딕셔너리로 변환하는 기반 클래스를 범용적으로 정의

```python
class Serializable(object):
    def __init__(self, *args):
        self.args = args
        
    def serialize(self):
        return json.dumps({"args": self.args})
```

- 이 클래스를 이용: `Point2D`처럼 간단한 불변 자료 구조를 문자열로 쉽게 직렬화: `Point2D` 객체 => `JSON`

```python
class Point2D(Serializable):
    def __init__(self, x, y):
        super().__init__(x, y)
        self.x = x
        self.y = y
        
    def __repr__(self):
        return `Point2D(%d, %d)` % (self.x, self.y)

point = Point2D(5, 3)
print("Object: ", point)
print("Serialized:", point.serialize())

>>>
Object: Point2D(5, 3)
Serialized: {"args": [5, 3]}
```

- 이 `JSON` 문자열을 역직렬화: `JSON` => `Point2D` 객체
- 역직렬화하는 또 다른 클래스 정의

```python
class Deserializable(Serializable):
    @classmethod
    def deserialize(cls, json_data):
        params = json.loads(json_data)
        return cls(*params["args"])

class BetterPoint2D(Deserializable):
    # ...

point = BetterPoint2D(5, 3)
print("Before:", point)
data = point.serialize()
print("Serialized:", data)
after = BetterPoint2D.deserialize(data)
print("After:", after)

>>>
Before: BetterPoint2D(5, 3)
Serialized: {"args": [5, 3]}
After: BetterPoint2D(5, 3)
```

- 이 방법의 문제점?
  - 직렬화된 데이터에 대응하는 타입을 미리 알고 있을 때만 동작
  - 우리가 원하는 것:
    - `JSON`으로 직렬화되는 클래스를 많이 갖추고
    - 그 중 어떤 클래스든 대응하는 파이썬 객체로 역직렬화하는 공통 함수를 하나만 두기를 원함

- 그렇게 만들기 위해서는 직렬화할 객체의 클래스 이름을 `JSON` 데이터에 포함하면 됨

```python
class BetterSerializable(object):
    def __init__(self, *args):
        self.args = args
        
    def serialize(self):
        return json.dumps({"class": self.__class__.__name__,
                           "args": self.args})
    
    def __repr__(self):
        # ...
```

- 그 다음으로 클래스 이름을 해당 클래스의 객체 생성자에 매핑하고 이 매핑을 관리
- 범용 `deserialize` 함수는 `register_class`에 넘긴 클래스가 어떤 것이든 제대로 동작

```python
registry = {}

def register_class(target_class):     # 클래스 이름을 해당 클래스의 객체 생성자에 매핑
    registry[target_class.__name__] = target_class
    
def deserialize(data):
    params = json.loads(data)
    name = params["class"]
    target_class = registry[name]
    return target_class(*params["args"])
```

