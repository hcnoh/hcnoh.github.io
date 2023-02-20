---
layout: post
use_math: true
title: "[Reinforcement Learning] 8. Trust Region Policy Optimization (4)"
date: 2023-02-09 00:00:00
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
permalink: /2023-02-09-reinforcement-learning-08
---

이번 포스트에서는 이전 포스트와 마찬가지로 `Trust Region Policy Optimization`(TRPO)를 설명하기 위한 이론적 배경들을 설명하도록 한다. 이전 포스트에서 다루었던 Lemma들과 `Pinsker's Inequality`를 바탕으로 이번 포스트에서는 TRPO의 중요한 정리를 먼저 다루고 이 정리를 바탕으로 `Minorization-maximization`(MM) 알고리즘을 정리하여 TRPO의 기본적인 형태를 다루게 된다.

## Minorization-maximization 알고리즘
먼저 다음의 정리를 살펴보자.

**Theorem 1)**

$$
\eta(\tilde{\pi}) \geq L_{\pi_\text{old}}(\tilde{\pi}) - CD_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}).
$$

여기서:

$$
\begin{array}{rl}
C
& = \frac{4 \epsilon \gamma}{(1 - \gamma)^2} \\
D_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi})
& = \max_s D_\text{KL}(\pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s)), \\
\epsilon
& = \max_{s, a} \left\vert A_{\pi_\text{old}}(s, a) \right\vert.
\end{array}
$$

이를 증명하기 위해서는 이전 [포스트](https://hcnoh.github.io/2023-01-02-reinforcement-learning-07)에서 다뤘던 Lemma 1과 Lemma 2, 그리고 `Pinsker's Inequality`에 대한 내용들이 필요하다. 먼저 임의의 정책 $$\pi_\text{old}$$와 $$\tilde{\pi}$$에 대해서 적절한 결합 분포를 선택하여 다음을 만족할 수 있다:

$$
D_\text{TV}^\text{max}(\pi_\text{old}, \tilde{\pi}) = \max_s D_\text{TV}\left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) = \max_s p(a \neq \tilde{a} \vert \pi_\text{old}, \tilde{\pi}, s) = \alpha.
$$

따라서 $$\pi_\text{old}$$와 $$\tilde{\pi}$$는 $$\alpha$$로 연관된 정책 짝이다.

이제 이를 바탕으로 다음을 살펴보자:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \\
= \sum_s \rho_{\pi_\text{old}}(s) \sum_a \tilde{\pi}(a \vert s) A_{\pi_\text{old}}(s, a) - \sum_s \rho_{\tilde{\pi}}(s) \sum_a \tilde{\pi}(a \vert s) A_{\pi_\text{old}}(s, a) \\
= \sum_s \rho_{\pi_\text{old}}(s) \bar{A}(s) - \sum_s \rho_{\tilde{\pi}}(s) \bar{A}(s) \\
= \sum_s \sum_{t = 0}^\infty \gamma^t p(s_t = s \vert \pi_\text{old}) \bar{A}(s) - \sum_s \sum_{t = 0}^\infty \gamma^t p(s_t = s \vert \tilde{\pi}) \bar{A}(s) \\
= \sum_{t = 0}^\infty \gamma^t \left( \mathbb{E}_{s_t \sim \pi_\text{old} } \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] \right) \\
\ \ \ \ \leq \sum_{t = 0}^\infty \gamma^t \left\vert \mathbb{E}_{s_t \sim \pi_\text{old} } \left[ \bar{A}(s_t) \right] - \mathbb{E}_{s_t \sim \tilde{\pi}} \left[ \bar{A}(s_t) \right] \right\vert
\end{array}
$$

여기서 $$\pi_\text{old}$$와 $$\tilde{\pi}$$는 $$\alpha = D_\text{TV}^\text{max}(\pi_\text{old}, \tilde{\pi})$$로 연관된 정책 짝이므로 다음과 같이 쓸 수 있다:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \\
\ \ \ \ \leq \sum_{t = 0}^\infty \gamma^t 4\alpha \left( 1 - (1 - \alpha)^t \right) \max_{s, a} \left\vert A_{\pi_\text{old}} (s, a) \right\vert \\
\ \ \ \  \ \ \ \ = \sum_{t = 0}^\infty 4\alpha \left( \gamma^t - (\gamma - \gamma \alpha)^t \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \left( \frac{1}{1 - \gamma} - \frac{1}{1 - \gamma + \gamma\alpha} \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \left( \frac{1}{1 - \gamma} - \frac{1}{1 - \gamma(1 - \alpha)} \right) \epsilon \\
\ \ \ \  \ \ \ \ = 4\alpha \cdot \frac{\gamma \alpha}{(1 - \gamma)(1 - \gamma(1 - \alpha))} \cdot \epsilon \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq 4\alpha \cdot \frac{\gamma \alpha}{(1 - \gamma)^2} \cdot \epsilon \\
\ \ \ \  \ \ \ \  \ \ \ \  \ \ \ \ = \frac{4\epsilon \gamma}{(1 - \gamma)^2} \cdot \alpha^2.
\end{array}
$$

여기서 Pinsker's Inequality를 통해서 다음을 유도할 수 있다:

$$
\begin{array}{l}
\alpha^2 = \max_s D_\text{TV}^2 \left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) \\
\ \ \ \  \leq \max_s \frac{1}{2} D_\text{KL}\left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) \\
\ \ \ \  \ \ \ \ \leq \max_s D_\text{KL} \left( \pi_\text{old}(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) = D_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}).
\end{array}
$$

