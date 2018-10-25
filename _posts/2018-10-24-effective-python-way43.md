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

- 이제 같은 로깅 함수를 `debug_logging` 컨텍스트에서 호출
  - 이번에는 `with` 블록 안에 있는 디버크 메시지가 모두 화면에 출력
  - 같은 함수를 `with` 블록 내부에서 실행하면 디버깅 메시지가 출력되지 않음

```python
with debug_logging(logging.DEBUG):
    print("Inside:")
    my_function()
print("After:")
my_function()

>>>
Inside:
some debug data
Error log here
More debug data
After:
Error log here
```

## with 타깃 사용
- `with` 문에 전달되는 컨텍스트 매니저에서 객체를 반환할 수도 있음
  - 이 객체는 복합문(`compound statement`)의 `as` 부분에 있는 지역 변수에 할당
  - 이 기능을 통하여 `with` 블록 안에 있는 코드에서 직접 컨텍스트와 상호작용 가능

- 파일에 쓰기를 수행한 후 해당 파일을 항상 올바르게 닫음을 보장하고 싶은 경우:
  - `with` 문에 `open`을 전달하면 됨
    - `open`은 `with`의 `as` 타깃에 파일 핸들을 반환 => `with` 블록이 종료할 때 핸들을 닫음
    - 매번 수동으로 파일 핸들을 여닫는 방법보다 나음

```python
with open("/tmp/my_output.txt", "w") as handle:
    handle.write("This is some data!")
```

- `as` 타깃에 값을 제공할 수 있게 하려면:
  - 컨텍스트 매니저에서 `yield`를 사용하여 값을 넘겨주면 됨

- `Logger` 인스턴스를 가져와서 심각성 수준을 설정한 후 `yield`로 인스턴스를 `as`에 전달하도록 정의한 예

```python
@contextmanager
def log_level(level, name):
    logger = logging.getLogger(name)
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield logger
    finally:
        logger.setLevel(old_level)
```

- `with` 블록에서 로깅 심각성 수준을 충분히 낮게 설정 => `as` 타깃으로 `debug` 같은 로깅 메서드를 호출하면 출력이 나옴
- 기본 프로그램 로거(`logger`)의 기본 로깅 심각성 수준은 `WARNING` => `logging` 모듈을 직접 사용하면 아무것도 출력되지 않음

```python
with log_level(logging.DEBUG, "my-log") as logger:
    logger.debug("This is my message!")
    logging.debug("This will not print")

>>>
this is my message!
```

- `with` 문이 종료된 후에 `my-log`라는 `Logger`의 디버그 로깅 메서드를 호출 => 아무것도 출력되지 않음
  - 이유? 기본 로깅 심각성 수준으로 되돌아갔기 때문
- 물론 오류 로그 메시지는 항상 출력됨

```python
logger = logging.getLogger("my-log")
logger.debug("Debug will not print")
logger.error("Error will print")

>>>
Error will print
```
