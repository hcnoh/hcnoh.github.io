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

이를 증명하기 위해서는 이전 [포스트](https://hcnoh.github.io/2023-01-02-reinforcement-learning-07)에서 다뤘던 Lemma 1과 Lemma 2, 그리고 `Pinsker's Inequality`에 대한 내용들이 필요하다. 먼저 임의의 정책 $$\pi_\text{old}$$와 $$\tilde{\pi}$$에 대해서 적절한 결합 분포를 선택하여 다음을 만족할 수 있다:

$$
D_\text{TV}^\text{max}(\pi_\text{old}, \tilde{\pi}) = \max_s D_\text{TV}\left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) = \max_s p(a \neq \tilde{a} \vert \pi_\text{old}, \tilde{\pi}, s) = \alpha.
$$

따라서 $$\pi_\text{old}$$와 $$\tilde{\pi}$$는 $$\alpha$$로 연관된 정책 짝이다.

이제 이를 바탕으로 다음을 살펴보자:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \\
= \sum_s \rho_{\pi_\text{old}}(s) \sum_a \tilde{\pi}(a \vert s) A_{\pi_\text{old}}(s, a) - \sum_s \rho_{\tilde{\pi}}(s) \sum_a \tilde{\pi}(a \vert s) A_{\pi_\text{old}}(s, a) \\
= \sum_s \rho_{\pi_\text{old}}(s) \bar{A}(s) - \sum_s \rho_{\tilde{\pi}}(s) \bar{A}(s) \\
= \sum_s \sum_{t = 0}^\infty \gamma^t p(s_t = s \vert \pi_\text{old}) \bar{A}(s) - \sum_s \sum_{t = 0}^\infty \gamma^t p(s_t = s \vert \tilde{\pi}) \bar{A}(s) \\
= \sum_{t = 0}^\infty \gamma^t \left( \mathbb{E}_{s_t \sim \pi_\text{old} } \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] \right) \\
\ \ \ \ \leq \sum_{t = 0}^\infty \gamma^t \left\vert \mathbb{E}_{s_t \sim \pi_\text{old} } \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] \right\vert
\end{array}
$$

여기서 $$\pi_\text{old}$$와 $$\tilde{\pi}$$는 $$\alpha = D_\text{TV}^\text{max}(\pi_\text{old}, \tilde{\pi})$$로 연관된 정책 짝이므로 다음과 같이 쓸 수 있다:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \\
\ \ \ \ \leq \sum_{t = 0}^\infty \gamma^t 4\alpha \left( 1 - (1 - \alpha)^t \right) \max_{s, a} \left\vert A_{\pi_\text{old}} (s, a) \right\vert \\
\ \ \ \  \ \ \ \ = \sum_{t = 0}^\infty 4\alpha \left( \gamma^t - (\gamma - \gamma \alpha)^t \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \left( \frac{1}{1 - \gamma} - \frac{1}{1 - \gamma + \gamma\alpha} \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \left( \frac{1}{1 - \gamma} - \frac{1}{1 - \gamma(1 - \alpha)} \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \cdot \frac{\gamma \alpha}{(1 - \gamma)(1 - \gamma(1 - \alpha))} \cdot \epsilon \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq 4\alpha \cdot \frac{\gamma \alpha}{(1 - \gamma)^2} \cdot \epsilon \\
\ \ \ \  \ \ \ \  \ \ \ \  \ \ \ \ = \frac{4\epsilon \gamma}{(1 - \gamma)^2} \cdot \alpha^2.
\end{array}
$$

여기서 Pinsker's Inequality를 통해서 다음을 유도할 수 있다:

$$
\begin{array}{l}
\alpha^2 = \max_s D_\text{TV}^2 \left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) \\
\ \ \ \  \leq \max_s \frac{1}{2} D_\text{KL}\left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) \\
\ \ \ \  \ \ \ \ \leq \max_s D_\text{KL} \left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) = D_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}).
\end{array}
$$

따라서:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \leq CD_\text{KL}^\text{max} (\pi_\text{old}, \tilde{\pi}) \\
\ \ \ \ \Longrightarrow \eta(\tilde{\pi}) \geq L_{\pi_\text{old}}(\tilde{\pi}) - CD_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)

## 수정 사항
- 2023.02.09
    - 최초 게제
- 2023.02.10
    - 증명 정리