따라서:

$$
\begin{array}{l}
L_{\pi_\text{old}}(\tilde{\pi}) - \eta(\tilde{\pi}) \leq CD_\text{KL}^\text{max} (\pi_\text{old}, \tilde{\pi}) \\
\ \ \ \ \Longrightarrow \eta(\tilde{\pi}) \geq L_{\pi_\text{old}}(\tilde{\pi}) - CD_\text{KL}^\text{max}(\pi_\text{old}, \tilde{\pi}). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

위의 정리를 통해서 우리는 `Minorization-maximization`(MM) 알고리즘을 획득하여 `단조롭게`(Monotonically) 정책을 개선할 수 있다. 이는 아래의 최적화 과정을 통해서 수행된다:

$$
\pi = \arg \max_\pi \left[ L_{\pi_\text{old}}(\pi) - CD_\text{KL}^\text{max}(\pi_\text{old}, \pi) \right].
$$

이 최적화 문제의 목적 함수는 `Surrogate 함수`라고 부른다. 좌우지간 이 최적화 과정을 통해서 단조롭게 정책을 개선하여 Expected Return $$\eta$$가 감소되지 않는 것을 보장하며 반복 알고리즘을 다음과 같이 수행할 수 있다:

- Expected Return $$\eta$$가 감소되지 않는 것을 보장하는 정책 반복 알고리즘
    - 정책 $$\pi_0$$ 초기화
    - 각 단계 $$i = 0, 1, 2, \cdots$$에 대해서 수렴할때까지 반복:
        - 모든 이득 함수 값들 $$A_{\pi_i}(s, a)$$을 계산한다.
        - 다음과 같은 최적화 문제를 푼다:

        $$
        \pi_{i + 1} = \arg \max_\pi \left[ L_{\pi_i}(\pi) - CD_\text{KL}^\text{max}(\pi_i, \pi) \right].
        $$

위의 알고리즘을 통해 Surrogate 함수를 최대화하는 정책을 계속 선택하면 그 정책에 의한 Expected Return 역시 최대화가 되게끔 계속 정책을 개선할 수 있게 된다.

## 제한된 최적화 문제
우리는 강화 학습에 인공 신경망 모델을 활용하여 정책망(Policy Network)을 훈련시키는 방식으로 적용을 할 것이다. 따라서 정책은 매개변수 $$\theta$$로 매개변수화되어 $$\pi_\theta$$라고 쓰게 된다. 이에따라 Surrogate 함수는 다음과 같이 쓰여지게 된다:

$$
L_{\theta_\text{old}}(\theta) - CD_\text{KL}^\text{max}(\theta_\text{old}, \theta).
$$

만약 우리가 Penalty 상수 $$C$$를 위의 정리에서 제안한 수치를 활용한다면 최적화 단계에 따른 Step Size가 매우 작을 것이다. 이는 훈련의 진행을 매우 더디게 만드는 원인이 된다. 따라서 더 큰 Step Size를 취하여 좀 더 거친 방법으로 훈련을 진행해야 할 것이다. 이를 위한 최적화 문제는 다음과 같이 수정될 수 있다:

$$
\begin{array}{l}
\text{maximize} \ L_{\theta_\text{old}}(\theta) \\
\text{subject to} \ D_\text{KL}^\text{max}(\theta_\text{old}, \theta) \leq \delta.
\end{array}
$$

즉 $$\pi_{\theta_\text{old}}$$와 $$\pi_\theta$$ 사이의 KL Divergence 영역을 특정 $$\delta$$값으로 제한하여 좀 더 큰 Step Size로 거칠게 훈련을 진행할 수 있게 된다. 여기서 물론 $$\delta$$를 적절히 잘 설정하는 것이 중요할 것이고 잘 선택된 $$\delta$$ 영역을 Trust Region이라고 말한다. 이 때문에 이 알고리즘을 우리가 TRPO라고 부르는 것이다.

좌우지간 이러한 Trust Region이라는 제한조건을 두고 최적화 문제를 풀어가는 것은 제한조건의 수가 증가할수록 상당히 실용적이지 못하다. 이 말을 좀 더 풀어서 설명하면 $$D_\text{KL}^\text{max}$$를 계산하는 과정에서 모든 가능한 상태 $$s$$를 고려하고 그 중 최대값을 찾아야하는 어려움이 있을 수 있다. 또한 추가적으로 최대값을 고르는 과정에서는 `Monte Carlo` 시뮬레이션이 불가능하다.

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)

## 수정 사항
- 2023.02.09
    - 최초 게제
- 2023.02.10
    - MM 알고리즘 정리