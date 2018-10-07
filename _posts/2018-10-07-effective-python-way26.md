---
layout: post
title: "WAY 26. 믹스인 유틸리티 클래스에만 다중 상속을 사용하자"
date: 2018-10-07 21:05:12
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
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
- 동적 조사(`dynamic inspection`)을 이용
  - 많은 클래스에 적용할 수 있는 범용 기능을 믹스인에 한 번만 작성하면 됨
- 믹스인을 조합하고 계층으로 구성하면 반복 코드를 최소화하고 재사용성을 극대화할 수 있음
- 믹스인 예제
  - 파이썬 객체를 메모리 내부 표현에서 직렬화(`serialization`)용 딕셔너리로 변환하는 기능이 필요
  - 이 기능을 모든 클래스에서 사용할 수 있게 범용으로 작성하기를 원함

```python
class ToDictMixin(object):
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
