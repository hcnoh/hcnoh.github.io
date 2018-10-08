---
layout: post
title: "WAY 4. 복잡한 표현식 대신 헬퍼 함수를 작성하자"
date: 2018-09-11 16:14:35
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-11-effective-python-way04
---

다음 코드는 URL에서 쿼리 문자열을 디코드하는 스크립트이다.

```python
from urllib.parse import parse_qs
my_values = parse_qs("red=5&blue=0%green=", keep_blank_values=True)
print(repr(my_values))

>>>
{"red": ["5"], "green": [""], "blue": ["0"]}
```

각 쿼리 문자열의 파라미터는 정수 값을 표현한다.

다음의 코드를 살펴보자.

```python
print("Red: ", my_values.get("red"))
print("Green: ", my_values.get("green"))
print("Opacity: " my_values.get("opacity"))

>>>
Red: ["5"]
Green: [""]
Opacity: None
```

red와 green 파라미터는 제대로 나오며 opacity 파라미터는 빠져있기 때문에 `None`으로 나오는 것을 알 수 있다.

파라미터가 없거나 비어있으면 기본값으로 0을 할당하게 하면 좋을 것이다. 파이썬의 문법 덕분에 불(boolean) 표현식으로도 아주 쉽게 처리할 수 있다.

```python
red = my_values.get("red", [""])[0] or 0
green = my_values.get("green", [""])[0] or 0
opacity = my_values.get("opacity", [""])[0] or 0
print("Red: ", red)
print("Green: ", green)
print("Opacity, ", opacity)

>>>
Red: "5"
Green: 0
Opacity: 0
```

red의 경우 my_values 딕셔너리 내부에 key가 있으며 value는 멤버 하나(문자열 "5")만 있는 리스트이다. 따라서 암시적으로 이 문자열은 `True`가 되며 red는 or 표현식의 첫 번째 부분을 할당받는다.

green의 경우 my_values 딕셔너리의 value가 멤버 하나(빈 문자열)만 있는 리스트이다. 빈 문자열은 암시적으로 `False`이므로 or 표현식의 결과는 두 번째 부분인 0이 된다.

opacity의 경우는 my_values 딕셔너리에 key가 아예 존재하지 않는다. get 메서드는 key가 딕셔너리에 없으면 두 번째 인수를 반환한다. 이때 반환되는 기본값은 멤버 하나(빈 문자열)만 있는 리스트이다. 따라서 or 표현식의 두 번째 부분인 0을 할당받는다.

위 식을 사용하기 위해서는 문자열 출력을 정수값으로 파싱을 할 필요가 있다. 내장 함수 `int`를 이용하면 정수값으로 파싱이 가능하다.

```python
red = int(my_values.get("red", [""])[0] or 0)
```

위 식은 사실 매우 읽기 어렵다. 이렇게 짧게 짜는게 좋을 수도 있지만 사실 `한 줄에 코든 코드를 집어넣는 것은 큰 의미가 없다`.

파이썬 2.5에 추가된 if/else 조건식(삼항 표현식)을 이용하면 코드를 짧게 유지하면서도 더 명확하게 표현할 수 있다.

```python
red = my_values.get("red", [""])
red = int(red[0]) if red[0] else 0
```

물론 다음과 같이 모든 로직을 펼쳐서 볼 수도 있다. 물론 보기에는 더 복잡해 보인다.

```python
green = my_values.get("green", [""])
if green[0]:
    green = int(green[0])
else:
    green = 0
```

이 로직을 반복해서 사용한다면 헬퍼 함수를 만드는 게 좋다.

```python
def get_first_int(values, key, default=0)
    found = my_values.get(key, [""])
    if found[0]:
        found = int(found[0])
    else:
        found = 0
    return found
```

이렇게 헬퍼 함수를 쓰면 호출 코드가 훨씬 더 명확해진다.

```python
green = get_first_int(my_values, "green")
```

표현식이 복잡해지기 시작하면 최대한 빨리 해당 표현식을 `작은 조각으로 분할하고 로직을 헬퍼 함수로 옮기는 방안`을 고려해야 한다. 무조건 짧은 코드보다 `가독성이 더 중요`하다. 이렇게 복잡한 표현식에는 파이썬의 함축적인 문법이 오히려 가독성을 해치게 된다.


