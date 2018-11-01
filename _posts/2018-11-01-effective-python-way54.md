---
layout: post
title: "WAY 54. 배포 환경을 구성하는 데는 모듈 스코프 코드를 고려하자"
date: 2018-11-01 16:35:36
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-01-effective-python-way54
---

## 제품화
- 작성한 파이썬 프로그램을 사용?
  - 개발 환경 => 제품 환경
- 즉, 다양한 상황에서 신뢰할 만한 프로그램을 만드는 것은 매우 중요함
- 파이썬 프로그램의 제품화:
  - 프로그램을 사용하는 동안 문제가 없게 하는 것
  - 파이썬의 내장 모듈 덕분에 디버깅, 최적화, 실행 시 프로그램의 품질과 성능을 극대화하는 테스트 기능 제공 가능

## 배포 환경/제품 환경
- `deployment environment` / `production environment`
- 프로그램을 실행하는 구성
- 모든 프로그램에는 적어도 하나의 배포 환경과 제품 환경이 있음

>- 프로그램을 작성하는 첫 번째 목적: 제품 환경에서 동작하도록 하는 것!

## 개발 환경
- `development environment`
- 제품 환경과는 많이 다름
- 프로그램을 작성하거나 수정하려면 개발에 사용 중인 컴퓨터에서 프로그램이 동작하도록 해야 함
