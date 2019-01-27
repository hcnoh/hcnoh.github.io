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

파이썬 코드를 살펴보다 보면 함수의 매개변수가 다음과 같은 형식으로 정의되는 것을 볼 때가 있다.

```python
def some_func(*args):
    return 0


def some_func2(**kwargs):
    return 0


```

## 
