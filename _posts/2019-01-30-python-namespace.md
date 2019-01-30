---
layout: post
use_math: true
title: "Python에서 네임스페이스의 의미"
date: 2019-01-30 10:14:05
tagline: "파이썬에서 변수를 구분하는 네임스페이스의 의미 및 역할 정리"
categories:
- Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-30-python-namespace
---

파이썬에서는 보통 메인 모듈의 구성이 다음과 같이 되어있다.

```python
def main():
    #...


if __name__ == '__main__':
    main()


```
