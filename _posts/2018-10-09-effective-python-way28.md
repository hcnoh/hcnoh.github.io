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
- 파이썬은 데이터 관리용 내장 컨테이너 타입(리스트, 튜플, 세트, 딕셔너리)도 제공

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
