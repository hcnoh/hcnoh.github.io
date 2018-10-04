---
layout: post
title: "WAY 23. 인터페이스가 간단하면 클래스 대신 함수를 받자"
date: 2018-10-04 15:01:51
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-04-effective-python-way23
---

- 파이썬 내장 API의 상당수는 함수를 넘겨서 동작을 자동화하는 기능 존재: 후크(`hook`) 기능
- `list` 타입의 `sort` 메서드 예시
  - 정렬에 기준이 되는 `key` 인수를 입력 받음
  - `lambda` 표현식을 `key` 후크로 넘겨서 이름 리스트를 길이로 정렬

```python
names = ["Socrates", "Archimedes", "Plato", "Aristotle"]
names.sort(key=lambda x: len(x))
print(names)

>>>
["Plato", "Socrates", "Aristotle", "Archimedes"]
```

- 다른 언어에서 후크는 추상 클래스로 정의할 수 있음
- 파이썬의 후크:
  - 인수와 반환 값을 잘 정의해놓은 단순히 상태가 없는 함수
  - 함수는 클래스보다 간단하여 후크로 쓰기에 이상적
  - 함수가 후크로 동작하는 이유: 파이썬이 일급 함수(`first-class function`)를 갖췄기 때문
- `defaultdict` 클래스의 동작을 사용자화하는 예제
  - 찾을 수 없는 키에 접근할 때마다 호출될 함수를 입력받음

```python
def log_missing():        # key를 찾을 수 없을 때마다 로그를 남기고 기본값으로 0을 반환하는 후크를 정의
    print("Key added")
    return 0

current = {"green": 12, "blue": 3}
increments = [("red", 5), ("blue", 17), ("orange", 9)]
result = defaultdict(log_missing, current)
print("Before:", dict(result))
for key, amount in increments:
    result[key] += amount
print("After:", dict(result))

>>>
Before: {"green": 12, "blue": 3}
Key added
Key added
After: {"orange": 9, "green": 12, "blue": 20, "red": 5}
```

- 기본값 후크를 `defaultdict`에 넘겨서 찾을 수 없는 키의 총 개수를 세기를 원함
  - 상태 보존 클로저를 사용함으로서 구현
  - 상태 보존 클로저를 기본값 후크로 사용하는 헬퍼 함수 구현

```python
def increment_with_report(current, increments):
    added_count = 0
    
    def missing():
        nonlocal added_count    # 상태 보존 클로저
        added_count += 1
        return 0
        
    result = defaultdict(missing, current)
    for key, amount in increments:
        result[key] += amount
    
    return result, added_count

result, count = increment_with_report(current, increments)
assert count == 2
```

- 위의 예제에서 `defaultdict`는 `missing` 후크가 상태를 유지한다는 사실은 모름
  - 하지만 `increment_with_report`의 결과는 잘 나옴
  - 간단한 함수를 인터페이스용으로 사용할 때 얻을 수 있는 
