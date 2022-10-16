---
layout: post
use_math: true
title: "[Reinforcement Learning] 6. Trust Region Policy Optimization (2)"
date: 2022-10-02 00:00:00
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
permalink: /2022-10-02-reinforcement-learning-06
---

## 연관된 정책 짝 (Coupled Policy Pair)
두 정책 $$(\pi, \tilde{\pi})$$에 대해서 만약 주어진 어떤 상태에 대한 두 정책이 생성한 행동이 일치하지 않을 확률이 $$\alpha$$보다 작거나 같은 경우 두 정책 $$(\pi, \tilde{\pi})$$을 $$\alpha$$로 `연관된 정책 짝`($$\alpha$$-coupled Policy Pair)이라고 정의한다. 이를 식으로 정리하면 다음과 같다:

$$
\sum_{a \neq \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) = p(a \neq \tilde{a} \vert \pi, \tilde{\pi}, s) \leq \alpha.
$$

## Total Variation Distance (TV Distance)
집합 $$\mathcal{X}$$에 정의된 두 분포 $$p$$와 $$q$$에 대한 `Total Variation Distance` (TV Distance)는 다음과 같이 정의한다:

$$
D_{\text{TV}}(p \Vert q) = \max_{A \subseteq \mathcal{X}} \left\vert p(A) - q(A) \right\vert.
$$

이는 다음과 같이도 쓸 수 있다.

**Theorem 1)**
$$
D_{\text{TV}}(p \Vert q) = \frac{1}{2}\sum_{x \in \mathcal{X}} \left\vert p(x) - q(x) \right\vert.
$$

Theorem 1의 증명은 다음과 같다. 먼저 집합 $$B$$를 다음과 같이 정의한다:

$$
B = \left\{ x \in \mathcal{X}: p(x) \geq q(x) \right\}.
$$

이를 통해서 다음을 확인할 수 있다:

$$
D_{\text{TV}}(p \Vert q) = p(B) - q(B) \ \text{or} \ q(\bar{B}) - p(\bar{B}).
$$

또한 다음을 확인할 수 있다:

$$
q(\bar{B}) - p(\bar{B}) = (1 - q(B)) - (1 - p(B)) = p(B) - q(B).
$$

따라서:

$$
\begin{array}{rl}
\frac{1}{2}\sum_{x \in \mathcal{X}} \left\vert p(x) - q(x) \right\vert
& = \frac{1}{2}\left[ \sum_{x \in B}(p(x) - q(x)) + \sum_{x \in \bar{B}}(q(x) - p(x)) \right] \\
& = \frac{1}{2}\left( p(B) - q(B) + q(\bar{B}) - p(\bar{B}) \right) \\
& = p(B) - q(B) \\
& = D_{\text{TV}}(p \Vert q). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

위의 증명 과정을 통해서 다음의 결과도 자연스레 유도할 수 있다:

**Remark**
$$
D_{\text{TV}}(p \Vert q) = \sum_{x \in \mathcal{X}: p(x) \geq q(x)} \left[ p(x) - q(x) \right].
$$

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)
- [Wikipedia: Pinsker's Inequality](https://en.wikipedia.org/wiki/Pinsker%27s_inequality)

## 수정 사항
- 2022.09.23
    - 최초 게제
- 2022.10.16
    - 연관된 정책 짝, TV Distance 내용 추가