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

- 위의 예제에서 `PathInputData`같은 `InputData`의 서브클래스는 몇 개든 있을 수 있음
  - 따라서 각 서브클래스에서는 처리할 바이트 데이터를 반환하는 표준 인터페이스인 `read`를 구현할 것임
  
- 맵리듀스 작업 클래스

```python
class Worker(object):
    def __init__(self, input_data)
        self.input_data = input_data
        self.result = None
    
    def map(self):
        raise NotImplementedError
        
    def reduce(self, other):
        raise NotImplementedError

```

- 다음은 적용하려는 특정 맵리듀스 함수를 구현한 `Worker`의 구체 서브클래스

```python
class LineCountWorker(Worker):
    def map(self):
        data = self.input_data.read()
        self.result = data.count("\n")
    
    def reduce(self, other):
        self.result += other.result
```

- 이런 식으로 구현을 한다면 잘 동작할 듯 해도 문제점이 발생
  - 적절히 인터페이스를 설계하고 추상화한 클래스
  - 하지만 객체를 생성한 후에나 유용
  - 무엇으로 객체를 만들고 맵리듀스를 
