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
>>> mkdir ~/kaldi-practice/kaldi_models
>>> cd ~/kaldi-practice/kaldi_models
```

그리고나서 도커 컨테이너를 생성하기 위해서 다음의 명령을 수행한다. 도커 컨테이너를 생성할 때 `-p` 옵션은 호스트 포트는 8080, 컨테이너 포트는 80으로 설정하여 실행하였으며, `-v` 옵션을 통해서 호스트 디렉토리를 `~/kaldi-practice/kaldi_models`, 컨테이너 디렉토리를 `/opt/models`로 설정하였다.

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
root@d63a3c7522dc:/opt# exit
```

## Kaldi 모델 다운로드
[링크2](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)에서는 간단한 예제를 한 번 돌려보기 위한 메뉴얼을 제공한다. 메뉴얼에서 모델을 다운로드받기 위한 명령은 다음과 같이 나와있다.

```bash
>>> cd ~/kaldi-practice/kaldi_models
>>> wget https://phon.ioc.ee/~tanela/tedlium_nnet_ms_sp_online.tgz
```

혹시 다음과 같은 에러가 발생할 수도 있다.

```bash
--2019-02-28 09:35:32--  https://phon.ioc.ee/~tanela/tedlium_nnet_ms_sp_online.tgz
Resolving phon.ioc.ee (phon.ioc.ee)... 193.40.251.126
Connecting to phon.ioc.ee (phon.ioc.ee)|193.40.251.126|:443... connected.
ERROR: cannot verify phon.ioc.ee's certificate, issued by ‘CN=TERENA SSL CA 3,O=TERENA,L=Amsterdam,ST=Noord-Holland,C=NL’:
  Issued certificate has expired.
To connect to phon.ioc.ee insecurely, use `--no-check-certificate'.
```

안전하지 않은 접근이라는 에러이다. 만약 위와 같은 에러가 발생한다면 다음과 같이 명령을 수정해본다.

```bash
>>> wget https://phon.ioc.ee/~tanela/tedlium_nnet_ms_sp_online.tgz --no-check-certificate
```

설치가 진행이 될 것이다.

```bash
--2019-02-28 09:36:55--  https://phon.ioc.ee/~tanela/tedlium_nnet_ms_sp_online.tgz
Resolving phon.ioc.ee (phon.ioc.ee)... 193.40.251.126
Connecting to phon.ioc.ee (phon.ioc.ee)|193.40.251.126|:443... connected.
WARNING: cannot verify phon.ioc.ee's certificate, issued by ‘CN=TERENA SSL CA 3,O=TERENA,L=Amsterdam,ST=Noord-Holland,C=NL’:
  Issued certificate has expired.
HTTP request sent, awaiting response... 200 OK
Length: 1552598256 (1.4G) [application/x-gzip]
Saving to: ‘tedlium_nnet_ms_sp_online.tgz’

tedlium_nnet_ms_sp_online.tgz                      100%[================================================================================================================>]   1.45G   586KB/s    in 16m 40s 

2019-02-28 09:53:36 (1.48 MB/s) - ‘tedlium_nnet_ms_sp_online.tgz’ saved [1552598256/1552598256]
```

그 다음으로 압축을 풀어준다.

```bash
>>> tar -zxvf tedlium_nnet_ms_sp_online.tgz
english/tedlium_nnet_ms_sp_online/
english/tedlium_nnet_ms_sp_online/ivector_extractor/
english/tedlium_nnet_ms_sp_online/ivector_extractor/final.dubm
english/tedlium_nnet_ms_sp_online/ivector_extractor/global_cmvn.stats
english/tedlium_nnet_ms_sp_online/ivector_extractor/splice_opts
english/tedlium_nnet_ms_sp_online/ivector_extractor/online_cmvn.conf
english/tedlium_nnet_ms_sp_online/ivector_extractor/final.ie
english/tedlium_nnet_ms_sp_online/ivector_extractor/final.mat
english/tedlium_nnet_ms_sp_online/words.txt
english/tedlium_nnet_ms_sp_online/phones.txt
english/tedlium_nnet_ms_sp_online/word_boundary.int
english/tedlium_nnet_ms_sp_online/G.carpa
english/tedlium_nnet_ms_sp_online/G.fst
english/tedlium_nnet_ms_sp_online/conf/
english/tedlium_nnet_ms_sp_online/conf/ivector_extractor.conf
english/tedlium_nnet_ms_sp_online/conf/splice.conf
english/tedlium_nnet_ms_sp_online/conf/online_nnet2_decoding.conf
english/tedlium_nnet_ms_sp_online/conf/online_cmvn.conf
english/tedlium_nnet_ms_sp_online/conf/mfcc.conf
english/tedlium_nnet_ms_sp_online/final.mdl
english/tedlium_nnet_ms_sp_online/HCLG.fst
```

그 다음으로는 모델을 실행시키기 위한 `yaml` 파일을 다운받아 준다. 역시 메뉴얼에서 제공하는 방법대로 따라간다.

```bash
>>> wget https://raw.githubusercontent.com/alumae/kaldi-gstreamer-server/master/sample_english_nnet2.yaml -P ~/kaldi-practice/kaldi_models
--2019-02-28 10:01:58--  https://raw.githubusercontent.com/alumae/kaldi-gstreamer-server/master/sample_english_nnet2.yaml
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.128.133, 151.101.192.133, 151.101.0.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.128.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2226 (2.2K) [text/plain]
Saving to: ‘/home/hcnoh/kaldi-practice/kaldi_models/sample_english_nnet2.yaml’

