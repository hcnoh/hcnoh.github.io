---
layout: post
title: "WAY 45. copyreg로 pickle을 신뢰할 수 있게 만들자"
date: 2018-10-25 10:56:25
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-25-effective-python-way44
---

## 내장 모듈 pickle
- 직렬화(파이썬 객체 => 바이트 스트림) 또는 역직렬화(바이트 스트림 => 파이썬 객체)하는데 사용
- `pickle`을 사용할 때 주의사항:
  - 신뢰할 수 없는 부분과 통신하는 데 사용하면 

