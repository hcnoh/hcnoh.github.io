---
layout: post
title: "[Effective Python] WAY 6. 한 슬라이스에 start, end, stride를 함께 쓰지 말자"
date: 2018-09-16 20:51:23
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Effective Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-16-effective-python-way06
---

이전 장에서 기본 슬라이싱 문법을 정리했는데 파이썬에서는 추가적으로 매 n번째 아이템을 가져올 수 있는 stride 문법을 제공한다.

## stride 기본 문법
- stride 문법의 기본 형태:
  - `some_list[start:end:stride]`
- stride 예시

```python
a = ["a", "b", "c", "d", "e", "f"]
odds = a[::2]
evens = a[1::2]
print(odds)
print(evens)

>>>
["a", "c", "e"]
["b", "d", "f"]
```

- -1 stride

```python
x = b'abcde'    # 바이트 문자열, 아스키 문자에는 잘 동작
y = x[::,-1]
print(y)

>>>
b'edcba'
```

```python
w = '原原'
x = w.encode("utf-8")   # 'utf-8' 바이트 문자열로 인코드된 유니코드 문자에서는 동작하지 않음
y = x[::-1]
z = y.decode("utf-8")

>>>
UnicodeDecoderError: 'utf-8' codec can't decode byte 0x9d in position 0: invalid start byte
```

- -n stride

```python
a = ["a", "b", "c", "d", "e", "f", "g", "h"]
a[::2]    # ["a", "c", "e", "g"]
a[::-2]   # ["h", "f", "d", "b"]
```

## start, end, stride를 함께 쓰면?
- start, end, stride를 함께 쓰는 예시

```python
a = ["a", "b", "c", "d", "e", "f", "g", "h"]
a[2::2]     # ["c", "e", "g"]
a[-2::-2]   # ["g", "e", "c", "a"]
a[-2:2:-2]  # ["g", "e"]
a[2:2:-2]   # []
```

- start, end, stride를 함께 쓰면 매우 혼란스러움
  - 가독성이 좋지 않기 때문에 각 인덱스가 어떤 작용을 하는지 확인이 어려움
  - 특히 음수 stride의 경우 더욱 문제
  - 셋은 함께 쓰지 않는 것이 좋음
  - stride는 양수 값을 사용하는 것이 좋음
- start, end, stride를 함께 써야하는 상황?
  - stride를 먼저 적용하고 나머지 적용: shallow copy의 크기를 최소화하기 위해서
- 내장 모듈 itertolls의 islice 메서드를 사용할 수 있음
  - start, end, stride를 두 단계로 나눠서 실행할 시간 및 메모리가 충분하지 않은 경우 유용
  - start, end, stride에 음수 값을 허용하지 않음
