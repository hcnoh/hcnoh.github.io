---
layout: post
title: "Ubuntu SSH 사용법"
date: 2018-10-17 13:43:08
tagline: "Ubuntu에서 네트워크 접속 방법인 SSH 사용법 정리"
categories:
- Ubuntu 16.04
tags:
- linux
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-17-ubuntu-ssh-usage
---

이번 포스팅은 다음의 링크들을 참고하여서 작성하였다:
- [링크1](http://programmingskills.net/archives/315)
- [링크2](https://www.yongbok.net/blog/%EB%A6%AC%EB%88%85%EC%8A%A4-ssh-%ED%8C%8C%EC%9D%BC-%EC%A0%84%EC%86%A1-scp/)

## 아이디와 비밀번호로 접속
- 로컬에서 서버로 접속하는 방법
  - 서버 로그인을 위한 계정 ID, 서버 IP 주소, 포트 번호가 필요
  
```bash
>>> ssh [서버 로그인을 위한 계정 ID]@[서버 IP 주소] -p [포트 번호]
```

## SSH 파일 전송
- 로컬에서 서버로 파일 또는 디렉토리를 옮기고자 하는 경우
  - `SCP` 이용

```bash
>>> scp [옮기고자 하는 파일 또는 디렉토리 경로] [파일이 전송될 서버 계정 ID]@[서버 IP 주소]:[저장이 될 경로]
```
