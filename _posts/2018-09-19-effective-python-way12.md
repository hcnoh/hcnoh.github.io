---
layout: post
title: "[Effective Python] WAY 12. for와 while 루프 뒤에는 else 블록을 쓰지 말자"
date: 2018-09-19 10:15:35
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Effective Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-19-effective-python-way12
---

파이썬의 루프는 다른 프로그래밍 언어와는 다르게 루프에서 반복되는 내부 블록 바로 다음에 `else` 블록을 둘 수 있는 기능이 있다. 이 `else` 블록을 사용함에 있어서 주의해야 할 사항들을 이번 장에서 정리한다.

## else 블록의 특징
- 루프가 종료되자마자 바로 실행:
  - 루프가 완료되어야만 실행되는 것!
  - 루프 내에서 `break` 문을 사용해야만 `else` 블록을 건너뛸 수 있음

```python
for i in range(3):
    print("Loop %d" % i)
    if i == 1:
        break
else:
    print("Else block!")

>>>
Loop 0
Loop 1
```

- 빈 시퀀스를 처리하는 루프문에서도 `else` 블록이 즉시 실행

```python
for x in []:
    print("Never runs")
else:
    print("For Else block!")

>>>
For Else block!
```

- `while` 루프가 처음부터 거짓인 경우에도 실행

```python
while False:
    print("Never runs")
else:
    print("While Else block!")

>>>
While Else block!
```

## else 블록의 활용
- 두 숫자가 서로소인지를 판별하는 코드

```python
a = 4
b = 9
for i in range(2, min(a, b) + 1):
    print("Testing", i)
    if a % i == 0 and b % i == 0:
        print("Not coprime")
        break
else:
    print("Coprime")

>>>
Testing 2
Testing 3
Testing 4
Coprime
```

- 이런 방식을 추천하진 않음
- 대신 두 가지 스타일의 헬퍼 함수를 사용하면 좋음

## 첫 번째 스타일의 헬퍼 함수
- 찾으려는 조건을 찾았을 때 바로 반환
- 루프가 실패로 끝나면 기본 결과(`True`)를 반환

```python
def coprime(a, b):
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            return False
    return True
```

## 두 번째 스타일의 헬퍼 함수
- 루프에서 찾으려는 대상을 찾았는지 알려주는 결과 변수 사용
- 뭔가를 찾았으면 즉시 `break`로 루프를 중단

```python
def coprime(a, b):
    is_coprime = True
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            is_coprime = False
            break
    return is_coprime
```

## else 블록을 쓰지 말아야 하는 이유
- `else` 블록을 사용한 표현의 장점 < 코드를 이해하려는 사람들이 받을 부담감

