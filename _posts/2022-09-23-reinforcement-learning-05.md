---
layout: post
use_math: true
title: "[Reinforcement Learning] 5. Trust Region Policy Optimization (1)"
date: 2022-09-23 00:00:00
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
permalink: /2022-09-23-reinforcement-learning-05
---

## 정책 변화에 따른 목적 함수의 변화
[강화 학습 2편 포스트](https://hcnoh.github.io/2022-07-02-reinforcement-learning-02)에서 강화 학습의 `목적 함수`(Objective Function) $$\eta(\pi)$$를 우리는 Expected Return으로 정의하기로 했었다:

$$
\eta(\pi) = \mathbb{E}_{\tau \sim \pi} \left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right].
$$

여기서 기존 정책인 $$\pi_\text{old}$$가 $$\tilde{\pi}$$로 변화하였을 경우 목적 함수는 어떻게 될지에 대해서 생각해보자. 즉, $$\eta(\pi_\text{old})$$와 $$\eta(\pi_\text{old})$$ 사이에는 어떤 차이가 있을까? 아래의 정리는 $$\eta(\tilde{\pi})$$를 $$\eta(\pi_\text{old})$$로 표현한 정리이다:

$$
\eta(\tilde{\pi}) = \eta(\pi_\text{old}) + \mathbb{E}_{\tau \sim \tilde{\pi}} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_\text{old}}(s_t, a_t) \right].
$$

즉, 새로운 정책에 대한 목적 함수 $$\eta(\tilde{\pi})$$는 기존 정책에 대한 목적 함수 $$\eta(\pi_\text{old})$$ 대비 기존 정책에 대한 이득 함수 $$A_{\pi_\text{old}}$$의 총 합의 기대값만큼의 변화가 있다는 의미이다. 물론 이 기대값은 새로운 정책 $$\tilde{\pi}$$를 Sampling Distribution으로 한다.

이에 대한 증명은 다음과 같다. 먼저 $$\eta(\tilde{\pi})$$를 살펴보면:

$$
\eta(\tilde{\pi}) = \mathbb{E}_{s_0 \sim \rho_0} \left[ V_{\tilde{\pi}}(s_0) \right].
$$

여기서 $$V_{\tilde{\pi}}(s_0)$$는 다음과 같이 정리할 수 있다:

$$
\begin{array}{rl}
V_{\tilde{\pi}}(s_0)
& = Q_{\tilde{\pi}}(s_0, a_0) - A_{\tilde{\pi}}(s_0, a_0) \\
& = r(s_0, a_0) + \mathbb{E}_{s_1 \sim p(\cdot \vert s_0, a_0)} \left[ \gamma V_{\tilde{\pi}}(s_1) \right] - A_{\tilde{\pi}}(s_0, a_0) \\
& = \mathbb{E}_{s_1 \sim p(\cdot \vert s_0, a_0)} \left[ r(s_0, a_0) + \gamma V_{\tilde{\pi}}(s_1) - A_{\tilde{\pi}}(s_0, a_0) \right].
\end{array}
$$

또한 아래의 정리도 확인할 수 있다:

$$
\begin{array}{l}
\sum_{t=0}^\infty \gamma^t A_{\pi_{\text{old}}}(s_t, a_t) \\
= \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ r(s_t, a_t) + \gamma V_{\pi_{\text{old}}}(s_{t+1}) - V_{\pi_{\text{old}}}(s_t) \right] \\
= \mathbb{E}_{s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ \sum_{t=0}^\infty \gamma^t \left( r(s_t, a_t) + \gamma V_{\pi_{\text{old}}}(s_{t+1}) - V_{\pi_\text{old}}(s_t) \right) \right].
\end{array}
$$

이들을 통해서 다음을 유도할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \tilde{\pi}} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_\text{old}}(s_t, a_t) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0, a_t \sim \tilde{\pi}(\cdot \vert s_t), s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_\text{old}}(s_t, a_t) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0, a_t \sim \tilde{\pi}(\cdot \vert s_t), s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ + \mathbb{E}_{s_0 \sim \rho_0, a_t \sim \tilde{\pi}(\cdot \vert s_t), s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ \sum_{t=1}^\infty \gamma^t V_{\pi_\text{old}}(s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ - \mathbb{E}_{s_0 \sim \rho_0, a_t \sim \tilde{\pi}(\cdot \vert s_t), s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ \sum_{t=0}^\infty \gamma^t V_{\pi_\text{old}}(s_t) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0} \left[ V_{\tilde{\pi}}(s_0) \right] - \mathbb{E}_{s_0 \sim \rho_0, a_t \sim \tilde{\pi}(\cdot \vert s_t), s_{t+1} \sim p(\cdot \vert s_t, a_t)} \left[ V_{\pi_\text{old}}(s_0) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0} \left[ V_{\tilde{\pi}}(s_0) \right] - \mathbb{E}_{s_0 \sim \rho_0} \left[ V_{\pi_\text{old}}(s_0) \right] \\
= \eta(\tilde{\pi}) - \eta(\pi_\text{old}). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

이 정리에서 새로운 정책 $$\tilde{\pi}$$의 목적 함수 $$\eta(\tilde{\pi})$$는 `할인된 방문 빈도`(Discounted Visitation Frequency)라는 개념을 활용하여 다르게 표현될 수 있다. 먼저 $$\tilde{\pi}$$의 할인된 방문 빈도 $$\rho_{\tilde{\pi}}(s)$$는 다음과 같이 정의한다:

$$
\rho_{\tilde{\pi}}(s) = \sum_{t=0}^\infty \gamma^t p(s_t=s \vert \tilde{\pi}).
$$

이를 통해서 $$\eta(\tilde{\pi})$$는 다음과 같이 정리할 수 있다:

$$
\eta(\tilde{\pi}) = \eta(\pi_\text{old}) + \sum_s \rho_{\tilde{\pi}}(s) \sum_a \tilde{\pi}(s \vert s) A_{\pi_\text{old}}(s, a).
$$

이는 또한 $$\bar{A} = \sum_a \tilde{\pi}(a \vert s) A_{\pi_\text{old}}(s, a)$$를 통해서 다음과 같이 정리할 수도 있다:

$$
\eta(\tilde{\pi}) = \eta(\pi_\text{old}) + \sum_s \rho_{\tilde{\pi}}(s) \bar{A}(s).
$$

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)

## 수정 사항
- 2022.09.23
    - 최초 게제