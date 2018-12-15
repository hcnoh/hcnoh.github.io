---
layout: post
use_math: true
title: "Bahdanau Attention 개념 정리"
date: 2018-12-11 18:26:08
tagline: "가장 기본적인 Attention 메커니즘인 Bahdanau Attention에 대해서 스터디하여 정리"
categories:
- Deep Learning
tags:
- deep learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-12-11-bahdanau-attention
---

이번 포스팅은 다음의 논문을 스터디하여 정리하였다:
- [링크1](https://arxiv.org/abs/1409.0473)

## Neural Machine Translation
기계 번역은 이전부터 확률적인 접근 방법을 통해서 수행이 되어왔다. 간단히 설명하면 소스 문장 $$\mathbf{x}$$을 Conditioning하여 조건부 확률 $$p(\mathbf{y} \vert \mathbf{x})$$를 최대화하는 타겟 문장 $$\mathbf{y}$$를 찾는 것이다. 수식으로 정리하면 아래와 같다. $$\widehat{\mathbf{y}}$$는 모델의 타겟 문장 $$\mathbf{y}$$에 대한 추정 문장이다.

$$
\widehat{\mathbf{y}} = \arg \max_{\mathbf{y}} p(\mathbf{y} \vert \mathbf{x})
$$

최근 딥러닝을 이용한 연구가 활발히 진행되면서 뉴럴 네트워크를 통한 언어 번역을 시도해 보려는 NMT(Neural Machine Translation)에 관한 연구가 각광을 받게 되었다. NMT는 딥러닝 모델 $$f_{\theta}(\mathbf{x})$$를 학습시키기 위해서 Loss $$\mathcal{L}$$을 다음과 같이 사용하게 된다.

$$
\mathcal{L} = -p(f_{\theta}(\mathbf{x}) \vert \mathbf{x})
$$

즉, 이 Loss를 이용하여 모델 파라미터 $$\theta$$를 다음과 같은 최적화를 통해서 학습을 시키면 된다.

$$
\widehat{\theta} = \arg \max_{\theta} p(f_{\theta}(\mathbf{x}) \vert \mathbf{x})
$$

기존의 NMT 연구는 RNN Encoder-Decoder를 이용하는 방식으로 많이 수행이 되었는데 이번에 소개하려는 논문에서는 이러한 RNN Encoder-Decoder 모델을 Attention Mechanism을 통해서 많은 개선을 이루어냈다.

논문에서 주장하는 개선 사항은 다음과 같다. 기존 RNN Encoder-Decoder는 소스 문장을 고정된 길이의 Vector로 인코딩을 하였지만 제안된 모델에서는 인코딩을 Vector들의 Sequence로 인코딩을 하여서 소스 문장의 정보가 Sequence에 쫙 펼쳐지게 되며 이것을 디코더가 스스로 어떤 Vector에 중점을 둬서 정보를 취할지 선택할 수 있게 하였다. 이 과정이 Attention Mechanism이며 이것을 처음 제안한 논문이 바로 이 논문이다.