sample_english_nnet2.yaml                          100%[================================================================================================================>]   2.17K  --.-KB/s    in 0s      

2019-02-28 10:01:58 (34.7 MB/s) - ‘/home/hcnoh/kaldi-practice/kaldi_models/sample_english_nnet2.yaml’ saved [2226/2226]
```

메뉴얼에서는 콘텐츠 업데이트를 위해 다음의 명령을 수행하라고 한다. 정확히 어떤 것을 수행하는지는 파악이 되지는 않았다. 차차 정리해보도록 하겠다.

```bash
>>> find ~/kaldi-practice/kaldi_models/ -type f | xargs sed -i 's:test:/opt:g'
>>> sed -i 's:full-post-processor:#full-post-processor:g' ~/kaldi-practice/kaldi_models/sample_english_nnet2.yaml
```

## Master 서버와 Worker 서버를 같은 호스트 머신에 실행하는 방법
Master 서버와 Worker 서버를 다른 머신에 실행할 수도 있겠지만 간단한 예제를 위해서는 같은 호스트 머신에 실행하는 방법을 수행하는 것을 먼저 해보는 것이 좋다.

일단 위에서의 방식과 동일하게 도커 컨테이너를 실행한다.

```bash
>>> docker run -it -p 8080:80 -v ~/kaldi-practice/kaldi_models:/opt/models jcsilva/docker-kaldi-gstreamer-server:latest /bin/bash
root@d63a3c7522dc:/opt#
```

또는 이미 도커 컨테이너가 실행되고 있다면 다음의 명령을 통해서 도커 컨테이너에 접속한다.

```bash
>>> docker exec -it [CONTAINER ID] /bin/sh
```

도커 컨테이너가 잘 실행이 되었다면 컨테이너 내부에서 다음의 명령을 통해서 다음의 명령을 수행하여 서비스를 개시한다.

```bash
>>> /opt/start.sh -y /opt/models/sample_english_nnet2.yaml
```

만약 서비스를 종료하고 싶다면 다음의 명령을 수행한다.

```bash
>>> /opt/stop.sh
```

서비스가 제대로 실행되고 있는지 확인하려면 다음과 같은 과정을 수행하면 된다. 먼저 도커 컨테이너가 돌아가고 있는 로컬호스트에서 파이어폭스 또는 크롬을 실행한다. 필자는 로컬호스트가 작업용 서버라서 GUI가 제공되지 않기 때문에 다른 방법으로 파이어폭스를 실행해야만 했다. 그 방법을 간단하게 정리하면 아래와 같다.

먼저 로컬호스트에 접속을 하는데 `ssh` 접속 명령을 사용한다. 이 때 옵션 -X를 추가해주어야 한다. 이전 [포스팅](https://hcnoh.github.io/2018-10-17-ubuntu-ssh-scp-usage)을 참고하여 다음과 같이 접속하면 된다.

```bash
>>> ssh -X [서버 로그인을 위한 계정 ID]@[서버 IP 주소] -p [포트 번호]
```

로컬호스트에 접속하여 다음의 명령을 통해서 파이어폭스를 실행하면 된다.

```bash
>>> firefox
```

파이어폭스 또는 크롬을 실행한 이후에 주소에 [http://www.websocket.org/echo.html](http://www.websocket.org/echo.html)을 입력하여 접속한다. 그 다음 `location:`에 `ws://localhost:8080/client/ws/status`을 입력한 이후에 Connect 버튼을 누른다.

![](/assets/img/2019-02-28-kaldi-gstreamer-server/01.png)

위와 같이 나오면 실행이 잘 되고있는 것이다. 특히 `num_workers_abailable`이 1인 것을 확인하자. 잘 되는 것을 확인했다면 `Ctrl+P`를 누른 후에 `Ctrl+Q`를 누르면 컨테이너가 실행되는 상태로 컨테이너 밖으로 빠져나올 수 있을 것이다. 빠져나온 후에 `~/kaldi-practice` 디렉토리로 이동하여 테스트에 사용할 `client` 예제 및 음성 파일을 다운받도록 한다.

```bash
>>> wget https://raw.githubusercontent.com/alumae/kaldi-gstreamer-server/master/kaldigstserver/client.py -P ~/kaldi-practice/tmp/
>>> wget https://raw.githubusercontent.com/jcsilva/docker-kaldi-gstreamer-server/master/audio/1272-128104-0000.wav -P ~/kaldi-practice/tmp/
>>> wget https://raw.githubusercontent.com/alumae/kaldi-gstreamer-server/master/test/data/bill_gates-TED.mp3 -P ~/kaldi-practice/tmp/
>>> cd tmp
>>> ls
client.py  1272-128104-0000.wav  bill_gates-TED.mp3
```

이제 다운로드 받은 `client.py` 파일을 실행시켜야 하는데 여기서 `ws4py==0.3.2` 패키지가 설치가 되어있어야 한다. 위에서 파이썬 패키지 Requirements에 정리된 패키지들을 모두 설치했다면 문제가 없을 것이다. 파이썬 코드를 실행한다.

```bash
>>> python ~/kaldi-practice/tmp/client.py -u ws://localhost:8080/client/ws/speech -r 32000 ~/kaldi-practice/tmp/1272-128104-0000.wav
mr coulter is the apostle of the middle classes and we're glad to welcome his. Audio sent, now sending EOS
mr coulter is the apostle of the middle classes and we're glad to welcome his gospel.
mr coulter is the apostle of the middle classes and we're glad to welcome his gospel.
```

잘 실행이 되는 것을 확인할 수 있다. 음성을 실시간으로 스크립트화하여 터미널에 띄워준다.

