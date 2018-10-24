---
layout: post
title: "WAY 43. 재사용 가능한 try/finally 동작을 만들려면 contextlib와 with 문을 고려하자"
date: 2018-10-24 19:40:02
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-24-effective-python-way43
---

## with 문의 사용
- 코드를 특별한 컨텍스트에서 실행함을 나타냄
  - 예를 들어: 상호 배제 잠금을 사용하여 잠금이 설정되어 있는 동안만 들여 쓴 코드를 실행함을 나타냄

```python
lock = Lock()
with lock:
    print("Lock is held")
```

- `Lock` 클래스가 `with` 문을 제대로 지원 => 위의 코드는 다음의 `try`/`finally` 구문에 상응

```python
lock.acquire()
try:
    print("Lock is held")
finally:
    lock.release()
```

- `with`문은 `try`/`finally` 구문과 다르게 반복이 없음 => 사용성이 더 좋음
- 내장 모듈 `contextlib`를 사용하면 객체와 함수를 `with` 문에 사용할 수 있게 만들기 쉬움
- `contextlib` 모듈:
  - 간단한 함수를 `with` 문에 사용할 수 있게 해주는 `contextmanager` 데코레이터를 포함
    - `__enter__`/`__exit__` 메서드를 담은 새 클래스를 정의하는 방법(표준 방법)보다 간편

- 코드의 특정 영역에 더 많은 디버깅 로그를 넣고 싶은 경우?
  - 로그 심각성 수준(`severity level`) 두 개로 로그를 남기는 함수 정의

```python
def my_function():
    logging.debug("Some debug data")
    logging.error("Error log here")
    logging.debug("More debug data")
```

- 이 프로그램의 기본 로그 수준은 `WARNING` => 따라서 함수를 실행하면 오류 메시지만 출력

```python
my_function()

>>>
Error log here
```

- 로그 수준을 임시로 높이고 싶은 경우?
  - 컨텍스트 매니저를 정의
- 컨텍스트 매니저?
  - `with` 블록에서 코드를 실행하기 전에 로그 심각성 수준을 높이고 실행 후에는 다시 낮추는 헬퍼 함수 정의

```python
@contextmanager
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield
    finally:
        logger.setLevel(old_level)
```

- `yield` 표현식 => `with` 블록의 내용이 실행되는 지점
  - `with` 블록에서 일어나는 모든 예외를 `yield` 표현식이 다시 일으킴 => 헬퍼 함수로 처리 가능
- 자세한 동작 원리는 Better way 40 "많은 함수를 동시에 실행하려면 코루틴을 고려하자"를 참고

