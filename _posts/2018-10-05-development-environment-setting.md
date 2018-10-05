---
layout: post
title: "개발 환경 세팅"
date: 2018-10-05 18:49:38
tagline: "Window 10 및 Ubuntu 16.04에서 TensorFlow 개발 환경 세팅 방법 정리"
categories:
- Ubuntu 16.04
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-05-development-environment-setting
---

## Window Bash 이용
먼저 파이썬이 설치되어 있는지 확인해 본다.

```bash
>>> python --version
The program 'python' can be found in the following packages:
 * python-minimal
 * python3
Try: sudo apt install <selected package>

>>> python3 --version
Python 3.5.2
```

파이썬2는 깔려있지 않지만 파이썬3는 깔려있는 것으로 확인된다. `3.5.2` 버전을 사용할 것이기 때문에 진행하도록 한다.

`pip`이 설치되어 있는지 확인해보자. `pip freeze` 명령을 통해서 `pip`으로 관리되고 있는 패키지들의 목록을 확인할 수 있다.

```bash
>>> pip freeze
The program 'pip' is currently not installed. You can install it by typing:
sudo apt install python-pip
```
