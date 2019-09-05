---
layout: project
use_math: true
title: "[모델 구현] Dynamic Key-Value Memory Networks Implementation in TensorFlow (README)"
date: 2019-08-31 23:15:23
tagline: "A TensorFlow implementation of Dynamic Key-Value Memory Networks"
categories:
- Project
tags:
- deep learning
- tensorflow
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-08-31-dkvmn-tensorflow
---

# Dynamic Key-Value Memory Networks Implementation in TensorFlow
["Dynamic Key-Value Memory Networks for Knowledge Tracing"](https://arxiv.org/abs/1611.08108) 논문에서 제시한 Knowledge Tracing 모델인 Dynamic Key-Value Memory Networks (DKVMN) 모델의 TensorFlow 구현입니다. 이 저장소는 DKVMN 모델 뿐 아니라 DKT (Deep Knowledge Tracing) 모델과의 비교 분석이 가능하도록 DKT 모델도 구현이 되어있습니다.

## Installing Dependencies
1. 파이썬 3.5 버전을 설치합니다.
2. TensorFlow 1.14.0 버전 설치합니다. GPU가 사용가능하다면 GPU 버전을, 그렇지 않다면 CPU 버전을 설치합니다.
3. 파이썬 패키지(Requirements)를 설치합니다. 설치는 아래와 같은 명령을 통해 수행 가능합니다.

   ```bash
   $ pip install -r requirements.txt
   ```

   아래와 같이 `virtualenv`를 활용하여 더 쉽게 패키지 관리를 할 수도 있습니다. `virtualenv`가 이미 설치되어 있다는 가정 하에 아래의 명령을 수행하면 됩니다. 다음은 리눅스 환경에서의 명령어입니다.

   ```bash
   $ virtualenv dkvmn-tensorflow-venv
   $ source ./dkvmn-tensorflow-venv/bin/activate
   $ pip install -r requirements.txt
   ```

   다음은 윈도 환경에서의 명령어입니다.
    ```bash
   $ virtualenv dkvmn-tensorflow-venv
   $ ./dkvmn-tensorflow-venv/Script/activate
   $ pip install -r requirements.txt
   ```

## Training
*Note: 데이터 세트를 다운로드받기 위한 여유분의 저장 공간이 필요합니다. ASSISTmentsData Skill-builder data 2009-2010 데이터 세트를 기준으로 약 80MB 이상의 저장 공간이 필요합니다.*

1. **데이터 세트를 다운로드합니다.**

   이 저장소는 다음의 데이터 세트에 대한 활용을 제공합니다.
   - [ASSISTmentsData Skill-builder data 2009-2010](https://sites.google.com/site/assistmentsdata/home/assistment-2009-2010-data/skill-builder-data-2009-2010)

2. **데이터 세트를 원하는 위치에 Unpack합니다.**

   Unpacking 이후에는 디렉토리 구조가 다음과 같아야 합니다.

   ```
   dataset
   └── assistments
       └── skill_builder_data.csv
   ```

3. **`main.py`의 스크립트 상단에서 Training을 위한 데이터 세트 Path 및 Hyperparameter들을 수정하여 줍니다.**

   `DATASET_DIR`같은 경우는 `assistments` 디렉토리까지 명시해줘야 합니다.

4. **`main.py`를 실행하여 모델 Training을 수행합니다.**

   데이터 전처리는 `main.py`를 실행하면 자동으로 수행되게 됩니다.

   ```bash
   $ python main.py
   ```

## Results

Training Results (DKVMN vs DKT):
- Training Loss
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-44-03.png)
- Mean Training Loss per 1 Epoch
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-44-19.png)
- Mean Test Loss per 1 Epoch
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-44-32.png)
- Test ACC
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-44-45.png)
- Test AUC
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-45-06.png)

Problem Clustering Results:
- ASSISTmentsData with DKVMN
![](/assets/img/2019-08-31-dkvmn-tensorflow/2019-08-31-dkvmn-tensorflow_2019-09-05-19-46-03.png)

## Recent Updates
1. 전처리 코드 수정
2. 결과물 업데이트를 위한 Plot 코드 작성중

## References
- DKVMN: [Dynamic Key-Value Memory Networks for Knowledge Tracing](https://arxiv.org/pdf/1611.08108.pdf)
- DKT: [Deep Knowledge Tracing](https://papers.nips.cc/paper/5654-deep-knowledge-tracing.pdf)