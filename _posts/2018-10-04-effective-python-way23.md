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

- 파이썬 내장 API의 상당수는 함수를 넘겨서 동작을 자동화하는 기능 존재: `hook` 기능
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
