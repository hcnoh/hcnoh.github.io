---
layout: post
title: "WAY 28. 커스텀 컨테이너 타입은 collections.abc의 클래스를 상속받게 만들자"
date: 2018-10-09 09:47:31
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-09-effective-python-way28
---

## 컨테이너
- 파이썬 프로그래밍의 대부분: 데이터를 담은 클래스들을 정의, 이 객체들이 연계되는 방법을 명시
- 모든 파이썬 클래스는 일종의 컨테이너:
  - 속성/기능을 함께 캡슐화
- 파이썬은 데이터 관리용 내장 컨테이너 타입(리스트, 튜플, 세트, 딕셔너리)도 제공
