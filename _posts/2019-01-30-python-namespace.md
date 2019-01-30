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

이번 포스팅은 다음의 링크들을 참고하여 작성하였다:
- [링크1](https://hashcode.co.kr/questions/3/if-__name__-__main__%EC%9D%80-%EC%99%9C%EC%93%B0%EB%82%98%EC%9A%94)

파이썬에서는 보통 메인 모듈의 구성이 다음과 같이 되어있다.

```python
def main():
    #...


if __name__ == '__main__':
    main()


```

사실 파이썬은 스크립트 언어이며 다른 컴파일 언어와는 다르게 자동으로 실행되는 메인함수가 없다. 따라서 일단 실행을 하게되면 들여쓰기가 되지 않은 모든 코드(Level 0 코드)를 실행하게 되며, 함수 및 클래스는 정의만 하게되고 실행되지는 않는다.

