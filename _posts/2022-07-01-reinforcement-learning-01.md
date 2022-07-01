---
layout: post
use_math: true
title: "[Reinforcement Learning] 1. 강화 학습 소개"
date: 2022-07-01 00:00:00
tagline: "강화 학습의 소개 및 강화 학습 내용을 기술하기 위한 기초적인 내용들 정리"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-07-01-machine-learning-01
---

강화 학습에 대해서 스터디를 진행하였고, 스터디 내용을 정리하여 연재를 진행하려고 한다. 주된 참고 자료는 강화 학습의 중요 알고리즘에 대한 논문 및 발표 자료들을 직접 활용하였다.

본 포스트 및 향후 업로드될 시리즈에서는 강화 학습의 내용을 기술해 나감에 있어서 Policy Gradient 위주로 내용 정리를 해나갈 계획이며, 이에 따라 Policy Gradient 계열의 중요 알고리즘인 TRPO, PPO 등을 자세히 다루게 될 것이다.

## 강화 학습이란?
강화 학습(Reinforcement Learning, RL)은 머신 러닝의 한 분야이며 환경(Environment) 내부에서 Agent의 최적의 정책(Policy)을 찾는 것을 목표로 한다. 여기서 Agent와 환경의 관계에 대해서 정리하고 넘어가도록 한다.
- Agent는 현재 환경의 상태(State)를 관찰하고, 그에 따른 행동(Action)을 수행한다.
- 환경은 Agent의 행동을 받아서 그에 따른 결과로 상태의 변화를 일으키며, 추가적으로 행동에 따른 보상(Reward)을 Agent에게 되돌려준다.

즉, Agent와 환경은 서로 상호작용을 하게 되며, 이 상황에서 Agent의 누적 보상을 최대화하도록 하는 최적의 정책을 찾는 것이 강화 학습의 목적이 된다.

## Value Function
Agent가 어떤 순간에 자신이 관찰한 현재의 상태와 그에 따른 자신의 행동이 얼마나 값어치가 있는지에 대한 기준이 필요할 것이다. 이러한 기준은 향후 자신의 행동을 위한 정책을 개선하기 위한 지표로 사용될 것이다. 이 기준은 단순하게 현재 상태와 행동에 대한 즉각적인 보상을 고려해볼 수 있다. Agent가 행동을 취할 때 단순히 향후 받을 보상을 높게 받을 행동만을 취하는 것이다.

하지만 단순하게 단기적인 보상만을 고려하는 경우에는 장기적으로 문제가 발생할 수 있다. 시험 공부를 해야하는 학생의 예시를 들어보자. 학생에게는 두 가지 선택이 있다. 지금 시험 공부를 할 것인가와 지금 게임을 할 것인가이다. 학생이 받을 수 있는 보상으로는 자신의 행복(물론 정량화하기 힘든 것이지만 여기서는 예시로 활용하기 위해서 그냥 넘어가기로 한다.)을 고려한다고 가정하자. 만약 지금 당장 게임을 한다면 당장의 행복감은 올라가지만 우리는 이 행동이 장기적으로는 좋은 선택지가 아님을 알 수 있다. 시험 공부를 함으로 성적이 잘 나오면 장기적으로는 더욱 행복할 수 있기 때문이다.

따라서 상태와 행동에 대한 값어치는 장기적으로 고려해야 할 필요가 있다. 현재 상태에 대한 값어치를 매기기 위해서 Value Function이라는 것을 다음과 같이 정의한다:

$$
V_\pi(s_t) = \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}}\left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right].
$$

여기서 $$s_t$$와 $$a_t$$는 각각 현재 시간 단계 $$t$$에서의 상태와 행동이다. 또한 위의 기대값을 계산하기 위한 Sampling Distribution은 $$a_t \sim \pi(\cdot \vert s_t)$$, $$s_{t+1} \sim \Pr[\cdot \vert s_t, a_t]$$이며, 여기서 $$\pi$$는 정책의 행동 확률 분포, $$\Pr[\cdot \vert s_t, a_t]$$은 환경의 상태 변화를 위한 모델 확률 분포이다.

비슷하게 현재 상태, 행동에 대한 값어치를 동시에 매기기 위한 State-action Value Function을 정의한다:

$$
Q_\pi(s_t, a_t) = \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right].
$$

여기서의 Sampling Distribution도 Value Function의 경우와 동일하다.

여기서 두 Value Function $$V_\pi$$ 및 $$Q_{\pi}$$의 관계에 대한 간단하지만 중요한 Lemma가 존재한다:

$$
\mathbb{E}_{a_t \sim \pi(\cdot \vert s_t)}\left[ Q_{\pi}(s_t, a_t) \right] = V_{\pi}(s_t).
$$

증명은 다음과 같다:

$$
\begin{array}{rl}
\mathbb{E}_{a_t \sim \pi(\cdot \vert s_t)}\left[ \mathbb{E}_{s_{t+1:\infty}, a_{t+1:
\infty}} \left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right] \right] \\
\ \ \ \ \ \ \ \ = \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}}\left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right]
& = V_\pi(s_t). \ \ \ \ \text{Q.E.D}
\end{array}
$$

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.01
    - 최초 게제