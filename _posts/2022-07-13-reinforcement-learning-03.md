---
layout: post
use_math: true
title: "[Reinforcement Learning] 3. Baseline Method & Actor-Critic Method"
date: 2022-07-13 00:00:00
tagline: "Policy Gradient의 성능 향상을 위해서 Baseline Method를 포함한 기본적인 방법론들을 다루고 이를 바탕으로 Actor-Critic Method에 대해서 기술"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-07-13-reinforcement-learning-03
---

이전 [포스트](https://hcnoh.github.io/2022-07-02-reinforcement-learning-02)에서는 강화 학습에서 가장 중요한 정리인 Policy Gradient Theorem을 중점적으로 다루었고 이를 바탕으로 기본적인 Policy Gradient 알고리즘인 REINFORCE 알고리즘과 이를 활용하기 위한 간단한 Gaussian Policy에 대한 내용을 다루었다. 이번 포스트에서는 이러한 Policy Gradient 방법들에서 사용하는 Estimator의 분산(Variance)을 줄여주는 방법들인 Baseline Method를 포함한 다양한 방법들을 알아보도록 한다.

## Baseline Method
Baseline Method란 Policy Gradient $$\nabla_\theta \eta(\pi_\theta)$$에 대한 Estimator의 분산을 줄여주는 기본적인 방법이다. 이전 포스트에서 소개한 Policy Gradient Estimator는 다음과 같다:

$$
\nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t.
$$

이를 Baseline $$b(s_t)$$를 사용하여 다음과 같이 써보자:

$$
\nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)).
$$

그러면 우리는 다음과 같은 결과를 유도할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\mathbb{E}_{a_t}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t) \right] \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\sum_{a_t}\nabla_\theta \pi_\theta(a_t \vert s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\nabla_\theta \sum_{a_t} \pi_\theta(a_t \vert s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right].
\end{array}
$$

여기서는 다음의 연산 결과를 사용했다:

$$
\nabla_\theta \sum_{a_t} \pi_\theta(a_t \vert s_t) = \nabla_\theta 1 = 0.
$$

즉, Baseline을 사용한 Estimator는 기존 Estimator와 마찬가지로 Policy Gradient에 대한 Unbiased Estimator인 것을 확인했다. 이 Estimator는 기존 Estimator의 분산을 줄여주는데 사용될 수 있다. 이는 다음의 연산 과정을 통해 확인할 수 있다:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
= \mathbb{E}_{\tau \sim \pi_\theta}\left[ \left( \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right)^2 \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \right)^2 \\
= \mathbb{E}_{\tau \sim \pi_\theta}\left[ \left( \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right)^2 \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)R_t \right] \right)^2 \\
= \mathbb{E}_{s_t, a_t}\left[ \left(\nabla_\theta\log\pi_\theta(a_t \vert s_t) \right)^2 \mathbb{E}_{s_{t+1:\infty, a_{t+1:\infty}}}\left[(R_t - b(s_t))^2\right] \right] \\
\ \ \ \ - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)R_t \right] \right)^2.
\end{array}
$$

만약 우리가 $$b(s_t)$$로 하여금 다음을 만족하게끔 정의한다면(항상 만족할 수 있는지 증명 필요):

$$
\mathbb{E}_{s_{t+1:\infty, a_{t+1:\infty}}}\left[ \left( R_t - b(s_t) \right)^2 \right] \leq \mathbb{E}_{s_{t+1:\infty, a_{t+1:\infty}}} \left[ R_t^2 \right],
$$

우리는 다음의 관계를 획득할 수 있다:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq \mathbb{E}_{s_t, a_t}\left[ \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right)^2 \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ R_t^2 \right] \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right] \right)^2 \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right].
\end{array}
$$

즉, Baseline은 Estimator의 분산을 줄여주게 된다:

$$
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \leq \text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right].
$$

미분 가능한 Baseline을 $$\hat{v}_w$$로 정의하자. 그러면 $$\hat{v}_w$$에 대한 Gradient는 다음과 같이 계산할 수 있다:

$$
\begin{array}{rl}
-\nabla_w \mathbb{E}_{s_t:\infty, a_{t:\infty}}\left[ (R_t - \hat{v}_w)^2 \right] & = -\nabla_w \mathbb{E}_{\tau \sim \pi_\theta}\left[ (R_t - \hat{v}_w(s_t))^2 \right] \\
& = -\mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_w (R_t - \hat{v}_w(s_t))^2 \right] \\
& = \mathbb{E}_{\tau \sim \pi_\theta}\left[ 2(R_t - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t) \right] \\
& \propto \mathbb{E}_{\tau \sim \pi_\theta}\left[ (R_t - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t) \right].
\end{array}
$$

## REINFORCE + Baseline Method
위의 결과를 바탕으로 Baseline Method를 도입한 REINFORCE 알고리즘은 다음과 같이 정리될 수 있다:

- REINFORCE + Baseline 알고리즘
    - 입력: 미분 가능한 매개변수화된 정책 $$\pi_\theta(a \vert s)$$, 미분 가능한 매개변수화된 State Value $$\hat{v}_w(s)$$.
    - 초매개변수: Learning Rate $$\alpha^\theta > 0, \alpha^w > 0$$.
    - 정책 매개변수 $$\theta$$ 및 State Value 가중치 $$w$$ 초기화.
    - 반복:
        - 정책 $$\pi_\theta$$를 따르며 에피소드 $$\tau=(s_0, a_0, s_1, a_1, \cdots)$$ 생성.
        - 에피소드의 각 단계 $$t=0,1,\cdots, T-1$$에 대해서:
        $$
        \begin{array}{rl}
        R_t & \longleftarrow \text{Return from step $t$} \\
        \delta & \longleftarrow R_t - \hat{v}_w(s_t) \\
        w & \longleftarrow w + \alpha^w \gamma^t \delta \nabla_w \hat{v}_w(s_t) \\
        \theta & \longleftarrow \theta + \alpha^\theta \gamma^t \delta \nabla_\theta \log \pi_\theta(a_t \vert s_t)
        \end{array}
        $$

## Whitening (Return Normalization)
분산을 줄이는 또 다른 방법으로는 Whitening이 있다. Whitening은 Return을 정규화하는 방법을 말한다. 특정 에피소드를 진행하는 도중에서 발생한 모든 Return들의 평균 및 표준편차를 통해서 각각의 시간 단계의 Return $$R_t$$를 정규화하는 방식으로 Whitening을 수행할 수 있다:

$$
\begin{array}{rl}
R^*_t & \longleftarrow \frac{R_t - \bar{R}}{\sigma_R}, \\
\theta & \longleftarrow \theta + \alpha \gamma^t \nabla_\theta \log \pi_\theta(a_t \vert s_t) R^*_t.
\end{array}
$$

이를 통해 Whitening이 분산을 줄이는데 도움이 되는 것을 확인할 수 있다:

$$
\text{Var}\left[ R^*_t \right] = \text{Var}\left[ \frac{R_t - \bar{R}}{\sigma_R} \right] = \frac{1}{\sigma_R^2} \text{Var}\left[ R_t \right].
$$

## Actor-Critic Method

## Actor-Critic Algorithm

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.14
    - 최초 게제
- 2022.07.30
    - REINFORCE + Baseline Method 및 Whitening 내용 정리