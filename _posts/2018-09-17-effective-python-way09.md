---
layout: post
title: "WAY 9. 컴프리헨션이 클 때는 제너레이터 표현식을 고려하자"
date: 2018-09-17 20:24:26
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-17-effective-python-way09
---

이번 포스팅은 다음의 링크를 참조하였다.
- [출처1](http://bluese05.tistory.com/m/56)

## 리스트 컴프리헨션의 문제점
- 입력 시퀀스에 있는 각 값 별로 아이템을 하나씩 담은 새 리스트를 통째로 생성한다는 점
- 따라서 메모리를 많이 소모할 수 있음
- 예제

```python
value = [len(x) for x in open("/tmp/my_file.txt")]
print(value)

>>>
[100, 57, 15, 1, 12, 75, 5, 86, 89, 11]
```

- 위의 예제에서는 리스트 컴프리헨션으로 인하여 파일에 있는 각 줄의 길이만큼 메모리가 필요함
  - 즉, 위의 예제에서 `value`를 생성할 때 차지하는 메모리를 정리하면:
    - `open`으로 열었던 txt 파일
    - `x in open()`으로 생성되는 소스 리스트 (value가 타겟 리스트)
  - 파일에 오류가 있거나 끊김이 없는 네트워크 소켓일 경우 문제가 발생

## 제너레이터 표현식
- 파이썬은 이러한 리스트 컴프리헨션의 문제를 해결하기위해 제너레이터 표현식을 제공
- 제너레이터 표현식?
  - 리스트 컴프리헨션과 제너레이터를 일반화한 것
  - 실행될 때 출력 시퀀스를 모두 구체화(메모리에 로딩)하지 않음
  - 표현식에서 한 번에 한 아이템을 내주는 이터레이터 기능
- 위의 예제와 동치인 제너레이터 표현식 예제

```python
it = (len(x) for x in open("/tmp/my_file.txt"))
print(it)

>>>
<generator object <genexpr> at 0x101b81480>
```

- 위의 예제에서는 메모리에 `open`으로 열었던 txt 파일만이 올라감
- 필요한 경우 `next` 사용: `next` 이후 이터레이터는 전진

```python
print(next(it))
print(next(it))
  
>>>
100
57
```

## 다른 제너레이터 표현식과 연계
- 예제

```python
it = (len(x) for x in open("/tmp/my_file.txt"))
roots = ((x, x**0.5) for x in it)
print(next(roots))

>>>
(15, 3.872983346207417)
```

- 위의 예제에서 `roots`를 전진시키면 루프의 도미노 효과로 내부 이터레이터인 `it`도 함께 전진

## 주의 사항
- 제너레이터 표현식이 반환한 이터레이터에는 상태가 존재
- 따라서 이터레이터를 한 번 넘게 사용하지 않도록 주의
- "WAY 17. 인수를 순회할 때는 방어적으로 하자" 

## 제너레이터란?
- 이터레이터를 생성해주는 함수
  - 이터레이터는 `next()` 메서드를 이용하여 데이터에 순차적으로 접근이 가능한 객체
  - `iterable`: 멤버를 하나씩 차례로 반환이 가능한 객체
    - `list`, `str`, `tuple`이 대표적인 예시
    - `__iter__()` 또는 `__getitem__()` 메서드로 정의된 class
    - `for loop` 또는 `zip()`, `map()`과 같이 sequential한 특징을 필요로 하는 작업에 유용하게 사용됨
  - `iterable`한 객체가 모두 이터레이터는 아님
    - `list` 객체는 `next()` 메서드를 가지고 있지 않음: 이터레이터가 아님
    - `for` 문을 사용하여 `list` 객체에 순차적으로 접근하는 경우는 파이썬 내부에서 임시로 `list`를 `iterator`로 자동으로 변환
    - `iter()` 함수를 이용하여 `list` 객체를 `listiterator` 타입으로 변경 가능

```python
x = [1, 2, 3]
y = iter(x)
print(type(x))
print(type(y))
print(next(y))
print(next(y))
print(next(y))
print(next(y))

>>>
<type 'list'>
<type 'listiterator'>
1
2
3
Traceback (most recent call last):      # 마지막 정보를 호출한 이후에 next()를 호출하면 StopIteration 이라는 exception이 발생
  File "<stdin>", line 1, in <module>
StopIteration
```

- 제너레이터와 일반 함수의 차이점: `yield`
  - 제너레이터 함수가 실행 중 `yield`를 만나면 반환 값을 `next()`를 호출한 쪽으로 전달
  - 이후 해당 함수는 일반 함수처럼 종료되지 않고 그 상태로 메모리에 유지됨

```python
def generator(n):
    i = 0
    while i < n:
        yield i
        i += 1

for x in generator(5):
    print x

>>>
0
1
2
3
4
```

- 위 구문이 실행되는 순서:
  1. `for` 문이 실행되며, 먼저 generator 함수가 호출
  2. generator 함수는 일반 함수와 동일한 절차로 실행
  3. `whille loop` 내부에서 `yield`를 만나면 `return`과 비슷하게 함수를 호출했던 `for` 문으로 값 반환
  4. 반환 이후에 일반 함수와는 다르게 generator 함수가 종료되지 않고 그대로 유지됨
  5. `for loop`이 돌면서 다시 generator 함수를 호출하면 `yield` 이후 구문부터 시작됨

## 지연 제너레이터란?
- 제너레이터 표현식을 통한 Lazy evaluation 효과 기능
  - 계산 결과 값이 필요할 때까지 계산을 늦추는 효과
- `sleep_func()` 예제

```python
import time

def sleep_func(x):
    print ("sleep")
    time.sleep(1)
    return x
```

- 리스트 컴프리헨셩릉 이용하여 `sleep_func()`을 수행하는 경우
  - 리스트 컴프리헨션을 수행하는 경우 `sleep_func()`을 `range()` 값 만큼 한번에 수행
  - 만약 `sleep_func()`에서 수행하는 시간이 길거나 `list` 값이 매우 큰 경우 수행할 때 부담

```python
l = [sleep_func(x) for x in range(5)]

for i in l:
    print(i)

>>>
sleep
sleep
sleep
sleep
sleep
0
1
2
3
4
```

- 제너레이터를 이용하여 `sleep_func()`을 수행하는 경우
  - 제너레이터는 실제 값을 로딩하지 않고 `for` 문이 수행될 때 하나씩 `sleep_func()`을 수행하며 값을 반환
  - 수행 시간이 긴 연산을 필요한 순간까지 늦출 수 있다는 점

```python
gen = (sleep_func(x) for x in range(5))

for i in gen:
    print(i)

>>>
sleep
0
sleep
1
sleep
2
sleep
3
sleep
4
```

