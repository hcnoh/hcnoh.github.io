---
layout: post
use_math: true
title: "Python 매개변수 앞의 *(Asterisk)의 의미"
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

## 매개변수 앞에 \*이 1개 붙어있는 경우
위의 첫 번째 예시인 `some_func`의 방법처럼 매개변수 `args`앞에 \*가 1개가 붙어있는 경우에는 매개변수 `args`를 가변적인 갯수를 가진 위치인수로 하겠다는 의미이다. 아래의 예시를 살펴보자.

```python
def print_args(*args):
    print("Positional:", args)

print_args(1, 2)

>>> Positional: 1, 2
```

