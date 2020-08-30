---
layout: post
title: "[Ubuntu] hostname 확인 및 변경"
date: 2018-10-05 17:43:12
tagline: "Ubuntu 16.04에서 hostname 확인 및 변경하는 방법 정리"
categories:
- Development Memo
tags:
- linux
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-05-ubuntu-hostname
---

## hostname 확인
- 터미널에서 `hostname`을 확인하는 방법

```bash
>>> hostname
```

- `vi` 에디터로 `hostname`이 적힌 파일을 열어볼 수 있음

```bash
>>> vi /etc/hostname
```

## hostname 변경
- 다음의 명령을 통해 `hostname`을 `new_host_name`으로 변경 가능하고 재부팅하면 적용이 됨

```python
>>> hostnamectl set-hostname new_host_name
```
