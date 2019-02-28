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

## 개발 환경 구축 
[링크1](https://github.com/alumae/kaldi-gstreamer-server?fbclid=IwAR35dhlAFrf59cdZ9B67iZe1QFUHwwmiRSrzBDwGwLaAqUZb4bvt2ttWA3U)에서 확인할 수 있듯이 Kaldi Gstreamer Server를 사용하기 위해 필요한 파이썬 패키지는 아래와 같이 정리할 수 있다.

파이썬 패키지 Requirements:
- Tornado 4 ([http://www.tornadoweb.org/en/stable/](http://www.tornadoweb.org/en/stable/))
- ws4py (버전 0.3.0에서 0.3.2)
- YAML
- JSON

참고로 이 툴킷은 파이썬 버전 2에서 사용 가능하며 파이썬 2에서는 JSON이 기본적으로 설치가 이미 되어있어서([참고](https://stackoverflow.com/questions/41466431/pip-install-json-fails-on-ubuntu)) JSON을 따로 설치할 필요는 없다.

그 외에는 Kaldi 및 Kaldi와 관련된 여러 패키지들이 필요한데 [링크2](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)에서 툴킷을 실행할 수 있는 도커 이미지를 제공한다. 따라서 도커를 이용하여 개발 환경을 구축할 필요가 있다.

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


