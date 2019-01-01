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
기계 번역은 이전부터 확률적인 접근 방법을 통해서 수행이 되어왔다. 간단히 설명하면 소스 문장 $$\mathcal{X}$$을 Conditioning하여 조건부 확률 $$p(\mathcal{Y} \vert \mathcal{X})$$를 최대화하는 타겟 문장 $$\mathcal{Y}$$를 찾는 것이다. 수식으로 정리하면 아래와 같다. $$\widehat{\mathcal{Y}}$$는 모델의 타겟 문장 $$\mathcal{Y}$$에 대한 추정 문장이다.

$$
\widehat{\mathcal{Y}} = \arg \max_{\mathcal{Y}} p(\mathcal{Y} \vert \mathcal{X})
$$

최근 딥러닝을 이용한 연구가 활발히 진행되면서 뉴럴 네트워크를 통한 언어 번역을 시도해 보려는 NMT(Neural Machine Translation)에 관한 연구가 각광을 받게 되었다. NMT는 딥러닝 모델 $$f_{\theta}(\mathcal{X})$$를 학습시키기 위해서 Loss $$\mathcal{L}$$을 다음과 같이 사용하게 된다.

$$
\mathcal{L} = -p(f_{\boldsymbol{\theta}}(\mathcal{X}) \vert \mathcal{X})
$$

즉, 이 Loss를 이용하여 모델 파라미터 $$\boldsymbol{\theta}$$를 다음과 같은 최적화를 통해서 학습을 시키면 된다.

$$
\widehat{\boldsymbol{\theta}} = \arg \max_{\boldsymbol{\theta}} p(f_{\boldsymbol{\theta}}(\mathcal{X}) \vert \mathcal{X})
$$

기존의 NMT 연구는 RNN Encoder-Decoder를 이용하는 방식으로 많이 수행이 되었는데 이번에 소개하려는 논문에서는 이러한 RNN Encoder-Decoder 모델을 Attention Mechanism을 통해서 많은 개선을 이루어냈다.

논문에서 주장하는 개선 사항은 다음과 같다. 기존 RNN Encoder-Decoder는 소스 문장을 고정된 길이의 벡터로 인코딩을 하였지만 제안된 모델에서는 인코딩을 벡터들의 Sequence로 인코딩을 하여서 소스 문장의 정보가 Sequence에 쫙 펼쳐지게 되며 이것을 디코더가 스스로 어떤 벡터에 중점을 둬서 정보를 취할지 선택할 수 있게 하였다. 이 과정이 Attention Mechanism이며 이것을 처음 제안한 논문이 바로 이 논문이다.

## Notation
이번 포스팅에서는 다음의 Notation을 사용할 것이다.
- $$\mathcal{X} = (\mathbf{x}_t)_{t=1}^{T_{\mathbf{x}}} \in \mathbb{R}^{n \times T_{\mathbf{x}}}$$: 소스 문장의 단어 One-Hot 인코딩 시퀀스
- $$\mathcal{Y} = (\mathbf{y}_t)_{t=1}^{T_{\mathbf{y}}} \in \mathbb{R}^{m \times T_{\mathbf{y}}}$$: 타겟 문장의 단어 One-Hot 인코딩 시퀀스
- $$T_{\mathbf{x}}, T_{\mathbf{y}}$$: 각각 $$\mathcal{X}, \mathcal{Y}$$의 시퀀스 길이 (문장의 길이)
- $$\mathbf{x}_t, \mathbf{y}_t$$: 각각 $$t$$번째 타임 스텝 단어의 One-Hot 인코딩
- $$\widehat{\mathcal{Y}} = (\widehat{\mathbf{y}}_t)_{t=1}^{T_{\mathbf{y}}}$$: 모델이 추정한 타겟 문장의 단어 One-Hot 인코딩 시퀀스

## RNN Encoder-Decoder
NMT의 가장 기본적인 접근은 RNN Encoder-Decoder 모델을 이용하는 것이다. RNN Encoder-Decoder 모델은 RNN 셀을 이용하여 인코더 및 디코더를 구성하고 인코더는 번역을 하고자 하는 소스 문장을 특정 임베딩 벡터로 인코딩을 하고 디코더는 임베딩된 벡터를 타겟 언어로 번역을 하여 타겟 문장을 생성해 내는 역할을 수행하게 된다.

