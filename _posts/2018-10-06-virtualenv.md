---
layout: post
title: "Python Virtualenv 사용법 정리"
date: 2018-10-06 12:28:06
tagline: "파이썬의 Virtualenv 패키지를 이용한 pip 패키지 관리 방법 정리"
categories:
- Python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-06-virtualenv
---

## Virtualenv란?
한 컴퓨터에서 여러 프로젝트를 관리하다보면 프로젝트마다 패키지를 따로 관리할 필요성을 느끼게 된다. 파이썬은 이러한 가상 환경 기능을 수행할 수 있는 패키지를 따로 제공하는데 이것이 `virtualenv`이다.

## Virtualenv 설치
`virtualenv`는 다음의 명령을 통해서 설치가 가능하다.

```bash
>>> pip install virtualenv
```

혹시 다음과 같은 에러가 난다면 아래처럼 명령을 조금 수정해주면 된다.

```bash
>>> pip install virtualenv
Collecting virtualenv
  Using cached https://files.pythonhosted.org/packages/b6/30/96a02b2287098b23b875bc8c2f58071c35d2efe84f747b64d523721dc2b5/virtualenv-16.0.0-py2.py3-none-any.whl
Installing collected packages: virtualenv
Could not install packages due to an EnvironmentError: [Errno 13] Permission denied: '/usr/local/lib/python3.5/dist-packages/virtualenv.py'
Consider using the `--user` option or check the permissions.
```

```bash
>>> pip install --user virtualenv
```
