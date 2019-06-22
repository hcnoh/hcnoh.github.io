---
layout: post
use_math: true
title: "[Windows] Python Virtualenv 사용법 정리"
date: 2019-06-19 09:41:09
tagline: "파이썬의 Virtualenv 패키지를 이용한 pip 패키지 관리 방법 정리 (Windows)"
categories:
- Windows
tags:
- virtualenv
- windows
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-19-windows-python-virtualenv
---

이번 포스팅은 다음의 링크들을 참고하여 작성하였다:
- [https://virtualenv.pypa.io/en/latest/userguide/](https://virtualenv.pypa.io/en/latest/userguide/)

이전 [Virtualenv 관련 포스팅](https://hcnoh.github.io/2018-10-06-ubuntu-python-virtualenv)과 다르게 이번 포스팅은 Windows 10 환경에서 사용법을 정리하였다. `virtualenv`에 대한 기본적인 설명은 이전 [Virtualenv 관련 포스팅](https://hcnoh.github.io/2018-10-06-ubuntu-python-virtualenv)을 참고하길 바란다.

## 가상환경 생성
가상환경의 디렉토리가 위치하기를 원하는 경로로 이동하여 다음의 명령을 수행하면 `tutorial-venv`라는 이름의 가상환경을 생성할 수 있다.

```bash
$ virtualenv tutorial-venv
```

현재 위치에 가상환경의 이름인 `tutorial-venv`라는 디렉토리가 생성될 것이다.

참고로 위의 명령어의 몇몇 중요한 옵션들은 다음과 같다.
- `-h`, `--help`: 도움말 표시
- `--system-site-packages`: 현재 생성하는 가상환경이 기존 파이썬의 `site-packages` 디렉토리에 접근 가능할지 여부, 만약 이 옵션을 추가한다면 가상환경 상에서의 `pip`을 통한 설치가 기존 파이썬에도 똑같이 적용되게 됨
- `--clear`: 현재 생성하는 가상환경과 같은 이름으로 가상환경이 이미 존재한다면 삭제하고 새로 만드는 옵션

## 가상환경 실행/해제
생성된 가상환경의 디렉토리 내부 경로를 통해서 다음의 명령을 통해 가상 환경이 실행될 수 있다.

```bash
$ .\tutorial-venv\Scripts\activate
```

![](/assets/img/2019-06-19-windows-python-virtualenv/2019-06-19-windows-python-virtualenv_2019-06-19-21-40-40.png)

위의 이미지와 같이 가상환경이 실행되면 커맨드라인 상에서 `(TUTORI~1)`라는 식으로 현재 `tutorial-venv`라는 이름의 가상환경이 실행되어 있다고 알려주게 된다.

또한 다음의 명령을 통해서 가상환경을 해제할 수 있다.

```bash
$ deactivate
```

![](/assets/img/2019-06-19-windows-python-virtualenv/2019-06-19-windows-python-virtualenv_2019-06-19-21-41-58.png)

## 가상환경에 패키지 설치
가상환경을 생성하면 패키지가 하나도 없는 것을 확인할 수 있을 것이다. 아래의 명령을 통해서 현재 설치된 패키지의 목록을 확인할 수 있다.

```bash
$ pip freeze
```

또한 이렇게 띄운 목록을 텍스트 파일로 저장할 수도 있다.

```bash
$ pip freeze > temp.txt
```

위의 명령을 수행하면 패키지들의 목록이 모두 `temp.txt` 파일에 저장이 될 것이다. 물론 이 파일을 새로운 패키지 설치에 이용할 수도 있다.

```bash
$ pip install -r temp.txt
```

위 명령을 통해서 `temp.txt`에 포함된 패키지들이 현재의 가상환경에 모두 설치가 될 것이다. 필자는 이러한 방식을 사용하여 기존 파이썬 패키지들을 모두 가져와서 설치하는 방법을 사용하였다. 참고로 `temp.txt`의 내용은 아래와 같다.

```bash
absl-py==0.7.1
astor==0.8.0
astroid==2.2.5
attrs==19.1.0
...(생략)
```