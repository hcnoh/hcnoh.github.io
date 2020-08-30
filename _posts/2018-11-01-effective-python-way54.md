---
layout: post
title: "[Effective Python] WAY 54. 배포 환경을 구성하는 데는 모듈 스코프 코드를 고려하자"
date: 2018-11-01 16:35:36
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-01-effective-python-way54
---

## 제품화
- 작성한 파이썬 프로그램을 사용?
  - 개발 환경 => 제품 환경
- 즉, 다양한 상황에서 신뢰할 만한 프로그램을 만드는 것은 매우 중요함
- 파이썬 프로그램의 제품화:
  - 프로그램을 사용하는 동안 문제가 없게 하는 것
  - 파이썬의 내장 모듈 덕분에 디버깅, 최적화, 실행 시 프로그램의 품질과 성능을 극대화하는 테스트 기능 제공 가능

## 배포 환경/제품 환경
- `deployment environment` / `production environment`
- 프로그램을 실행하는 구성
- 모든 프로그램에는 적어도 하나의 배포 환경과 제품 환경이 있음

>- 프로그램을 작성하는 첫 번째 목적: 제품 환경에서 동작하도록 하는 것!

## 개발 환경
- `development environment`
- 제품 환경과는 많이 다름
- 프로그램을 작성하거나 수정하려면 개발에 사용 중인 컴퓨터에서 프로그램이 동작하도록 해야 함

## 제품 환경에서의 어려움
- Better way 53 "의존성을 분리하고 재현하려면 가상 환경을 사용 하자"
  - `pyenv` 같은 도구 이용 => 모든 환경에서 같은 파이썬 패키지가 설치되도록 보장 가능

>- 하지만 제품 환경은 종종 개발 환경에서 재현하기 어려운 많은 외부 기능을 요구함

- 예시:
  - 프로그램이 웹 서버 컨테이너에서 실행되고 데이터베이스에 접근해야 함
    - 코드를 수정할 때마다 서버 컨테이너를 실행해야 함
    - 데이터베이스를 적절하게 설정해야 함
    - 프로그램에는 접근용 패스워드가 필요함
    - 즉, 한 줄을 변경하더라도 프로그램이 올바르게 동작하는지 검증하는 일을 하려고 한다면 상당한 노력이 필요

- 해결법?
  - 시작할 때 프로그램의 일부를 오버라이드해서 배포 환경에 따라 서로 다른 기능을 제공하는 것
  - 예시: 서로 다른 `__main__` 파일을 두 개 만들어 둠 => 제품용/개발용

```python
# dev_main.py
TESTING = True
import db_connection
db = db_connection.Database()

# prod_main.py
TESTING = False
import db_connection
db = db_connection.Database()
```

- 위의 예시에서 두 파일의 차이점: `TESTING` 상수의 값
  - 프로그램에서 다른 모듈들은 `__main__` 모듈을 임포트하고 `TESTING` 값으로 자체의 속성을 정의하는 방법을 결정

```python
# db_connection.py
import __main__       # __main__ 모듈 임포트

class TestingDatabase(object):
    # ...

class RealDatabase(object):
    # ...

if __main__.TESTING:  # TESTING 값으로 자체의 속성을 정의
    Database = TestingDatabase
else:
    Database = RealDatabase
```

- 위의 예제는 함수나 메서드 내부가 아님 => 모듈 스코프에서 일반 파이썬 코드가 동작
- 모듈 수준에서 `if` 문을 이용하여 모듈의 이름을 정의하는 방법
  - 모듈을 다양한 배포 환경에 알맞게 만들 수 있음
  - 데이터베이스 설정 등이 필요없을 때 재현해야 하는 수고를 덜 수 있음

## 배포 환경이 복잡해지는 경우
- 위 예제의 `TESTING` 같은 파이썬 상수에서 별도의 설정 파일로 옮기는 방안을 고려해야 함
- 내장 모듈 `configparser`와 같은 도구를 이용 => 제품 설정을 코드와 분리하여 관리

## 호스트 플랫폼에 따라 다르게 동작해야 하는 경우
- 모듈의 최상위 구성 요소를 정의하기 전에 `sys` 모듈을 조사

```python
# db_connection.py
import sys

class Win32Database(object):
    # ...

class PosixDatabase(object):
    # ...
    
if sys.platform.startswith("win32"):
    Database = Win32Database
else:
    Database = PosixDatabase
```

- 이와 유사한 방법:
  - `os.environ`에 들어있는 환경 변수를 기반으로 모듈을 정의


