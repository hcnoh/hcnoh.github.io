---
layout: post
use_math: true
title: "Python 함수의 매개변수 앞의 *(Asterisk)의 의미"
date: 2019-01-27 22:27:54
tagline: "파이썬의 함수로 넘겨주는 매개변수의 이름 앞에 쓰이는 *의 의미 및 사용법 정리"
categories:
- Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-27-python-arguments-asterisk
---

이번 포스팅은 이전에 작성된 [포스팅](https://hcnoh.github.io/2018-10-03-effective-python-way21)을 참고하여 작성하였다.

파이썬 코드를 살펴보다 보면 함수의 매개변수가 다음과 같은 형식으로 정의되는 것을 볼 때가 있다.

```python
def some_func(*args):
    return 0


def some_func2(**kwargs):
    return 0
```

이번 포스팅에서는 위에서의 매개변수가 의미하는 바가 무엇인지, 그리고 간단한 사용법을 정리하였다.

## 함수의 매개변수 정의 앞에 \*이 1개 붙어있는 경우
위의 첫 번째 예시인 `some_func`의 방법처럼 매개변수 `args`앞에 \*가 1개가 붙어있는 경우에는 매개변수 `args`를 가변적인 갯수를 가진 위치 인수로 정의하겠다는 의미이다. 아래의 예시를 살펴보자.

```python
def print_args(*args):
    print("Positional:", args)
    print("Type:", type(args))

print_args(1, 2)

>>>
Positional: 1, 2
Type: <class 'tuple'>
```

위와 같이 매개변수를 가변 갯수의 위치 인수로 설정하였기 때문에 임의의 변수들인 `1, 2`를 매개변수로 입력해줘도 잘 동작하는 것을 확인할 수 있다. 또한 입력받은 매개변수 `1, 2`는 튜플 형태로 입력되는 것도 추가적으로 확인할 수 있다. 함수를 정의할 때 매개변수를 가변적으로 그리고 따로 이름을 가지지 않은 위치 인수로 정의하고 싶다면 \*을 한개 사용하여 매개변수를 정의하면 간단할 것이다. 예컨대 아래와 같은 활용도 가능하다.

```python
def get_summation(*args):
    return sum(args)

print(get_summation(1, 2, 3, 4))

>>>
10
```

## 함수의 매개변수 정의 앞에 \*이 2개 붙어있는 경우
맨 처음 예시로 들었던 `some_func2`처럼 매개변수 `kwargs` 앞에 \*가 2개가 붙어있는 경우에는 매개변수 `args`를 가변적인 갯수를 가진 키워드 인수로 정의하겠다는 의미이다. 마찬가지로 예시를 들어보자.

```python
def print_kwargs(**kwargs):
    print("Keyword:", kwargs)
    print("Type:", type(kwargs))

print_kwargs(foo=1, bar=2)

>>>
Keyword: {'bar': 2, 'foo': 1}
Type: <class 'dict'>
```

이번에는 위에서 \*이 1개 붙어있는 경우와는 다르게 입력된 `kwargs`가 딕셔너리 형태로 전달되었다는 것을 확인할 수 있다. 딕셔너리 형태로 전달된 키워드 인수 각각을 사용하고 싶다면 함수 내부에서 딕셔너리의 각 요소에 접근하면 될 것이다. 예컨대 다음과 같다.

```python
def get_summation(**kwargs):
    for key, val in kwargs.items():
        print("%s: %d", (key, sum(val)))

get_summation(first_semester=[40, 60, 50], second_semester=[30, 80, 90])

>>>
second_semester: 200
first_semester: 150
```

참고로 파이썬2에서는 키워드 인수 기능이 제공되지 않으며 만약 구현하고 싶으면 다음의 [링크](https://hcnoh.github.io/2018-10-03-effective-python-way21)에서 확인할 수 있듯이 아래처럼 구현하면 된다.

```python
# 파이썬2
def safe_division(number, divisor, **kwargs):
    ignore_overflow = kwargs.pop("ignore_overflow", False)
    ignore_zero_div = kwargs.pop("ignore_zero_division", False)
    if kwargs:
        raise TypeError("Unexpected **kwargs: %r" % kwargs)
    # ...
```

이런 방식으로 키워드 인수의 디폴트 값까지 설정이 가능하게 된다.

```python
safe_division(1, 10)
safe_division(1, 0, ignore_zero_division=True)
safe_division(1, 10**500, ignore_overflow=True)
```

## 함수의 매개변수로 튜플 및 딕셔너리를 활용하는 방법: \*을 활용
이번에는 반대로 정의된 함수에 매개변수로 튜플 및 딕셔너리를 입력하는 방법을 정리해보자. 위에서의 \*의 활용 방식과 매우 유사한데 만약 튜플을 넘기고 싶다면 튜플의 변수명 앞에 \*을 하나 붙이고 함수로 넘기고 딕셔너리의 경우에는 \*을 2개 붙여서 넘기면 된다.

다음은 튜플을 함수의 매개변수로 넘기는 방법에 대한 예제이다.

```python
def some_func(first_val, second_val, third_val):
    print("first:", first_val)
    print("second:", second_val)
    print("third:", third_val)

some_args = 4, "foo", "bar"
print(some_args)

some_func(*some_args)

>>>
(4, 'foo', 'bar')
first: 4
second: foo
third: bar
```

튜플 `some_args`를 정의한 이후에 함수 `some_func`에 \*을 하나 붙여서 `some_func(*some_args)`와 같이 매개변수로 넘겨주면 우리가 예상하던 결과를 얻어낼 수 있다.

마찬가지로 딕셔너리 형태로 넘기는 방법을 생각해볼 수 있다.

```python
some_kwargs = {"first_val": 4, "second_val": "foo", "third_val": "bar"}
some_func(**some_kwargs)

>>> 
first: 4
second: foo
third: bar
```

같은 결과를 얻을 수 있다. 이런 식으로 함수에 입력될 매개변수들을 딕셔너리 또는 튜플 형태로 구현해서 관리하는 방식은 개발자들에게 더 좋은 가독성을 제공할 수 있다는 장점이 있다.