Encoder-Decoder 모델은 기본적으로 다음의 역할을 수행하게 된다. 모델이 현재 타임 스텝의 디코더 아웃풋 단어 One-Hot 인코딩 $$\mathbf{y}_t$$를 추정하기 위해서 인코더에 입력되는 소스 문장 $$\mathcal{X}$$와 이전 타임 스텝 디코더 아웃풋 단어 One-Hot 인코딩들인 $$\{\mathbf{y}_0, \cdots, \mathbf{y}_{t-1}\}$$이 Conditioning이 된 조건부 확률 모델 $$p(\mathbf{y}_t \vert \mathbf{y}_0,\cdots, \mathbf{y}_{t-1}, \mathbf{x})$$를 모델링해야 하며 이것은 아래와 같이 기본 RNN 연산을 이용하여 모델링될 수 있다.

$$
\begin{align*}
\widehat{\mathbf{y}}_t
& = p(\mathbf{y}_t \vert \mathbf{y}_0,\cdots, \mathbf{y}_{t-1}, \mathcal{X}) \\
& = \text{Softmax}\left( \mathbf{W_y}\mathbf{s}_t + \mathbf{b_y} \right) \\
\mathbf{s}_t
& = \tanh(\mathbf{W_{ys}}\mathbf{y}_{t-1} + \mathbf{W_{ss}}\mathbf{s}_{t-1} + \mathbf{b_s})
\end{align*}
$$

$$
\begin{align*}
\text{where} \
\mathbf{y}_0 & = \text{Enc}(\mathcal{X}) \\
\mathbf{s}_0 & = \mathbf{h}_{T_{\mathbf{x}}}
\end{align*}
$$

여기서 $$\mathbf{s}_t$$는 $$t$$번째 타임 스텝의 디코더 RNN Hidden State Vector이며 $$\mathbf{y}_0$$는 인코더가 최종적으로 생성한 문장 임베딩이다. 즉, 디코더 RNN은 입력으로 이전 타임 스텝의 인코더 아웃풋을 받는 구조라고 할 수 있다. 위의 모델을 그림으로 그리면 아래와 같다.

![](/assets/img/2018-12-11-tacotron/02.png)

디코더의 역할은 인코더가 생성한 소스 문장의 임베딩 벡터를 이용하여 타겟 언어의 문장으로 번역된 타겟 문장을 생성하는 것이다. 그렇다면 인코더의 역할은 소스 문장을 적절한 임베딩 벡터로 변환하는 것이라고 할 수 있다. 인코더도 마찬가지로 RNN 구조를 가지고 있으며 기본적으로 문장 임베딩은 소스 문장의 마지막 입력인 \<EOS\>, 즉 End of Sentence가 입력된 마지막 출력 벡터를 문장 임베딩 벡터로 사용하게 된다. 또한 마지막 타임 스텝의 인코더 RNN Hidden State Vector $$\mathbf{h}_{T_{\mathbf{x}}}$$는 디코더의 첫번째 타임 스텝의 Hidden State Vector $$\mathbf{s}_0$$로 들어가게 된다.
  
## 기존 Encoder-Decoder 모델의 단점: Attention Mechanism으로 극복
이러한 기존 모델의 단점은 Bahdanau Attention 논문에서 주장하는대로 문장 임베딩을 고정된 길이로만 해야 한다는 점이다. 이 경우 짧은 문장에서는 큰 문제가 없을 수도 있지만 문장이 길어질수록 더 많은 정보를 고정된 길이로 더 많이 압축해야 하기 때문에 정보의 손실이 있다는 점이 가장 큰 문제라고 볼 수 있다. 추가적으로 RNN 특유의 Long Term Dependency 문제가 발생할 수도 있겠지만 이건 인코더 RNN을 Bidirectional로 구성하면 해결할 수 있는 문제라 여기서는 따로 언급하지 않도록 하겠다.

