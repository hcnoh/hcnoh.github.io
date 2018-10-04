---
layout: post
title: "WAY 24. 객체를 범용으로 생성하려면 @classmethod 다형성을 이용하자"
date: 2018-10-04 17:31:32
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-04-effective-python-way24
---

## 다형성
- 파이썬은 객체/클래스가 다형성을 지원
- 다형성이란?
  - 계층 구조에 속한 여러 클래스가 자체의 메서드를 독립적인 버전으로 구현하는 방식
- 맵리듀스(MapReduce) 구현
  - 입력 데이터를 표현할 공통 클래스 필요

```python
class InputData(object):          # 서브클래스에서 정의해야 하는 read 메서드가 있는 입력 데이터 클래스
    def read(self):
        raise NotImplementedError

class PathInputData(InputData):   # 디스크에 있는 파일에서 데이터를 읽어오도록 구현한 InputData의 서브클래스
    def __init__(self, path):
        super().__init__()
        self.path = path
    
    def read(self):
        return open(self.path).read()
```
