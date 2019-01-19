---
layout: project
use_math: true
title: "wavenet-tensorflow"
date: 2019-01-19 01:29:33
tagline: "A TensorFlow implementation of Google's WaveNet"
categories:
- Project
tags:
- deep learning
- tensorflow
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-19-wavenet-tensorflow
---

# WaveNet in TensorFlow
This repository is for a TensorFlow implementation of Google's WaveNet. The original link of WaveNet paper is [here](https://regmedia.co.uk/2016/09/09/wavenet.pdf). In this repository, only global conditioning was implemented for WaveNet model.

![](/assets/img/2019-01-19-wavenet-tensorflow/wavenet-architecture.png)

## Prerequisites
- Python 3.5
- librosa 0.6.2 or higher
- numpy 1.14.5 or higher
- tensorflow-gpu 1.10.0 or higher

More simpler way to install these prerequisites using python 3.5 virtualenv is that:

```bash
>>> virtualenv --system-site-packages -p python3 wavenet-venv
>>> source ./wavenet-venv/bin/activate
>>> pip install -r requirements.txt
```

## Usage
The dataset directory should be organized by this structure.

```
dataset
├── p01 (person name, the name doesn't matter)
    ├── txt
        ├── xxx.txt (name doesn't matter)
        ├── yyy.txt
        └── ...
    └── wav
        ├── xxx.wav (name doesn't matter but should be same with corresponding txt file)
        ├── yyy.wav
        └── ...
├── p02
└── ...
```

To train a model:

```bash
>>> python train.py
```

Also, to generate a audio with a trained model:

```bash
>>> python generate.py
```

## Results
I trained WaveNet model without using any global conditions. The trained model just mimics voice from original audio dataset.
- The original audio sample that I used as a dataset: [https://soundcloud.com/artzizou/p280-original-sample?in=artzizou/sets/wavenet-tensorflow](https://soundcloud.com/artzizou/p280-original-sample?in=artzizou/sets/wavenet-tensorflow)
- A generated audio from my trained WaveNet model: [https://soundcloud.com/artzizou/p280-final-version?in=artzizou/sets/wavenet-tensorflow](https://soundcloud.com/artzizou/p280-final-version?in=artzizou/sets/wavenet-tensorflow)

## Author
Hyungcheol Noh / [About Me](https://hcnoh.github.io/about)