어쨌든 결과적으로 Attention 메커니즘을 통한 보완이 가능하다고 주장한다. Attention 메커니즘을 이용하면 인코더가 고정된 길이의 문장 임베딩을 할 필요가 없으며 소스 문장의 벡터의 시퀀스를 이용하여 디코더가 디코딩이 가능하게 된다. 따라서 문장의 길이에 관계없이 Dynamic하게 정보를 인코딩이 가능하게 된다. Attention 메커니즘을 이용하여 조건부 확률 모델 $$p(y_i \vert y_0,\cdots, y_{i-1}, \mathbf{x})$$를 모델링하면 아래와 같다.

$$
\begin{align*}
p(y_i \vert y_0,\cdots, y_{i-1}, \mathbf{x}) & = g(y_{i-1}, s_i, c_i) \\
s_i & = f(y_{i-1}, s_{i-1}, c_i)
\end{align*}
$$

$$
\begin{align*}
\text{where} \ y_0 & = \text{<Go> Token}, \\
s_0 & = \mathbf{0} \\
c_i & = \text{Attn}(s_{i-1}, [h_1, \cdots, h_{T_{\mathbf{x}}}])
\end{align*}
$$

여기서 달라진 점은 $$y_0$$와 $$s_0$$, 그리고 새롭게 Context Vector $$c_i$$가 추가된 것들을 확인할 수 있다. $$y_0$$는 기존과 다르게 문장 임베딩을 사용하지 않고 문장의 시작점을 나타내는 새로운 \<Go> 토큰을 사용하게 되며 $$s_0$$는 평범한 RNN처럼 Zero Vector를 사용하게 된다. 여기서 핵심은 $$c_i$$를 어떻게 구하며 또 활용할 것이냐가 될 것이다. 방금까지의 설명을 그림으로 정리하면 아래와 같다.

![](/assets/img/2018-12-11-tacotron/03.png)

## Bahdanau Attention
일단 $$c_i$$를 구하는 연산이 바로 Attention 메커니즘이 수행하는 일이 될 것이다. Bahdanau Attention에서 $$c_i$$는 다음과 같이 구할 수 있다.

$$
\begin{align*}
c_i & = \sum_{j=1}^{T_{\mathbf{x}}} \alpha_{ij}h_j \\
\alpha_{ij} & = \frac{\exp(e_{ij})}{\sum_{k=1}^{T_{\mathbf{x}}}\exp(e_{ik})} \\
e_{ij} & = a(s_{i-1}, h_j)
\end{align*}
$$

$$h_j$$는 $$T_{\mathbf{x}}$$ 길이의 소스 문장의 $$j$$번째 인코더 RNN Hidden State Vector이다. 또한 여기서 $$a$$는 Alignment Model이라고 부르게 된다. $$a(s_{i-1}, h_j)$$는 $$s_{i-1}$$과 $$h_j$$ 사이의 연관성을 Scoring하는 Score Function이라고 할 수 있다. 논문에서 사용한 Alignment Model은 다음과 같다.

$$
a(s_{i-1}, h_j) = v_a^T \tanh(W_a s_{i-1} + U_a h_j)
$$

사실 두 벡터 $$s_{i-1}$$과 $$h_j$$ 사이의 Similarity를 구한다는 관점에서 봤을 경우 $$W_a s_{i-1} - U_a h_j$$라고 쓰는 것이 더 직관적일 것 같기는 하다. $$W_a$$와 $$U_a$$라는 두 Linear Transformation을 통해서 임베딩 공간에 뿌려진 두 벡터 $$W_a s_{i-1}$$과 $$U_a h_j$$ 사이의 거리를 $$W_a s_{i-1} - U_a h_j$$라고 정의할 수도 있기 때문이다. 어쨌든 그건 부호의 차이일 뿐이니 여기서는 큰 의미는 없다.

또한 $$\alpha_{ij}$$는 Softmax Function이라는 점을 주목하자면 다음과 같이 정리할 수 있을 것이다. 먼저 현재 Context Vector $$c_i$$를 구하기 위해서 이전 타임 스텝의 디코더 RNN Hidden State Vector $$s_{i-1}$$과 인코더 RNN Hidden State Vector들인 $$\{h_1, \cdots, h_{T_{\mathbf{x}}} \}$$들 사이의 Score 벡터인 $$\mathbf{e}_i = [e_{i1}, \cdots, e_{iT_{\mathbf{x}}}]$$를 구하게 된다. 이 벡터에 Softmax를 취하여 Alignment $$\mathbf{a}_i = [\alpha_{i1}, \cdots, \alpha_{iT_{\mathbf{x}}}]$$를 구하여서 Matrix $$H=[h_1,\cdots, h_{T_{\mathbf{x}}}]$$와 아래와 같이 곱해서 Context Vector $$c_i$$를 구하게 된다. 그 외 다른 정의들도 함께 정리하였다.

