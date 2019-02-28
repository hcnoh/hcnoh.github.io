---
layout: post
use_math: true
title: "[Kaldi] Kaldi Gstreamer Server 사용법 정리"
date: 2019-02-28 15:23:14
tagline: "Kaldi를 통해서 학습된 음성 인식 모델을 테스트하기 위한 서버 구축 툴킷인 Kaldi Gstreamer Server에 대한 간단한 사용법 정리"
categories:
- Kaldi
tags:
- kaldi
- docker
- speech
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-02-28-kaldi-gstreamer-server
---

이번 포스팅은 다음의 링크들을 참고하여 작성하였다:
- [링크1](https://github.com/alumae/kaldi-gstreamer-server?fbclid=IwAR35dhlAFrf59cdZ9B67iZe1QFUHwwmiRSrzBDwGwLaAqUZb4bvt2ttWA3U)
- [링크2](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)

## Kaldi Gstreamer Server란?
음성 인식 개발용 툴킷으로 최근 가장 많이 사용되는 것으로 알려진 것이 바로 Kaldi라는 툴킷이다. Kaldi는 GMM(Gaussian Mixture Model), HMM(Hidden Markov Model) 등의 통계 기반의 모델 및 최근의 딥러닝 모델들까지 포함하는 음성 인식 모델들을 제공해줄 뿐 아니라 Language Model 같은 전/후처리 모듈들 역시 제공해주는 사실상 음성 인식을 위한 올인원 패키지라고 볼 수 있다.
Kaldi Gstreamer Server는 [링크1](https://github.com/alumae/kaldi-gstreamer-server?fbclid=IwAR35dhlAFrf59cdZ9B67iZe1QFUHwwmiRSrzBDwGwLaAqUZb4bvt2ttWA3U)에서 확인할 수 있듯이 Kaldi를 통해서 얻어진 음성 인식 모델을 실행하기 위한 서버 구축을 도와주는 프레임워크라고 보면 된다. 현재 처음 사용중이라 자세한 사항은 아직 파악이 되지 않았지만 사용하면서 차차 업데이트를 진행하도록 하겠다.

## 파이썬 패키지 
[링크1](https://github.com/alumae/kaldi-gstreamer-server?fbclid=IwAR35dhlAFrf59cdZ9B67iZe1QFUHwwmiRSrzBDwGwLaAqUZb4bvt2ttWA3U)에서 확인할 수 있듯이 Kaldi Gstreamer Server를 사용하기 위해 필요한 파이썬 패키지는 아래와 같이 정리할 수 있다.

파이썬 패키지 Requirements:
- Tornado 4 ([http://www.tornadoweb.org/en/stable/](http://www.tornadoweb.org/en/stable/))
- ws4py (버전 0.3.0에서 0.3.2)
- YAML
- JSON

참고로 이 툴킷은 파이썬 버전 2에서 사용 가능하며 파이썬 2에서는 JSON이 기본적으로 설치가 이미 되어있어서([참고](https://stackoverflow.com/questions/41466431/pip-install-json-fails-on-ubuntu)) JSON을 따로 설치할 필요는 없다.

그 외에는 Kaldi 및 Kaldi와 관련된 여러 패키지들이 필요한데 [링크2](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)에서 툴킷을 실행할 수 있는 도커 이미지를 제공한다. 따라서 도커를 이용하여 개발 환경을 구축할 필요가 있다.

## 도커 설치 확인
일단 여기서는 도커가 이미 설치되어 있다고 가정한다. 도커가 설치되어 있는지 확인하기 위해서는 다음의 명령을 수행하면 된다.

```bash
>>> docker version
Client:
 Version:           18.06.0-ce
 API version:       1.38
 Go version:        go1.10.3
 Git commit:        0ffa825
 Built:             Wed Jul 18 19:11:02 2018
 OS/Arch:           linux/amd64
 Experimental:      false

Server:
 Engine:
  Version:          18.06.0-ce
  API version:      1.38 (minimum version 1.12)
  Go version:       go1.10.3
  Git commit:       0ffa825
  Built:            Wed Jul 18 19:09:05 2018
  OS/Arch:          linux/amd64
  Experimental:     false
```

위와 같은 형식으로 출력이 된다면 잘 설치가 되어있는 것이다.

## 도커 이미지 가져오기
다음의 명령을 통해서 Kaldi Gstreamer Server 도커 이미지를 가져올 수 있다.

```bash
>>> docker pull jcsilva/docker-kaldi-gstreamer-server
Using default tag: latest
latest: Pulling from jcsilva/docker-kaldi-gstreamer-server
3d77ce4481b1: Pull complete 
53457bfe6a81: Pull complete 
6fddb0335893: Pull complete 
8e48209c42f9: Pull complete 
e24641a195b5: Pull complete 
Digest: sha256:74a1bc0818dbde060e3966767e4caaf9d8f039146120c121841c570b6efc6e04
Status: Downloaded newer image for jcsilva/docker-kaldi-gstreamer-server:latest
```

이미지가 잘 받아졌는지 확인하기 위해서는 다음의 명령을 수행하면 된다.

```bash
>>> docker images
REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
<none>                                  <none>              348b52ea004f        2 months ago        362MB
ubuntu                                  14.04               f17b6a61de28        3 months ago        188MB
tensorflow/serving                      latest-gpu          ad003bd9bd92        3 months ago        2.18GB
tensorflow/serving                      latest              d42952c6f8a6        3 months ago        230MB
nvidia/cuda                             latest              04a9ce0dec6d        6 months ago        1.96GB
tensorflow/tensorflow                   latest-gpu          49b48d227d6e        6 months ago        3.1GB
hello-world                             latest              2cb0d9787c4d        7 months ago        1.85kB
jcsilva/docker-kaldi-gstreamer-server   latest              13d616ef11e1        8 months ago        1.11GB
```

마지막 줄에 `jcsilva/docker-kaldi-gstreamer-server`라는 것이 보이는 것을 확인할 수 있다면 이미지를 제대로 받아온 것이다.

## 도커 컨테이너 생성
다운받은 도커 이미지를 이용해서 도커 컨테이너를 생성할 차례다. 다운받은 도커 이미지를 이용하여 도커 컨테이너를 생성하면 생성된 도커 컨테이너는 일종의 가상 환경의 역할을 할 수 있으며 여기서는 Kaldi를 포함한 여러 패키지를 사용할 수 있는 가상 환경이 생성되는 셈이다.

먼저 도커 컨테이너를 생성하기 전에 다음의 디렉토리를 생성해 준다.

```bash
>>> mkdir ~/kaldi-practice
>>> mkdir ~/kaldi-practice/models
>>> cd ~/kaldi-practice/models
```

그리고나서 도커 컨테이너를 생성하기 위해서 다음의 명령을 수행한다.

```bash
>>> docker run -it -p 8080:80 -v ~/kaldi-practice/kaldi_models:/opt/models jcsilva/docker-kaldi-gstreamer-server:latest /bin/bash
root@d63a3c7522dc:/opt#
```

아마 이런식으로 터미널에 출력이 될 것이다. 그러면 도커 컨테이너를 생성하여 현재 컨테이너에 진입한 상태이다. 컨테이너가 제대로 생성되었는지 확인하기 위해서 다음과 같이 몇 가지 명령을 수행해 본다.

```bash
root@d63a3c7522dc:/opt# ls
gst-kaldi-nnet2-online	kaldi  kaldi-gstreamer-server  models  start.sh  stop.sh
```

잘 실행이 된 것이다. 일단 다른 작업이 더 필요하기 때문에 현재 생성된 컨테이너는 종료하고 빠져나오도록 한다.

```bash
root@d63a3c7522dc:/opt# \exit
```
