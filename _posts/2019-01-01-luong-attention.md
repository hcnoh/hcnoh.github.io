---
layout: post
use_math: true
title: "Luong Attention 개념 정리"
date: 2019-01-01 13:43:13
tagline: "Luong Attention에 대해서 스터디하여 정리"
categories:
- Deep Learning
tags:
- deep learning
- tensorflow
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-01-luong-attention
---

지난번에 Bahdanau Attention에 대한 포스팅을 작성하였으며 그 후속으로 이번에는 Luong Attention에 대한 내용을 정리하게 되었다. Bahdanau Attention에 대한 포스팅은 다음의 [링크](https://hcnoh.github.io/2018-12-11-bahdanau-attention)를 통해서 확인할 수 있다.

또한 이번 포스팅은 다음의 논문을 스터디하여 정리하였다:
- [링크1](https://arxiv.org/pdf/1508.04025)

## Notation
이전 포스팅에 이어서 이번 포스팅에서도 다음의 Notation을 사용할 것이다.
- $$\mathcal{X} = (\mathbf{x}_t)_{t=1}^{T_{\mathbf{x}}} \in \mathbb{R}^{n \times T_{\mathbf{x}}}$$: 소스 문장의 단어 One-Hot 인코딩 시퀀스
- $$\mathcal{Y} = (\mathbf{y}_t)_{t=1}^{T_{\mathbf{y}}} \in \mathbb{R}^{m \times T_{\mathbf{y}}}$$: 타겟 문장의 단어 One-Hot 인코딩 시퀀스
- $$T_{\mathbf{x}}, T_{\mathbf{y}}$$: 각각 $$\mathcal{X}, \mathcal{Y}$$의 시퀀스 길이 (문장의 길이)
- $$\mathbf{x}_t, \mathbf{y}_t$$: 각각 $$t$$번째 타임 스텝 단어의 One-Hot 인코딩
- $$\widehat{\mathcal{Y}} = (\widehat{\mathbf{y}}_t)_{t=1}^{T_{\mathbf{y}}}$$: 모델이 타겟 문장의 단어 One-Hot 인코딩 시퀀스를 추정하기 위해서 사용하는 확률 모델 $$\widehat{\mathbf{y}}_t$$의 시퀀스

## Bahdanau Attention으로부터 달라진 점
논문에서 제시하는 Bahdanau Attention과의 차이점은 다음과 같이 정리할 수 있다.
- 디코더의 Hidden State Vector를 구하는 방식이 간소화되었고 결과적으로 Attention Mechanism의 Computation Path가 간소화되었다.
- Local Attention과 그것을 위한 Alignment Model을 제시하였다.
- 다양한 Score Function들을 제시하였고 그들 각각을 비교해 보았다.

사실 이 논문에서 주된 내용은 Hidden State Vector를 구하는 방식이 달라졌다는 점과 Local Attention을 사용했다는 점 말고는 특이한 점은 없다. 특히 다양한 Score Function을 제시하였다는 내용은 너무도 마이너해보인다. 아마 비슷한 시기에 비슷한 내용으로 논문을 준비하다보니 벌어진 현상으로 보인다. 어쨌든 Hidden State Vector를 어떻게 구했는지, 그리고 Local Attention이 무엇인지를 중점적으로 보면 될 듯 싶다.

## Hidden State Vector

## Local Attention과 그것을 위한 Alignment Model

$$
\mathbf{a}_t = \text{Softmax}\left(\left(\text{Score}(\mathbf{s}_{t-1}, \mathbf{h}_j) \exp\left( -\frac{(j-p_t)^2}{2\sigma^2} \right) \right)_{j=p_t-D}^{p_t+D}\right) \in \mathbb{R}^{2D+1}
$$

$$
\begin{align*}
\text{where} \ \sigma = \frac{D}{2}
\end{align*}
$$

$$
p_t = t
$$

$$
p_t = T_{\mathbf{x}} \cdot \text{Sigmoid}\left(\mathbf{v}_p^\text{T} \tanh(\mathbf{W}_p \mathbf{s}_t)\right)
$$

$$
\begin{align*}
\text{Score}(\mathbf{s}_t, \mathbf{h}_j)
& = \mathbf{s}_t^\text{T}\mathbf{h}_j
& dot \\
\text{Score}(\mathbf{s}_t, \mathbf{h}_j)
& = \mathbf{s}_t^\text{T}\mathbf{W_ah}_j
& general \\
\text{Score}(\mathbf{s}_t, \mathbf{h}_j)
&= \mathbf{v_a}^\text{T}\tanh(\mathbf{W_as}_t + \mathbf{U_a h}_j)
& concat \\
\text{Score}(\mathbf{s}_t, \mathbf{h}_j)
& = \text{Softmax}(\mathbf{W_as}_t)
& location
\end{align*}
$$

$$
\begin{align*}
\widehat{\mathbf{y}}_t
& = \text{Softmax}\left( \mathbf{W_y}\tilde{\mathbf{s}}_t + \mathbf{b_y} \right) \\
\tilde{\mathbf{s}}_t
& = \tanh(\mathbf{W_{ss}}\mathbf{s}_t + \mathbf{W_{cs}}\mathbf{c}_t + \mathbf{b_s})
\end{align*}
$$