$$
c_i = H \mathbf{a}_i^T
$$

$$
\begin{align*}
\text{where} \ H & = [h_1,\cdots, h_{T_{\mathbf{x}}}], \\
\mathbf{a}_i & = [\alpha_{i1}, \cdots, \alpha_{iT_{\mathbf{x}}}] \\
& = \text{Softmax}(\mathbf{e}_i), \\
\mathbf{e}_i & = [e_{i1}, \cdots, e_{iT_{\mathbf{x}}}], \\
e_{ij} & = a(s_{i-1}, h_j) \\
& = v_a^T \tanh(W_a s_{i-1} + U_a h_j)
\end{align*}
$$

$$
\begin{align*}
g(y_{i-1}, s_i) & = \text{Softmax}(W_ys_i + b_y) \\
f(y_{i-1}, s_{i-1}) & = \tanh(W_{ys}y_{i-1} + W_{ss}s_{i-1} + b_s)
\end{align*}
$$

$$
\begin{align*}
g(y_{i-1}, s_i, c_i) & = \text{Softmax}(W_ys_i + b_y) \\
f(y_{i-1}, s_{i-1}, c_i) & = \tanh(W_{ys}y_{i-1} + W_{ss}s_{i-1} + W_{cs}c_i + b_s)
\end{align*}
$$

$$
\begin{align*}
g(y_{i-1}, s_i) & = \text{Softmax}(W_ys_i + b_y) \\
f(y_{i-1}, s_{i-1}) & = z_i \odot s_{i-1} + (1-z_i)\odot \tilde{s}_i \\
z_i & = \sigma(W_z y_{i-1} + U_z s_{i-1} +b_z) \\
r_i & = \sigma(W_r y_{i-1} + U_r s_{i-1} + b_r) \\
\tilde{s}_i & = \tanh(W_s y_{i-1} + U_s (r_i \odot s_{i-1}) + b_s)
\end{align*}
$$

$$
\begin{align*}
g(y_{i-1}, s_i, c_i) & = \text{Softmax}(W_ys_i + b_y) \\
f(y_{i-1}, s_{i-1}, c_i) & = z_i \odot s_{i-1} + (1-z_i)\odot \tilde{s}_i \\
z_i & = \sigma(W_z y_{i-1} + U_z s_{i-1} + C_zc_i + b_z) \\
r_i & = \sigma(W_r y_{i-1} + U_r s_{i-1} + C_rc_i + b_r) \\
\tilde{s}_i & = \tanh(W_s y_{i-1} + U_s (r_i \odot s_{i-1}) + C_sc_i + b_s)
\end{align*}
$$

$$
[W : C][y_{i-1}^T : c_i^T]^T = Wy_{i-1} + Cc_i
$$

$$
\begin{align*}
g(y_{i-1}, s_i, c_i) & = \text{Softmax}(W_y \tilde{s}_i + b_y) \\
\tilde{s}_i & = \tanh(W_{ss} s_i + W_{cs} c_i + b_s)
\end{align*}
$$

$$
\alpha_{ij} = a(s_i, h_j)\exp\left( -\frac{(j-p_i)^2}{2\sigma^2} \right)
$$

$$
p_i = i
$$

$$
p_i = S \cdot \text{Sigmoid}(v_p^T \tanh(W_p s_i))
$$

$$
\begin{align*}
a(s_i, h_j) & = s_i^Th_j & dot \\
a(s_i, h_j) & = s_i^TW_ah_j & general \\
a(s_i, h_j) & = v_a^T\tanh(W_as_i + U_a h_j) & concat \\
a(s_i, h_j) & = \text{Softmax}(W_as_i) & location
\end{align*}
$$

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
