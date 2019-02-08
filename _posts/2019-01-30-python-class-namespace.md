---
layout: post
use_math: true
title: "[Python] 클래스 네임스페이스 개념 정리"
date: 2019-01-30 19:07:51
tagline: "파이썬의 클래스 내부의 지역 네임스페이스 및 외부 지역 네임스페이스에서의 클래스 이름에 대한 개념 정리"
categories:
- Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-30-python-class-namespace
---

지난번 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에 이어서 이번에는 파이썬의 클래스 네임스페이스에 대한 개념에 대해서 정리해보았다. 이번 포스팅은 다음의 링크를 참고하여 작성하였다:
- [링크1](https://wikidocs.net/1743)

## 파이썬의 네임스페이스 확인
파이썬에서 현재 스코프의 네임스페이스를 확인하는 방법으로는 `locals` 메서드를 사용하는 것을 이전 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에서 설명했던 것 같다. 한편 `locals` 메서드는 딕셔너리 형태의 네임스페이스를 복사해서 반환하는 메서드인데 여기서 네임스페이스의 Key인 `이름`들만을 반환하는 `dir` 메서드가 있다. 이전 [포스팅](https://hcnoh.github.io/2019-01-30-python-namespace)에서 사용했던 예제들을 이용하여 `dir`을 사용하는 예제를 살펴보도록 하자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
    
    inner_func()

a = 10
outer_func()
print(locals())
print(dir())

>>>
{'__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7fa44f28bc18>, '__spec__': None, 'outer_func': <function outer_func at 0x7fa44f2c2f28>, '__name__': '__main__', '__package__': None, '__file__': 'namespace_example01.py', '__cached__': None, '__builtins__': <module 'builtins' (built-in)>, 'a': 10, '__doc__': None}                 
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'a', 'outer_func']
```

`locals` 메서드는 네임스페이스의 딕셔너리를 전부 반환하며 Key는 이름, Value는 객체로 구성되어 있는 것을 확인할 수 있다. 또한 `dir` 메서드는 네임스페이스의 이름들을 요소로 가진 리스트를 복사해서 반환하게 되는 것을 확인할 수 있다.

## 파이썬 클래스의 네임스페이스
파이썬의 클래스는 모듈과 마찬가지로 각각의 네임스페이스를 갖는다. 
