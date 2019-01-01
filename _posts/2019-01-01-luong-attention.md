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
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-01-luong-attention
---


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
