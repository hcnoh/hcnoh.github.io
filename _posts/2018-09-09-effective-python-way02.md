---
layout: post
title: "[Effective Python] WAY 2. PEP 8 스타일 가이드를 따르자"
date: 2018-09-09 20:25:48
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-09-effective-python-way02
---

이번 포스팅은 다음의 링크를 참고하여 작성하였다.
- [출처 1](https://hashcode.co.kr/questions/963/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%97%90%EC%84%9C-%EB%93%A4%EC%97%AC%EC%93%B0%EA%B8%B0%EB%8A%94-%ED%83%AD-%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4-%EC%A4%91-%EC%96%B4%EB%8A%90%EA%B1%B8%EB%A1%9C-%ED%95%98%EC%8B%9C%EB%82%98%EC%9A%94)
- [출처 2](http://paphopu.tistory.com/30)

## PEP 8이란?
- 파이썬 개선 제안서 (Python Enhancement Proposal) #8
- 파이썬 코드를 어떻게 구성할지 알려주는 스타일 가이드
- 유지보수, 가독성을 위해서 일관성있는 스타일을 지키는 것이 중요

## PEP 8의 규칙 예시: 화이트 스페이스
화이트 스페이스는 공백이라고도 한다. 파이썬에서 공백은 문법적으로 의미가 있기 때문에 주의해야 한다.
- 탭이 아닌 스페이스로 들여 쓴다.
  - 들여쓰기 수준은 스페이스 4개
  - IDE(파이참 또는 잘 설정된 vim)에서는 탭이 스페이스 4개로 설정되어 있는 것임
- 한 줄에 79자 이하
- 길어서 다음 줄로 이어지면 들여쓰기 수준을 위해 스페이스 4개 사용
- 파일 내에서 함수/클래스는 빈 줄 두 개로 구분
- 클래스 내에서 메서드 사이는 빈 줄 하나로 구분
- 리스트 인덱스, 함수 호출, 키워드 인수 할당에는 스페이스를 사용하지 않음

```python
b = a[3:5]    # 리스트 인덱스에는 스페이스를 사용하지 않음
b = a[3: 5]   # 틀린 예시
```

- 변수 할당 앞뒤에 스페이스 하나만 사용:

```python
a = b     # "=" 앞뒤로 스페이스 하나만 사용
a=b       # 틀린 예시
```

## PEP 8의 규칙 예시: 명명(Naming)
이 스타일을 따르면 코드를 읽을 때 각 이름에 대응하는 타입을 구별하기 쉬움
- 함수, 변수, 속성은 lowercase_underscore 형식
- Protected 인스턴스 속성은 _leading_underscore 형식
- Private 인스턴스 속성은 __double_leading_underscore 형식
- 클래스와 예외는 CapitalizedWord 형식
- 모듈 수준의 상수는 ALL_CAPS 형식
- 클래스의 인스턴스 메서드의 첫 번째 파라미터의 이름은 self
- 클래스 메서드의 첫 번째 파라미터의 이름은 cls

## PEP 8의 규칙 예시: 표현식과 문장
파이썬의 계명 (The Zen of Python) "어떤 일을 하는 확실한 방법이 (될 수 있으면 하나만) 있어야 한다.", 표현식과 문장의 본보기로 이 스타일을 정리
- 긍정 표현식의 부정(if not a is b) 대신에 인라인 부정(if a is not b)
- 길이를 확인(`if len(somelist) == 0`)하여 빈 값(`[]` 또는 `‘’`)을 확인하지 않는다. `If not somelist`를 사용하고, 빈 값은 암시적으로 `False`가 된다. 비어있지 않으면 `if somelist` 문이 암시적으로 `True`가 된다.
- 한 줄로 된 `if` 문, `for`와 `while` 루프, `except` 복합문을 쓰지 않는다. 여러 줄로 나눠서 명료하게 작성

```python
for i in range(10): print("hello world")    # 틀린 예시
for i in range(10):
    print("hello world")                    # 옳은 예시
end
```

- 항상 파일 맨 위에 `import` 문
- 모듈 임포트의 경우, 항상 모듈의 절대 이름을 사용하며 현재 모듈의 경로를 기준으로 상대 경로로 된 이름을 사용하지 않는다. 예를 들면 `bar` 패키지의 `foo` 모듈을 임포트하려면 그냥 `import foo`가 아니라 `from bar import foo`라고 해야 함
- 상대적인 임포트를 해야 한다면 명시적인 구문을 이용하여 `from . import foo`라고 한다. (절대적 임포트 vs 상대적 임포트?)
- 임포트는 섹션 순으로 구분해야 함 (표준 라이브러리 모듈 / 서드파티 모듈 / 자신이 만든 모듈), 섹션 내에서는 알파벳 순서로 임포트 (각각의 차이?)

## 파이썬의 메서드 종류
- 인스턴스 메서드:
  - 클래스 내부에 정의되어 있는 함수를 호출할 때, 인스턴스를 필요로 한다는 조건
  - 즉, 인스턴스 메서드는 호출하기 위해 인스턴스의 선언을 요구함
  - 첫 번째 파라미터는 항상 self
- 인스턴스 메서드 예제:

```python
class InstMethod:

    def __init__(self, name):
        self.name = name
    
    def print_name(self):
        print("my name is " + self.name)

# 인스턴스 선언
name_instance = InstMethod("hyungcheol_noh")

# print_name이라는 함수를 호출하기 위해서는 name_instance처럼 인스턴스를 먼저 선언해야 함
name_instance.print_name()
>>> my name is hyungcheol_noh
```

- 클래스 메서드:
  - 인스턴스 메서드와 다르게 자기 자신을 첫 번째 파라미터로 받음
  - `@classmethod` 데코레이터를 이용하여 선언
  - self가 아닌 cls를 매개변수로 사용
- 클래스 메서드 예제:

```python
class ClassMethod:
    
    __name = "hyungcheol_noh"

    @classmethod
    def print_name(cls):
        print("my name is " + cls.__name)

# 인스턴스를 따로 선언해줄 필요없이 함수를 호출할 수 있음
ClassMethod.print_name()
>>> my name is hyungcheol_noh
```

- 스테이틱 메서드:
  - 인스턴스나 클래스를 인자로 받지 않음
  - 클래스 네임스페이스 안에 저장
  - 클래스/인스턴스 모두를 통해서 호출이 가능
  - `@staticmethod` 데코레이터를 이용하여 선언
- 스테이틱 메서드 예제:

```python
class StaticMethod:

    @staticmethod
    def print_name(name):
        print("my name is " + name)

# 클래스를 통해서 호출 가능
StaticMethod.print_name("hyungcheol_noh")
>>> my name is hyungcheol_noh

# 인스턴스를 통해서도 호출이 가능
me = StaticMethod()
me.print_name("hyungcheol_noh")
>>> my name is hyungcheol_noh
```

