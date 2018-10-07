---
layout: post
title: "WAY 26. 믹스인 유틸리티 클래스에만 다중 상속을 사용하자"
date: 2018-10-07 21:05:12
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-07-effective-python-way26
---

## 파이썬의 다중 상속
- 파이썬은 다중 상속을 다루기 쉽게 하는 기능을 내장한 객체 지향 언어
- 하지만 다중 상속은 아예 안하는 것이 좋음
  - 다중 상속으로 얻는 편리함과 캡슐화가 필요한 경우: 대신 믹스인(`mix-in`)을 작성하는 방안
- 믹스인?
  - 클래스에서 제공해야 하는 추가적인 메서드만 정의하는 작은 클래스
  - 자체의 인스턴스 속성(`attribute`)을 정의하지 않음
  - `__init__` 생성자를 호출하도록 요구하지 않음
