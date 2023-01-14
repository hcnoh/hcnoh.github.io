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

증명은 다음과 같다. 먼저 모든 부분집합 $$A \subseteq \mathcal{X}$$에 대하여 다음을 확인할 수 있다:

$$
\begin{array}{rl}
p(A) - q(A)
& = \sum_{x \in A}p(x) - \sum_{x \in A}q(x) \\
& = p(X \in A) - p(Y \in A).
\end{array}
$$

또한 모든 확률 값은 $$1$$보다 작거나 같으므로:

$$
p(X \in A \land Y \notin A) \leq 1.
$$

이는 다음과 같이 정리될 수 있다:

$$
\begin{array}{ll}
p(X \in A \land Y \notin A) \leq 1 \\
\ \ \ \ \Longrightarrow p(X \in A) + p(Y \notin A) - p(X \in A \land Y \notin A) \leq 1 \\
\ \ \ \ \Longrightarrow p(X \in A) + p(Y \notin A) - 1 \leq p(X \in A \land Y \notin A) \\
\ \ \ \ \Longrightarrow p(X \in A) - p(Y \in A) \leq p(X \in A \land Y \notin A).
\end{array}
$$

여기서 또한 다음을 확인할 수 있다:

$$
X \in A \land Y \notin A \longrightarrow X \neq Y.
$$

따라서:

$$
p(X \in A \land Y \notin A) \leq p(X \neq Y).
$$

즉, 최종적으로 다음을 확인할 수 있다:

$$
\begin{array}{rl}
\alpha = \sum_{x:p(x) \geq q(x)} \left[ p(x) - q(x) \right] & \leq \max_A \left[ p(A) - q(A) \right] \\
& \leq p(X \neq Y). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

Lemma 1을 통해서 우리는 두 분포에 대한 TV Distance는 두 분포로 생성되는 샘플이 일치하지 않을 확률값에 대한 Lower Bound를 의미한다는 것을 확인할 수 있다. 다음으로 Lemma 2를 살펴보자.

**Lemma 2)**
다음을 만족하는 분포 $$p, q$$를 가정하자:

$$
D_{\text{TV}}(p \Vert q) = \alpha.
$$

이 때, 두 분포 $$p, q$$로 생성되는 랜덤 변수 $$X, Y$$에 대해서 $$X = Y$$일 확률이 $$1 - \alpha$$인 $$(X, Y)$$에 대한 결합 분포가 존재한다.

증명은 다음과 같다. 먼저 다음을 살펴보자:

$$
\begin{array}{rl}
\alpha = D_{\text{TV}}(p \Vert q)
& = \sum_{x:p(x) \geq q(x)} \left[ p(x) - q(x) \right] \\
& = \sum_{x:p(x) < q(x)} \left[ q(x) - p(x) \right].
\end{array}
$$

또한 다음의 분포들을 고려해보자:

$$
\begin{array}{rl}
f_1(x)
& = \left\{
\begin{array}{ll}
\frac{p(x) - q(x)}{\alpha} & \text{if} \ p(x) \geq q(x) \\
0 & \text{otherwise}
\end{array},
\right. \\
f_2(x)
& = \left\{
\begin{array}{ll}
0 & \text{if} \ p(x) \geq q(x) \\
\frac{q(x) - p(x)}{\alpha} & \text{otherwise}
\end{array},
\right. \\
f_3(x)
& = \left\{
\begin{array}{ll}
\frac{q(x)}{1 - \alpha} & \text{if} \ p(x) \geq q(x) \\
\frac{p(x)}{1 - \alpha} & \text{otherwise}
\end{array}.
\right.
\end{array}
$$

그러면 다음을 확인할 수 있다:

$$
\begin{array}{rl}
\sum_x f_1(x)
& = \sum_{x: p(x) \geq q(x)} f_1(x) = 1, \\
\sum_x f_2(x)
& = \sum_{x: p(x) < q(x)} f_2(x) = 1, \\
\sum_x f_3(x)
& = \frac{1}{1 - \alpha} \left[ \sum_{x: p(x) \geq q(x)} q(x) + \sum_{x: p(x) < q(x)} p(x) \right] \\
& = \frac{1}{1 - \alpha} \left[ \sum_{x: p(x) \geq q(x)} q(x) + 1 - \sum_{x: p(x) \geq q(x)} p(x) \right] \\
& = \frac{1}{1 - \alpha} \left( 1 - \sum_{x: p(x) \geq q(x)} \left[ p(x) - q(x) \right] \right) \\
& = \frac{1 - \alpha}{1 - \alpha} = 1.
\end{array}
$$

즉, $$f_1, f_2, f_3$$는 확률 분포라는 것을 확인할 수 있다. 또한 다음의 확률 분포를 가지는 랜덤 변수 $$Z$$를 고려하자:

$$
\begin{array}{rl}
p(z)
& = \left\{
    \begin{array}{ll}
        1 - \alpha & \text{if} \ z = 1, \\
        \alpha & \text{if} \ z = 0, \\
        0 & \text{otherwise}.
    \end{array}
\right.
\end{array}
$$

이를 바탕으로 다음과 같은 샘플링 과정(Sampling Process)을 고려해보자. 만약 $$Z = 1$$이라면 $$X$$를 $$f_3$$로 부터 생성하고 $$Y$$는 $$Y = X$$로 선택한다. 그리고 만약 $$Z = 0$$이라면 $$X$$는 f_1으로부터 생성하고 $$Y$$는 $$f_2$$로부터 생성한다.

이 과정으로부터 다음을 확인할 수 있다:

$$
\begin{array}{rl}
p(X = Y)
& = 1 - \alpha, \\
p(x)
& = (1 - \alpha) f_3(x) + \alpha f_1(x), \\
q(x)
& = (1 - \alpha) f_3(x) + \alpha f_2(x). \ \ \ \ \text{Q.E.D.}
\end{array}
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
- 2023.01.14
    - 증명 정리