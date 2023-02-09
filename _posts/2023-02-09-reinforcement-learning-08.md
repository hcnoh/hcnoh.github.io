---
layout: post
use_math: true
title: "[Reinforcement Learning] 8. Trust Region Policy Optimization (4)"
date: 2023-02-09 00:00:00
tagline: "Trust Region 방법을 이용하여 Policy Gradient의 성능을 향상시킨 알고리즘인 Trust Region Policy Optimization에 대해서 정리"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2023-02-09-reinforcement-learning-08
---

이번 포스트에서는 이전 포스트와 마찬가지로 `Trust Region Policy Optimization`(TRPO)를 설명하기 위한 이론적 배경들을 설명하도록 한다. 이전 포스트에서 다루었던 Lemma들과 `Pinsker's Inequality`를 바탕으로 이번 포스트에서는 TRPO의 중요한 정리를 먼저 다루고 이 정리를 바탕으로 `Minorization-maximization`(MM) 알고리즘을 정리하여 TRPO의 기본적인 형태를 다루게 된다.

## Minorization-maximization 알고리즘을 위한 정리
먼저 다음의 정리를 살펴보자.

**Theorem 1)**
$$
\eta(\tilde{\pi}) \geq L_{\pi_\text{old}}(\tilde{\pi}) - CD_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}).
$$

여기서:

$$
\begin{array}{rl}
C
& = \frac{4 \epsilon \gamma}{(1 - \gamma)^2} \\
D_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi})
& = \max_s D_\text{KL}(\pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s)), \\
\epsilon
& = \max_{s, a} \left\vert A_{\pi_\text{old}}(s, a) \right\vert.
\end{array}
$$


## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)

## 수정 사항
- 2023.02.09
    - 최초 게제