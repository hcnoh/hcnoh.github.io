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

## REINFORCE + Baseline Method

## Whitening (Return Normalization)

## Actor-Critic Method

## Actor-Critic Algorithm

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.14
    - 최초 게제