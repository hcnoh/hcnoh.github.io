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
한 컴퓨터에서 여러 프로젝트를 관리하다보면 프로젝트마다 패키지를 따로 관리할 필요성을 느끼게 된다. 파이썬에서는 가상환경 기능을 통해서 프로젝트마다 따로 패키지를 관리할 수 있다. 파이썬에서 제공하고 있는 가상환경 관리 패키지가 바로 `virtualenv`이다.

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

## Virtualenv를 이용하여 가상환경 만들기
파이썬2의 가상환경을 만드는 명령은 다음과 같다.

```bash
>>> virtualenv env_name
```

위의 명령을 통해서 `env_name`이라는 이름의 파이썬2 가상환경을 만들 수 있다.

파이썬3의 가상환경을 만드는 명령은 다음과 같다.

```bash
>>> virtualenv -p python3 env_name
```

문제는 이러한 방식으로 만들게 되면 이 가상환경들은 아무 패키지도 포함하지 않은 빈 가상환경이 된다. 아마 기본 파이썬에 설치되어있는 `site-packages`를 모두 가져오고 싶을 수 있다. 다음의 명령을 통해서 `site-packages`를 모두 포함한 파이썬3의 가상환경을 만들 수 있다.

```bash
>>> virtualenv --system-site-packages -p python3 env_name
```

또 한가지 주의할 점은 위의 명령들을 통해서 가상환경을 만들게 되면 가상환경을 담은 디렉토리가 현재 내가 터미널 상에서 위치하고 있는 디렉토리 내부에 만들어진다는 점이다. 즉, 현재 내가 `/home/hyungcheol`에 위치해 있었다면 가상환경이 담긴 디렉토리의 경로는 `/home/hyungcheol/env_name`이 될 것이다.

어쨌든 내가 만든 가상환경이 담긴 디렉토리의 경로만 정확하게 알고 있다면 다음의 명령을 통해서 가상환경을 실행할 수 있다.

```bash
>>> source /home/hyungcheol/env_name/bin/activate
```
