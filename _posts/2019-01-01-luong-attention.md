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

$$
\mathbf{a}_t = \text{Softmax}\left(\left(\text{Score}(\mathbf{s}_{t-1}, \mathbf{h}_j) \exp\left( -\frac{(j-p_t)^2}{2\sigma^2} \right) \right)_{j=1}^{T_{\mathbf{x}}}\right) \in \mathbb{R}^{2D+1}
$$

$$
\begin{align*}
\text{where} \ \sigma \ \frac{D}{2}
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
