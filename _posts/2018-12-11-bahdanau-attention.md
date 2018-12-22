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

또한 다음의 링크도 참고하여 작성하였다.
- [링크2](https://www.tensorflow.org/api_docs/python/tf/contrib/seq2seq/AttentionWrapper)

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

논문에서 주장하는 개선 사항은 다음과 같다. 기존 RNN Encoder-Decoder는 소스 문장을 고정된 길이의 벡터로 인코딩을 하였지만 제안된 모델에서는 인코딩을 벡터들의 Sequence로 인코딩을 하여서 소스 문장의 정보가 Sequence에 쫙 펼쳐지게 되며 이것을 디코더가 스스로 어떤 벡터에 중점을 둬서 정보를 취할지 선택할 수 있게 하였다. 이 과정이 Attention Mechanism이며 이것을 처음 제안한 논문이 바로 이 논문이다.

## RNN Encoder-Decoder
NMT의 가장 기본적인 접근은 RNN Encoder-Decoder 모델을 이용하는 것이다. RNN Encoder-Decoder 모델은 RNN 셀을 이용하여 인코더 및 디코더를 구성하고 인코더는 번역을 하고자 하는 소스 문장을 특정 임베딩 벡터로 인코딩을 하고 디코더는 임베딩된 벡터를 타겟 언어로 번역을 하여 타겟 문장을 생성해 내는 역할을 수행하게 된다.

Encoder-Decoder 모델은 기본적으로 다음의 역할을 수행하게 된다. 현재 타임 스텝의 디코더 아웃풋 단어 $$y_i$$를 생성하기 위해서 인코더에 입력되는 소스 문장 $$\mathbf{x}$$와 이전 타임 스텝 디코더 아웃풋 단어들인 $$\{y_0, \cdots, y_{i-1}\}$$이 Conditioning이 된 조건부 확률 모델 $$p(y_i \vert y_0,\cdots, y_{i-1}, \mathbf{x})$$를 모델링해야 하며 이것은 아래와 같이 기본 RNN 연산을 이용하여 모델링될 수 있다.

$$
\begin{align*}
p(y_i \vert y_0,\cdots, y_{i-1}, \mathbf{x}) & = g(y_{i-1}, s_i) \\
s_i & = f(y_{i-1}, s_{i-1})
\end{align*}
$$

$$
\begin{align*}
\text{where} \ y_0 & = \text{Enc}(\mathbf{x}) \\
s_0 & = h_{T_{\mathbf{x}}}
\end{align*}
$$

여기서 $$s_i$$는 디코더 RNN의 Hidden State Vector이며 $$y_0$$는 인코더가 최종적으로 생성한 문장 임베딩이다. 즉, 디코더 RNN은 입력으로 이전 타임 스텝의 인코더 아웃풋을 받는 구조라고 할 수 있다. 위의 모델을 그림으로 그리면 아래와 같다.

![](/assets/img/2018-12-11-tacotron/02.png)


```python
import tensorflow as tf
import hyparams as hp

enc_outs = encoder(inputs)

cell = tf.nn.rnn_cell.GRUCell(num_units=hp.attention_units)
attn_mechanism = tf.contrib.seq2seq.BahdanauAttention(num_units=hp.attention_depth,
                                                      memory=enc_outs)
attn_cell = tf.contrib.seq2seq.AttentionWrapper(cell=attn_cell,
                                                attention_mechanism=attention_mechanism,
                                                alignment_history=True,
                                                output_attention=False)
```

```python
class AttentionWrapper(rnn_cell_impl.RNNCell):
  """Wraps another `RNNCell` with attention.
  """

  def __init__(self,
               cell,
               attention_mechanism,
               attention_layer_size=None,
               alignment_history=False,
               cell_input_fn=None,
               output_attention=True,
               initial_cell_state=None,
               name=None,
               attention_layer=None):
... (생략)
    Args:
... (생략)
      cell_input_fn: (optional) A `callable`.  The default is:
        `lambda inputs, attention: array_ops.concat([inputs, attention], -1)`.
... (생략)
```
