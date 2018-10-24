---
layout: post
title: "WAY 43. 재사용 가능한 try/finally 동작을 만들려면 contextlib와 with 문을 고려하자"
date: 2018-10-24 19:40:02
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-24-effective-python-way43
---

## with 문의 사용
- 코드를 특별한 컨텍스트에서 실행함을 나타냄
  - 예를 들어: 상호 배제 잠금을 사용하여 잠금이 설정되어 있는 동안만 들여 쓴 코드를 실행함을 나타냄

```python
lock = Lock()
with loc:
    print("Lock is held")
```

- `Lock` 클래스가 `with` 문을 제대로 지원 => 위의 코드는 다음의 `try`/`finally` 구문에 상응

```python
lock.acquire()
try:
    print("Lock is held")
finally:
    lock.release()
```

