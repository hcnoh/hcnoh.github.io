---
layout: post
title: "[Effective Python] WAY 13. try/except/else/finally에서 각 블록의 장점을 이용하자"
date: 2018-09-20 14:55:45
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-20-effective-python-way13
---

## 파이썬 예외처리 과정
- 4번의 구분되는 시점
  - `try`, `except`, `else`, `finally`
- 각 블록들을 다양하게 조합하면 유용

## finally 블록
- 예외는 전달하고 싶지만, 예외가 발생해도 정리 코드를 실행하고 싶을 때
- `try` 블록이 예외를 일으키건 일으키지 않건 무조건 실행
- 사용 예시: 파일 핸들러를 제대로 종료하는 작업

```python
handle = open("/tmp/random_data.txt") # IOError가 일어날 수 있음
try:
    data = handle.read()  # UnicodeDecodeError가 일어날 수 있음
finally:
    handle.close()        # try 이후에 항상 실행
```

- `read` 메서드에서 발생한 예외는 항상 호출 코드까지 전달
- `close` 메서드 또한 `finally` 블록에서 실행되는 것이 보장
- 파일을 열 때 일어나는 예외는 `finally` 블록에서 처리하지 않아야 하므로 `try` 블록 앞에서 `open`을 호출해야 함

## else 블록
- 어떤 예외를 처리하고 어떤 예외를 전달할지를 명확하게 하고 싶을 때: `try`/`except`/`else`를 사용
- `try` 블록이 예외를 일으키지 않으면 `else` 블록이 실행
- `else` 블록을 사용하면 `try` 블록의 코드를 최소로 줄이고 가독성을 높일 수 있음
- 문자열에서 `JSON` 딕셔너리 데이터를 로드하여 그 안에 든 키 값을 반환하는 예제

```python
def load_json_key(data, key):
    try:
        result_dict = json.loads(data)    # ValueError가 일어날 수 있음
    except ValueError as e:
        raise KeyError from e
    else:
        return result_dict[key]           # KeyError가 일어날 수 있음
```

## 모두 함께 사용하기
- 복합문 하나로 모든 것을 처리하고 싶다면 `try`/`except`/`else`/`finally`를 사용
- 파일에서 수행할 작업 설명을 읽고 처리한 후 즉석에서 파일을 업데이트하는 예제

```python
UNDEFINED = object()

def divide_json(path):
    handle = open(path, "r+")                         # IOError가 일어날 수 있음
    try:                                              # try 블록은 파일을 읽고 처리하는 데 사용
        data = handle.read()                          # UnicodeDecodeError가 일어날 수 있음
        op = json.loads(data)                         # ValueError가 일어날 수 있음
        value = (op["numerator"]/op["denominator"])   # ZeroDivisionError가 일어날 수 있음
    except ZeroDivisionError as e:                    # except 블록은 try 블록에서 일어난 예외를 처리하는 데 사용
        return UNDEFINED
    else:                                             # else 블록은 파일을 즉석에서 업데이트하고 이와 관련된 예외가 전달되게 하는 데 사용
        op["result"] = value
        result = json.dumps(op)
        handle.seek(0)
        handle.write(result)                          # IOError가 일어날 수 있음
        return value
    finally:                                          # finally 블록은 파일 핸들을 정리하는 데 사용
        handle.close()                                # 항상 실행됨
```

