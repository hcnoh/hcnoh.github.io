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
위의 첫 번째 예시인 `some_func`의 방법처럼 매개변수 `args`앞에 \*가 1개가 붙어있는 경우에는 매개변수 `args`를 가변적인 갯수를 가진 위치인수로 하겠다는 의미이다. 아래의 예시를 살펴보자.

```python
def print_args(*args):
    print("Positional:", args)

print_args(1, 2)

>>> Positional: 1, 2
```

위와 같이 매개변수를 가변 갯수의 위치변수로 설정하였기 때문에 임의의 변수들인 `1, 2`를 매개변수로 입력해줘도 잘 동작하는 것을 확인할 수 있다. 함수를 정의할 때 매개변수를 가변적으로 그리고 따로 이름을 가지지 않은 위치변수로 정의하고 싶다면 \*을 한개 사용하여 매개변수를 정의하면 간단할 것이다. 예컨대 아래와 같은 활용도 가능하다.

```python
def get_summation(*args):
    return sum(args)

# 이 부분 확인 필요
```

## 함수의 매개변수 정의 앞에 \*이 2개 붙어있는 경우

## 함수의 매개변수로 리스트 및 딕셔너리를 활용하는 방법: \*을 활용

