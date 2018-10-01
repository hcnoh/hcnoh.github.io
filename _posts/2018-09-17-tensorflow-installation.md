---
layout: post
title: "TensorFlow 설치 방법 정리"
date: 2018-09-17 1:28:2
tagline: "Virtualenv를 이용하여 TensorFlow 설치하는 방법 정리"
categories:
- TensorFlow
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-17-tensorflow-installation
---

Tmux 및 zshell, vim까지 세팅이 대충 끝나고 이제는 TensorFlow를 설치하여 작업 환경 구축을 마무리할 생각이다. TensorFlow는 여러 가상환경 위에서 설치하는 것을 제공하지만 여기서는 Virtualenv를 이용한 가상환경 위에서 설치하는 방법을 정리하였다.

이번 포스팅은 TensorFlow 공식 홈페이지에 나온 방법을 참고하여 정리하였다.
- [출처 1](https://www.tensorflow.org/install/install_linux#InstallingVirtualenv)

## 가상환경 만들기
먼저 다음과 같이 파이썬 가상환경을 만들어 준다. 물론 Virtualenv는 이미 설치가 되어있는 상태이다. 가상환경의 이름은 tensorflow로 한다. 다음의 명령을 통해서 tensorflow라는 이름의 파이썬2 가상환경을 만들 수 있다.

```bash
>>> virtualenv tensorflow
```

`-p python3` 명령을 추가하면 파이썬3 가상환경을 만들게 된다.

```bash
>>> virtualenv -p python3 tensorflow
```

또한 원래 시스템에 설치된 파이썬의 `site-packages`에 있는 패키지들을 가상환경 생성 시에 설치할 수도 있다. 물론 패키지들만 가져오는 것이고 `site-packages`에 액세스 권한을 부여하지는 않는다.

```bash
>>> virtualenv --system-site-packages -p python3 tensorflow
```

이렇게 하면 `site-packages`에 있는 패키지들을 같이 설치한 파이썬3 가상환경인 tensorflow를 만들게 된다. 가상환경을 만든 이후에 다음의 명령을 통해 가상환경을 실행할 수 있다.

```bash
>>> source ~/tensorflow/bin/activate

hcnoh@svr /home/hcnoh tensorflow
>>> python --version
Python 3.5.2
```

Activate를 한 이후에 파이썬 버전을 확인해보니 제대로 나오는 것을 확인할 수 있다. 이러면 무사히 가상환경이 만들어 진 것이다.

## TensorFlow 설치
먼저 `pip` 업데이트를 해본다.

```bash
hcnoh@svr /home/hcnoh tensorflow
>>> pip install -U pip
Requirement already up-to-date: pip in ./tensorflow/lib/python3.5/site-packages (18.0)
```

이미 최신판이라는 결과가 뜬다. 그 다음으로 `TensorFlow-GPU` 버전을 설치한다.

```bash
>>> pip install -U tensorflow-gpu
```

설치가 진행이 될 것이다. 설치 완료가 되면 테스트를 해볼 수 있다.

```bash
>>> python -c "import tensorflow as tf; print(tf.__version__)"
1.10.0
```

`pip show` 명령을 통해서도 버전을 확인할 수 있다.

```bash
>>> pip show tensorflow-gpu
Name: tensorflow-gpu
Version: 1.10.0
Summary: TensorFlow is an open source machine learning framework for everyone.
Home-page: https://www.tensorflow.org/
Author: Google Inc.
Author-email: opensource@google.com
License: Apache 2.0
Location: /home/hcnoh/tensorflow/lib/python3.5/site-packages
Requires: tensorboard, grpcio, setuptools, protobuf, six, gast, termcolor, wheel, numpy, absl-py, astor
Required-by:
```

1.10.0 버전이 무사히 설치된 것을 확인할 수 있다.

## 가상환경 빠져나오기
가상환경은 `deactivate` 명령을 통해 간단히 빠져나올 수 있다.

```bash
hcnoh@svr /home/hcnoh tensorflow
>>> deactivate
hcnoh@svr /home/hcnoh
```

[Go to the Home Page]({{ site.url }}{{ site.baseurl }})
