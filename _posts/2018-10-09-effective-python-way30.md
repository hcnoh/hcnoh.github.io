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

## @properpty 데코레이터의 필요성
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

- 먼저 양동이를 채워봄

```python
bucket = Bucket(60)
fill(bucket, 100)
print(bucket)

>>>
Bucket(quota=100)
```

- 그러고 나서 필요한 양만큼 빼봄

```python
if deduct(bucket, 99):
    print("Had 99 quota")
else:
    print("Not enough fo 99 quota")
print(bucket)

>>>
Had 99 quota
Bucket(quota=1)
```

- 이용할 수 있는 양보다 더 많이 빼려고 시도

```python
if deduct(bucket, 3):
    print("Nad 3 quota")
else:
    print("Not enough for 3 quota")
print(bucket)

>>>
Not enough for 3 quota
Bucket(quota=1)
```

## @properpty 데코레이터를 이용한 구현
- 위와 같은 구현의 문제점:
  - 0까지 도달하면 `deduct`가 항상 `False`를 반환
  - 이때 `deduct`를 호출하는 쪽이 중단된 이유가 `Bucket`의 할당량이 소진되어서인지 아니면 처음부터 `Bucket`에 할당량이 없어서인지 알 수 없음
- 문제를 수정하기 위해서:
  - 클래스에서 기간 동안 발생한 `max_quota`와 `quota_consumed`의 변경을 추적하도록 수정

```python
class Bucket(object):
    def __init__(self, period):
        self.period_delta = timedelta(seconds=period)
        self.reset_time = datetime.now()
        self.max_quota = 0
        self.quota_consumed = 0
    
    def __repr__(self):
        return ("Bucket(max_quota=%d, quota_consumed=%d)" % (self.max_quota, self.quota_consumed))
    
    @property
    def quota(self):    # 새 속성들을 이용하여 실시간으로 현재 할당량의 수준을 계산
        return self.max_quota - self.quota_consumed
    
    @quota.setter       # quota 속성이 할당을 받는 순간에 fill과 deduct에서 사용하는 이 클래스의 현재 인터페이스와 일치하는 특별한 동작을 하게 만듦
    def quota(self, amount):
        delta = self.max_quota - amount
        if amount == 0:
            # 새 기간의 할당량을 리셋함
            self.quota_consumed = 0
            self.max_quota = 0
        elif delta < 0:
            # 새 기간의 할당량을 채움
            assert self.quota_consumed == 0
            self.max_quota = amount
        else:
            # 기간 동안 할당량을 소비함
            assert self.max_quota >= self.quota_consumed
            self.quota_consumed += delta
```

- 앞에서의 데모 코드를 다시 실행

```python
bucket = Bucket(60)
print("Initial", bucket)
fill(bucket, 100)
print("filled", bucket)

if deduct(bucket, 99):
    print("Had 99 quota")
else:
    print("Not enougn for 99 quota")

print("Now", bucket)

if deduct(bucket, 3):
    print("Had 3 quota")
else:
    print("Not enough for 3 quota")

print("Still", bucket)

>>>
Initial Bucket(max_quota=0, quota_consumed=0)
Filled Bucket(max_quota=100, quota_consumed=0)
Had 99 quota
Now Bucket(max_quota=100, quota_consumed=99)
Not enough for 3 quota
Still Bucket(max_quota=100, quota_consumed=99)
```
