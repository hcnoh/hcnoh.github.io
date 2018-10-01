---
layout: post
title: "WAY 3. bytes, str, unicode의 차이점을 알자"
date: 2018-09-10 10:15:52
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-09-effective-python-way03
---

파이썬에서 문자 시퀀스를 나타내는 타입은 2가지 방식이 있다. 로 8비트(raw 8 bits) 값과 유니코드(Unicode) 문자가 그 2가지 방식이다. 파이썬3과 파이썬2에서는 각각 다른 방식을 사용하는데 그 방식의 차이를 정리해보도록 하자.

- 파이썬3에서 문자 시퀀스를 나타내는 방법
    - bytes 인스턴스: 로 8비트 값
    - str 인스턴스: 유니코드 문자

- 파이썬2에서 문자 시퀀스를 나타내는 방법
    - str 인스턴스: 로 8비트 값
    - unicode 인스턴스: 유니코드 문자

유니코드 문자를 로 8비트 값으로 변환하는 과정을 인코딩(encoding)이라고 한다. 가장 일반적으로 사용되는 인코딩 방식은 `utf-8`이다. 또한 인코딩의 반대 과정을 디코딩(decoding)이라고 한다. 주의할 점은 파이썬3의 str 인스턴스와 파이썬2의 unicode 인스턴스는 연관된 바이너리 인코딩이 따로 없다는 점이다. 파이썬 스크립트 상에서는 encode 메서드와 decode 메서드를 이용하여 인코딩/디코딩이 가능하다.
- 인코딩 (encode 메서드): 유니코드 문자 => 로 8비트 값
- 디코딩 (decode 메서드): 로 8비트 값 => 유니코드 문자

파이썬 프로그래밍을 할 때 스크립트 내부에서는 유니코드 문자 타입(파이썬3의 str/파이썬2의 unicode)을 사용하고, 인코딩에 관해서는 어떤 가정도 있어서는 안된다. 즉 유니코드 문자 타입으로 일관성있게 스크립트를 작성하라는 말. 이렇게 구현해놓아야 외부로 텍스트를 내보내거나 외부로부터 받아들일때 다른 텍스트 인코딩을 쉽게 수용 가능하다.
- 파이썬 스크립트 내부에서는 유니코드 문자 타입만을 사용

파이썬의 두 가지 문자 타입으로 인하여 다음의 두 가지 문제가 생길 수 있다.
- 인코딩된 로 8비트 값을 처리하려는 상황
- 인코딩이 없는 유니코드 문자를 처리하려는 상황

이 두 경우에서 서로간의 변환 및 입력값과 스크립트 내부에서 사용되는 타입이 일치하게 하기 위해서는 `로 8비트 값 <=> 유니코드 문자`로의 변환이 가능한 헬퍼 함수가 필요하다.

- 파이썬 3: str, bytes => bytes

```python
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode("utf-8")
    else:
        value = bytes_or_str
    return value  # str 인스턴스
```

- 파이썬 3: str, bytes => str

```python
def to_bytes(bytes_or_str):
    if isinstance(bytes_r_str, str):
        value = bytes_or_str.encode("utf-8")
    else:
        value = bytes_or_str
    return value  # bytes 인스턴스
```

- 파이썬 2: unicode, str => unicode

```python
def to_unicode(unicode_or_str):
    if isinstance(unicode_or_str, str):
        value = unicode_or_str.decode("utf-8")
    else:
        value = unicode_or_str
    return value  # unicode 인스턴스
```

- 파이썬 2: unicode, str => str

```python
def to_str(unicode_or_str):
    if isinstance(unicode_or_str, unicode):
        value = unicode_or_str.encode("utf-8")
    else:
        value = unicode_or_str
    return value  # str 인스턴스
```

파이썬에서 문자 시퀀스를 처리할 때 중대한 이슈 두 가지가 있다.

먼저 파이썬 2에서 str이 7비트 아스키 문자만 포함하고 있는 경우에는 unicode와 str 인스턴스가 같은 타입처럼 보인다. 즉, 다음과 같은 특징을 갖게 된다.
- 이런 str과 unicode를 + 연산자로 묶을 수 있다.
- `==`/ `!=` 연산자로 str과 unicode를 비교할 수 있다.
- `%s` 같은 포맷 문자열에 unicode 인스턴스를 사용할 수 있다.

이 의미를 정리하면 다음과 같다.
- 파이썬 2에서 7비트 아스키만 처리하는 경우: str 또는 unicode를 받는 함수에 str이나 unicode 인스턴스를 넘겨도 동작
- 파이썬 3에서는 bytes/str 인스턴스는 같지 않으니 주의해야 함

두 번째 이슈는 파이썬 내장 함수 open이 반환하는 파일 핸들을 사용하는 연산의 기본 인코딩 방식이다.
- 파이썬 2에서는 바이너리 인코딩(로 8비트 값) 사용
- 파이썬 3에서는 `utf-8` 인코딩 사용

다음의 코드는 파이썬 2에서는 잘 동작하지만, 파이썬 3에서는 동작하지 않는다.

```python
with open("/tmp/random.bin", "w") as f:
    f.write(os.urandom(10))

>>>
TypeError: must be str, not bytes
```

에러의 원인은 파이썬 3의 open은 encoding이라는 파라미터를 받아야 하기 때문이다. 파이썬 2와는 다르게 파이썬 3의 open은 바이너리가 아닌 인코딩 값을 받기 때문에 인코딩 방법을 명시해줘야 하기 때문이다. 이 코드가 동작하기 위해서는 다음처럼 수정해주면 된다.

```python
with open("/tmp/random.bin", "wb) as f:
    f.write(os.urandom(10))
```

즉 위처럼 문자 쓰기 모드 `w`가 아니라 바이너리 쓰기 모드인 `wb`로 설정해주면 파이썬 2에서처럼 잘 동작한다는 것을 확인할 수 있다. 읽기 모드에서도 마찬가지로 `r`, `rb`가 사용된다.

## 바이너리 vs 유니코드
- 추가 예정

[Go to the Home Page]({{ site.url }}{{ site.baseurl }})
