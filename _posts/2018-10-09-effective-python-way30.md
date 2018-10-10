---
layout: post
title: "WAY 30. 속성을 리팩토링하는 대신 @property를 고려하자"
date: 2018-10-09 21:47:58
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-09-effective-python-way30
---

## @properpty 데코레이터
- `@property` 데코레이터(`decorator`):
  - 더 간결한 방식으로 인스턴스의 속성에 접근할 수 있음
  - 단순 숫자 속성을 즉석에서 계산하는 방식으로 변경:
    - 호출하는 쪽을 변경하지 않고도 기존의 클래스를 사용한 곳이 새로운 동작을 하게 해주므로 매우 유용함
  - 시간이 지나면서 인터페이스를 개선할 때 중요한 임시방편이 됨

- 구멍 난 양동이의 할당량을 일반 파이썬 객체로 구현

```python
class Bucket(object):
    def __init__(self, period):
        self.period_delta = timedelta(seconds=period)
        self.reset_time = datetime.now()
        self.quota = 0
        
    def __repr__(self):
        return "Bucket(quota=%d)" % self.quota
```

- 구멍 난 양동이(leaky bucket) 알고리즘은 양동이를 채울 때마다 할당량이 다음 기간으로 넘어가지 않게 하는 식으로 동작

```python
def fill(bucket, amount):
    now = datetime.now()
    if now - bucket.reset_time > bucket.period_delta:
        bucket.quota = 0
        bucket.reset_time = now
    bucket.quota += amount
```

- 할당량을 소비하는 쪽에서는 매번 사용할 양을 뺄 수 있는지부터 확인해야 함

```python
def deduct(bucket, amount):
    now = datetime.now()
    if now - bucket.reset_time > bucket.period_delta:
        return False
    if bucket.quota - amount < 0:
        return False
    bucket.quota -= amout
    return True
```
