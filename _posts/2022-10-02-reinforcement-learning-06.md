---
layout: post
use_math: true
title: "[강화 학습] 6. Trust Region Policy Optimization (2)"
date: 2022-10-02 00:00:00
tagline: "Trust Region 방법을 이용하여 Policy Gradient의 성능을 향상시킨 알고리즘인 Trust Region Policy Optimization에 대해서 정리"
categories:
- 강화 학습 스터디
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-10-02-reinforcement-learning-06
---

이번 포스트에서는 `Trust Region Policy Optimization`(TRPO)를 설명하기 위한 몇 가지 이론적 배경들인 `Coupled Policy Pair` 및 `Total Variation Distance`(TV Distance)에 대해서 정리하도록 한다.

## Coupled Policy Pair
두 정책 $$(\pi, \tilde{\pi})$$에 대해서 만약 주어진 어떤 상태에 대한 두 정책이 생성한 행동이 일치하지 않을 확률이 $$\alpha$$보다 작거나 같은 경우 두 정책 $$(\pi, \tilde{\pi})$$을 $$\alpha$$-`coupled Policy Pair`라고 정의한다. 이를 식으로 정리하면 다음과 같다:

$$
\sum_{a \neq \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) = p(a \neq \tilde{a} \vert \pi, \tilde{\pi}, s) \leq \alpha.
$$

Coupled Policy Pair의 개념을 통해서 우리는 모든 상태 $$s$$에 대해서 $$\alpha$$-coupled Policy Pair $$\pi, \tilde{\pi}$$에 대해 다음의 정리가 만족됨을 보이고자 한다.

**Theorem 1)**

$$
\left\vert \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \pi} \left[ \bar{A}(s_t) \right] \right\vert \leq 4\alpha\left( 1 - \left( 1 - \alpha \right)^t \right) \max_{s, a} \left\vert A_\pi(s, a) \right\vert.
$$

이를 증명하기 위해서는 먼저 다음의 Lemma를 증명해야 한다.

**Lemma 1)**
주어진 $$\alpha$$-coupled Policy Pair $$\pi, \tilde{\pi}$$는 모든 상태 $$s$$에 대해서 다음을 만족한다:

$$
\left\vert \bar{A}(s) \right\vert \leq 2\alpha \max_{s, a} \left\vert A_\pi(s, a) \right\vert.
$$

Lemma 1의 증명은 다음과 같다:

$$
\begin{array}{rl}
\left\vert \bar{A}(s) \right\vert
& = \left\vert \sum_a \tilde{\pi}(a \vert s) A_\pi (s, a) \right\vert \\
& = \left\vert \sum_a \left( \tilde{\pi}(a \vert s) A_\pi (s, a) - \pi(a \vert s) A_\pi (s, a) \right) \right\vert \\
& = \left\vert \sum_{\tilde{a}} \tilde{\pi}(\tilde{a} \vert s) A_\pi (s, \tilde{a}) - \sum_{a} \pi(a \vert s) A_\pi (s, a) \right\vert.
\end{array}
$$

여기서는 다음의 관계를 사용하였다:

$$
\begin{array}{rl}
\sum_a \pi(a \vert s) A_\pi(s, a)
& = \sum_a \pi(a \vert s) (Q_\pi (s, a) - V_\pi(s)) \\
& = \sum_a \pi(a \vert s)Q_\pi(s, a) - V_\pi(s) \\
& = 0.
\end{array}
$$

따라서 $$\left\vert \bar{A}(s) \right\vert$$는 다음과 같이 정리할 수 있다:

$$
\begin{array}{rl}
\left\vert \bar{A}(s) \right\vert
& = \left\vert \sum_{a, \tilde{a}} \pi(a \vert s) \cdot \pi(\tilde{a} \vert s) \cdot A_\pi(s, \tilde{a}) \right. \\
& \ \ \ \  \ \ \ \ - \left. \sum_{a, \tilde{a}} \pi(a \vert s) \cdot \tilde{\pi}(\tilde{a} \vert s) \cdot A_\pi(s, a) \right\vert \\
& = \left\vert \sum_{a, \tilde{a}} \pi(a \vert s) \tilde{\pi}(a \vert s) \left( A_\pi(s, \tilde{a}) - A_\pi(s, a) \right) \right\vert \\
& = \left\vert \sum_{a, \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) \left( A_\pi(s, \tilde{a}) - A_\pi(s, a) \right) \right\vert \\
& = \left\vert \sum_{a \neq \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) \left( A_\pi(s, \tilde{a}) - A_\pi(s, a) \right) \right\vert \\
& \leq \sum_{a \neq \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) \left\vert A_\pi(s, \tilde{a}) - A_\pi(s, a) \right\vert \\
& \leq \sum_{a \neq \tilde{a}} p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) \cdot 2\max_{s, a} \left\vert A_\pi(s, a) \right\vert \\
& \leq 2\alpha \max_{s, a} \left\vert A_\pi(s, a) \right\vert. \ \ \ \ \text{Q.E.D.}
\end{array}
$$

