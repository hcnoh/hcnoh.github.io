---
layout: post
use_math: true
title: "[Reinforcement Learning] 7. Trust Region Policy Optimization (3)"
date: 2023-01-02 00:00:00
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
permalink: /2023-01-02-reinforcement-learning-07
---

이번 포스트에서는 이전 포스트와 마찬가지로 `Trust Region Policy Optimization`(TRPO)를 설명하기 위한 이론적 배경들을 설명하도록 한다. 먼저 이전 포스트에서 다루었던 `연관된 정책 짝`과 `TV Distance` 사이의 관계를 먼저 정리하고 이어서 TV Distance의 중요한 성질인 `Pinsker's Inequality`를 정리하도록 한다.

## 연관된 정책 짝과 TV Distance 사이의 관계
먼저 다음의 Lemma를 살펴보도록 하자.

**Lemma 1)**
다음을 만족하는 분포 $$p, q$$를 가정하자:

$$
D_{\text{TV}}(p \Vert q) = \alpha.
$$

이때 두 분포 $$p, q$$는 다음을 만족한다:

$$
\alpha \leq p(X \neq Y), \ X \sim p, Y \sim q.
$$

## Pinsker's Inequality
`Pinsker's Inequality`는 `Kullback-Leibler Divergence`(KL Divergence)와 `TV Distance` 사이의 관계에 대한 부등식이다. Pinsker's Inequality를 기술하면 다음과 같다:

**Theorem 1)**
공간 $$\mathcal{X}$$에서 정의된 분포 $$p, q$$에 대하여 다음의 부등식이 성립한다:

$$
D_{\text{TV}} (p \Vert q) \leq \sqrt{\frac{1}{2} D_{\text{KL}}(p \Vert q)}.
$$

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)
- [Wikipedia: Pinsker's Inequality](https://en.wikipedia.org/wiki/Pinsker%27s_inequality)

## 수정 사항
- 2023.01.02
    - 최초 게제