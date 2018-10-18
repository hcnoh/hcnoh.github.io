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

- 이 클래스를 이용: `Point2D`처럼 간단한 불변 자료 구조를 문자열로 쉽게 직렬화

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


