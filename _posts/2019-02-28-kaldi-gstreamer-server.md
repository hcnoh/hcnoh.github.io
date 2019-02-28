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



