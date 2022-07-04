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

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.02
    - 최초 게제