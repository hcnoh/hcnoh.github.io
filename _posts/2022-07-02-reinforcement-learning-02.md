---
layout: post
use_math: true
title: "[Reinforcement Learning] 2. Policy Gradient Theorem"
date: 2022-07-02 00:00:00
tagline: "강화 학습에서 가장 중요한 정리인 Policy Gradient Theorem을 다루고 이를 통한 기초적인 알고리즘인 REINFORCE에 대해서 정리"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-07-02-reinforcement-learning-02
---

이전 [포스트](https://hcnoh.github.io/2022-07-01-reinforcement-learning-01)에서는 강화 학습 알고리즘들을 기술하는데 필요한 용어들과 함수들, 그리고 그 함수들의 성질들에 관하여 간단히 정리하였다. 이번 포스트에서는 그러한 함수들과 관련된 추가적인 특징들과 강화 학습에서 가장 중요한 정리인 Policy Gradient Theorem을 다룰 것이다.

## 강화 학습의 목적
강화 학습의 목적을 다시 형식적으로 기술해보자. 그 전에 Return에 대해서 먼저 정의한다. Return은 현재 시간 단계 $$t$$에서부터 미래까지의 누적 보상으로 정의한다:

$$
R_t = \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}).
$$

추가적으로 Return은 State-action Value Function $$Q_\pi$$의 Unbiased Estimator이기도 하다. 주어진 고정된 $$s_t, a_t$$에 대해서:

$$
\mathbb{E}_{\tau \sim \pi}[R_t] = \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}[R_t] = Q_\pi(s_t, a_t).
$$

여기서 $$\tau$$는 Agent가 생성하는 궤적 $$s_{t+1}, a_{t+1}, s_{t+2}, a_{t+2}, \cdots$$이며 $$\tau \sim \pi$$는 궤적 $$\tau$$의 Sampling Distribution이 정책 $$\pi$$라는 의미이다. 다시 말하면 $$\pi$$의 정책을 가지는 Agent가 궤적 $$\tau$$를 생성한다는 의미이며 이 경우에는 이를 다음과 같이 표현할 수 있다:

$$
\tau \sim \pi : s_{t'} \sim \Pr[\cdot \vert s_{t'-1}, a_{t'-1}], a_{t'} \sim \pi(\cdot \vert s_{t'}) \ \ \text{for} \ t'=t+1, t+2, \cdots
$$

여기서 우리는 정책 $$\pi$$에 대한 Expected Return을 생각해볼 수 있다. 정책 $$\pi$$에 대한 Expected Return $$\eta(\pi)$$는 다음과 같이 정의한다:

$$
\eta(\pi) = \mathbb{E}_{\tau \sim \pi}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right].
$$

이는 다음과 같이 표현될 수 있다:

$$
\begin{array}{rl}
\eta(\pi)
& = \mathbb{E}_{\tau \sim \pi}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right] \\
& = \mathbb{E}_{s_0 \sim \rho_0}\left[ \mathbb{E}_{a_t \sim \pi(\cdot \vert s_t), s_{t+1}\sim \Pr[\cdot \vert s_t, a_t]}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right] \right] \\
& = \mathbb{E}_{s_0 \sim \rho_0}\left[ V_\pi(s_0) \right].
\end{array}
$$

Expected Return $$\eta(\pi)$$의 의미는 정책 $$\pi$$를 통해서 획득할 수 있는 누적 보상의 기대값이다. 즉, 이를 통해서 우리는 강화 학습의 목적을 다음과 같이 형식화할 수 있을 것이다:

$$
\arg \max_\pi \eta(\pi).
$$

즉, 강화 학습의 목적은 누적 보상의 기대값인 Expected Return $$\eta(\pi)$$를 최대화하는 정책 $$\pi$$를 찾는 것이다. 따라서 우리는 앞으로 주어진 정책 $$\pi$$의 Expected Return $$\eta(\pi)$$를 `목적 함수`라고 부르기로 한다.

## Policy Gradient Theorem
위에서 우리는 강화 학습이란 목적 함수를 최대화하는 정책을 찾는 것이라고 정리했다. 이 문제를 어떻게 접근할지에 대해서 고민하기에 앞서 우리는 정책을 특정 매개변수 $$\theta$$로 매개변수화된 확률 분포 $$\pi_\theta$$라고 가정하자. 이렇게 된다면 우리는 목적 함수를 최대화하는 정책을 매개변수화하는 $$\theta$$를 찾으면 되는 것이다. 즉, 우리는 다음과 같은 반복적인 알고리즘을 통해 최적의 $$\theta$$를 찾고자 시도해볼 수 있다:

$$
\theta = \theta_{\text{old}} + \left. \nabla_\theta \eta(\pi_\theta)\right\vert_{\theta_{\text{old}}}.
$$

여기서 $$\nabla_\theta \eta(\pi_\theta)$$를 Policy Gradient라고 정의한다.

Policy Gradient Theorem은 Policy Gradient에 대한 계산 결과를 제시한다. Policy Gradient Theorem에서 제시한 Policy Gradient의 결과는 다음과 같다:

$$
\nabla_\theta \eta(\pi_\theta) = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \sum_{t=0}^\infty \gamma^t \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right].
$$

이를 증명해보자.

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.02
    - 최초 게제