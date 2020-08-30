---
layout: post
title: "[Effective Python] WAY 24. 객체를 범용으로 생성하려면 @classmethod 다형성을 이용하자"
date: 2018-10-04 17:31:32
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-04-effective-python-way24
---

## 다형성
- 파이썬은 객체/클래스가 다형성을 지원
- 다형성이란?
  - 계층 구조에 속한 여러 클래스가 자체의 메서드를 독립적인 버전으로 구현하는 방식

## 다형성의 필요성
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
  - 무엇으로 객체를 만들고 맵리듀스를 조율?

- 헬퍼 함수로 직접 객체를 만들고 연결하는 방법
  - 디렉토리의 내용을 나열하고 그 안에 있는 각 파일로 `PathInputData` 인스턴스를 생성하는 코드

```python
def generate_inputs(data_dir):
    for name in os.listdir(data_dir):
        yield PathInputData(os.path.join(data_dir, name))
```

- `generate_inputs` 함수에서 반환한 `InputData` 인스턴스를 사용하는 `LineCountWorker` 인스턴스 생성

```python
def create_workers(input_list):
    workers = []
    for input_data in input_list:
        workers.append(LineCountWorker(input_data))
    return workers
```

- 그 다음, `map` 단계를 여러 스레드로 나눠서 이 `Worker` 인스턴스들을 실행
- 그런 다음 `reduce`를 반복적으로 호출하여 결과를 최종값 하나로 합침

```python
def execute(workers):
    threads = [Thread(target=w.map) for w in workers]
    for thread in threads: thread.start()
    for thread in threads: thread.join()
    
    first, rest = workers[0], workers[1:]
    for worker in rest:
        first.reduce(worker)
    return first.result
```

- 마지막으로 단계별로 실행하려고 `mapreduce` 함수에서 모든 조각을 연결

```python
def mapreduce(data_dir):
    inputs = generate_inputs(data_dir)
    workers = create_workers(inputs)
    return execute(workers)
```

- 위의 과정들을 테스트하기 위한 코드

```python
from tempfile import TemporaryDirectory

def write_test_files(tmpdir):
    # ...

with TemporaryDirectory() as tmpdir
    write_test_files(tmpdir)
    result = mapreduce(tmpdir)

print("There are", result, "lines")

>>>
There are 4360 lines
```

>- 위의 과정에는 문제가 몇 가지 있음
>  - `mapreduce` 함수가 전혀 범용적이지 않음
>    - 다른 `InputData`나 `Worker` 서브클래스를 작성한다면 `generate_inputs`, `create_workers`, `mapreduce` 함수를 알맞게 다시 작성해야 함

- 즉, 객체를 생성하는 범용적인 방법이 필요
  - 다른 언어에서는 생성자 다형성을 이용할 수 있음
    - `InputData` 서브클래스에서 특별한 생성자를 제공 => 맵리듀스를 조율하는 헬퍼 메서드가 범용적으로 사용할 수 있게함
    - 파이썬은 단일 생성자 메서드만을 제공하기 때문에 이 방법을 사용할 수 없음

## 클래스 메서드 다형성
>- 위의 문제들을 해결하기 위한 가장 좋은 방법은 `@classmethod` 다형성을 이용하는 것
- `@classmethod` 다형성?
  - `InputData.read`에 사용한 인스턴스 메서드 다형성과 거의 같음
  - 하지만 `@classmethod` 다형성은 생성된 객체만이 아니라 전체 클래스에 적용됨
- 맵리듀스 예제에 적용

```python
class GenericInputData(object):
    def read(self):
        raise NotImplementedError
        
    @classmethod
    def generate_inputs(cls, config):     # GenericInputData를 구현하는 서브클래스가 해석할 설정 파라미터들을 담은 딕셔너리를 입력 받음
        raise NotImplementedError         # 입력 파일들을 얻어올 디렉터리를 config로 알아냄
```

```python
class PathInputData(GenericInputData):
  # ...
  def read(self):
      return open(self.path).read()
  
  @classmethod
  def generate_inputs(cls, config):
      data_dir = config["data_dir"]
      for name in os.listdir(data_dir):
          yield cls(os.path.join(data_dir, name))   # PathInputData 클래스 인스턴스 생성
```

- 마찬가지의 방법으로 `GenericWorker` 클래스에 `create_workers` 헬퍼를 작성


```python
class GenericWorker(object):
    # ...
    def map(self):
        raise NotImplementedError
    
    def reduce(self, other):
        raise NotImplementedError
    
    @classmethod
    def create_workers(cls, input_class, config):
        workers = []
        for input_data in input_class.generate_inputs(config)   # 클래스 다형성
            workers.append(cls(input_data))                     # create_workers가 __init__ 메서드를 직접 사용하지 않고 GenericWorker를 생성 => cls를 호출함으로써 가능
        return workers

class LineCountWorker(GenericWorker):
    # ...
```

- `mapreduce` 함수를 완전히 범용적으로 재작성

```python
def mapreduce(worker_class, input_class, config):               # 범용적으로 동작하기 위해 이전보다 더 많은 파라미터를 요구함
    workers = worker_class.create_workers(input_class, config)
    return execute(workers)

with TemporaryDirectory() as tmpdir:
    write_test_files(tmpdir)
    config = {"data_dir": tmpdir}
    result = mapreduce(LineCountWorker, PathInputData, config)
```

- 이제 `GenericInputData`와 `GenericWorker`의 다른 서브클래스를 원하는 대로 만들어도 글루 코드(glue code)를 작성할 필요가 없음