이제 Theorem 1을 증명해보도록 하자. 먼저 주어진 $$\alpha$$-coupled Policy Pair $$\pi, \tilde{\pi}$$에 대해서 $$n_t$$를 시간 단계 $$t$$ 이전까지 정책 $$\pi$$와 $$\tilde{\pi}$$가 행동이 동일하지 않은 횟수로 정의하자. 그러면 이 정의를 통해 다음의 관계를 생각해볼 수 있다:

$$
\begin{array}{rl}
\mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] & = p(n_t = 0) \cdot \mathbb{E}_{s_t \sim \tilde{\pi} \vert n_t = 0} \left[ \bar{A}(s_t) \right] \\
& \ \ \ \  \ \ \ \ + p(n_t > 0) \cdot \mathbb{E}_{s_t \sim \tilde{\pi} \vert n_t > 0} \left[ \bar{A}(s_t) \right].
\end{array}
$$

여기서 동일한 상태-행동 쌍 $$(s_{t - 1}, a_{t - 1})$$에 대해서 확률값 $$p(s_t \vert s_{t - 1}, a_{t - 1})$$이 동일하기 때문에 다음의 관계를 자연스럽게 확인할 수 있다:

$$
\mathbb{E}_{s_t \sim \tilde{\pi} \vert n_t = 0} \left[ \bar{A}(s_t) \right] = \mathbb{E}_{s_t \sim \pi \vert n_t = 0} \left[ \bar{A}(s_t) \right].
$$

또한 $$n_t$$의 정의에 의하여 다음을 확인할 수 있다:

$$
\begin{array}{rl}
p(n_t = 0)
& = \sum_{a_0 = \tilde{a}_0} p(a_0, \tilde{a}_0 \vert \pi, \tilde{\pi}, s_0) \cdots \sum_{a_{t - 1}, \tilde{a}_{t - 1}} p(a_{t - 1}, \tilde{a}_{t - 1} \vert \pi, \tilde{\pi}, s_{t - 1}) \\
& = (1 - \alpha)^t.
\end{array}
$$

$$
\therefore \ p(n_t > 0) = 1 - p(n_t = 0) = 1 - (1 - \alpha)^t.
$$

따라서 다음을 유도할 수 있다:

$$
\begin{array}{l}
\left\vert \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \pi} \left[ \bar{A}(s_t) \right] \right\vert \\
\ \ \ \ = p(n_t > 0) \left\vert \mathbb{E}_{s_t \sim \tilde{\pi} \vert n_t > 0} \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \pi \vert n_t > 0} \left[ \bar{A}(s_t) \right] \right\vert \\
\ \ \ \ \leq \left( 1 - (1 - \alpha)^t \right) \left\{ \left\vert \mathbb{E}_{s_t \sim \tilde{\pi} \vert n_t > 0} \left[ \bar{A}(s_t) \right] \right\vert + \left\vert \mathbb{E}_{s_t \sim \pi \vert n_t > 0} \left[ \bar{A}(s_t) \right] \right\vert \right\} \\
\ \ \ \  \ \ \ \ \leq \left( 1 - (1 - \alpha)^t \right) \left( 2\alpha \max_{s, a} \left\vert A_\pi(s, a) \right\vert + 2\alpha \max_{s, a}\left\vert A_\pi(s, a) \right\vert \right) \\
\ \ \ \  \ \ \ \  \ \ \ \ = 4\alpha \left( 1 - (1 - \alpha)^t \max_{s, a} \left\vert A_\pi(s, a) \right\vert \right). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

## Total Variation Distance (TV Distance)
집합 $$\mathcal{X}$$에 정의된 두 분포 $$p$$와 $$q$$에 대한 `Total Variation Distance` (TV Distance)는 다음과 같이 정의한다:

$$
D_{\text{TV}}(p \Vert q) = \max_{A \subseteq \mathcal{X}} \left\vert p(A) - q(A) \right\vert.
$$

이는 다음과 같이도 쓸 수 있다.

**Theorem 2)**

$$
D_{\text{TV}}(p \Vert q) = \frac{1}{2}\sum_{x \in \mathcal{X}} \left\vert p(x) - q(x) \right\vert.
$$

Theorem 2의 증명은 다음과 같다. 먼저 집합 $$B$$를 다음과 같이 정의한다:

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

**Remark)**

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
    - Coupled Policy Pair, TV Distance 내용 추가
- 2024.03.14
    - 몇몇 명칭 수정 및 오타 수정