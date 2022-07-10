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
\tau \sim \pi : s_{t'} \sim p(\cdot \vert s_{t'-1}, a_{t'-1}), a_{t'} \sim \pi(\cdot \vert s_{t'}) \ \ \text{for} \ t'=t+1, t+2, \cdots.
$$

여기서 우리는 정책 $$\pi$$에 대한 Expected Return을 생각해볼 수 있다. 정책 $$\pi$$에 대한 Expected Return $$\eta(\pi)$$는 다음과 같이 정의한다:

$$
\eta(\pi) = \mathbb{E}_{\tau \sim \pi}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right],
$$

여기서 $$\rho_0$$는 최초 상태 $$s_0$$의 분포이다. 이 결과는 다음과 같이 표현될 수 있다:

$$
\begin{array}{rl}
\eta(\pi)
& = \mathbb{E}_{\tau \sim \pi}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right] \\
& = \mathbb{E}_{s_0 \sim \rho_0}\left[ \mathbb{E}_{a_t \sim \pi(\cdot \vert s_t), s_{t+1}\sim p(\cdot \vert s_t, a_t)}\left[ \sum_{t=0}^\infty \gamma^t r(s_t, a_t) \right] \right] \\
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

## Proof of Policy Gradient Theorem
Policy Gradient Theorem을 증명해보는 섹션이다. 내용이 좀 복잡하여 처음 강화 학습을 접하는 사람들은 넘어가도 좋을 것 같다.

Policy Gradient Theorem을 증명하기 위해서 먼저 목적 함수 $$\eta(\pi_\theta)$$를 살펴보면 다음과 같다:

$$
\eta(\pi_\theta) = \mathbb{E}_{s_0 \sim \rho_0}\left[ V_{\pi_\theta}(s_0) \right].
$$

여기서 Value Function $$V_{\pi_\theta}(s_0)$$는 다음과 같이 표현된다:

$$
V_{\pi_\theta}(s_0) = \sum_{a_0}\pi_\theta(a_0 \vert s_0)Q_{\pi_\theta}(s_0, a_0).
$$

이를 바탕으로 Policy Gradient $$\nabla_\theta \eta(\pi_\theta)$$를 계산해보면:

$$
\begin{array}{l}
\nabla_\theta \eta(\pi_\theta) \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ V_{\pi_\theta}(s_0) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \nabla_\theta \left( \sum_{a_0}\pi_\theta(a_0 \vert s_0) Q_{\pi_\theta}(s_0, a_0) \right) \right].
\end{array}
$$

여기서 Gradient 연산 $$\nabla_\theta$$는 선형 연산이므로 $$\sum_{a_0}$$로 들어갈 수 있고, 미분 연산의 법칙인 $$(fg)'=f'g + fg'$$을 활용하면:

$$
\begin{array}{l}
\mathbb{E}_{s_0 \sim \rho_0}\left[ \nabla_\theta \left( \sum_{a_0}\pi_\theta(a_0 \vert s_0) Q_{\pi_\theta}(s_0, a_0) \right) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right. \\
\ \ \ \  \ \ \ \  \ \ \ \ + \left. \sum_{a_0}\pi_\theta(a_0 \vert s_0) \nabla_\theta Q_{\pi_\theta}(s_0, a_0) \right]. \\
\end{array}
$$

여기서 다시 Lemma $$Q_{\pi_\theta}(s_0, a_0) = r(s_0, a_0) + \gamma \sum_{s_{1}}p\left( s_1 \vert s_0, a_0  \right)\gamma V_{\pi_\theta}(s_1)$$을 활용하면:

$$
\begin{array}{l}
\mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right. \\
\ \ \ \  \ \ \ \  \ \ \ \ + \left. \sum_{a_0}\pi_\theta(a_0 \vert s_0) \nabla_\theta Q_{\pi_\theta}(s_0, a_0) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right. \\
\ \ \ \  \ \ \ \  \ \ \ \ + \left. \sum_{a_0}\pi_\theta(a_0 \vert s_0) \nabla_\theta \left( r(s_0, a_0) + \gamma \sum_{s_1}p(s_1 \vert s_0, a_0) V_{\pi_\theta}(s_1) \right) \right] \\
 = \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right. \\
\ \ \ \  \ \ \ \  \ \ \ \ + \left. \sum_{a_0}\pi_\theta(a_0 \vert s_0) \nabla_\theta \left( \gamma \sum_{s_1}p(s_1 \vert s_0, a_0) V_{\pi_\theta}(s_1) \right) \right]
\end{array}
$$

여기서는 추가적으로 $$r(s_0, a_0)$$는 $$\theta$$와 관계가 없는 함수라는 사실을 활용하여 정리하였다. 위 결과를 다시 깔끔하게 정리하면 다음과 같다.

$$
\begin{array}{l}
\nabla_\theta \eta(\pi_\theta) \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0}(\nabla_\theta \pi_\theta(a_0 \vert s_0)) Q_{\pi_\theta}(s_0, a_0) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ + \mathbb{E}_{s_0 \sim \rho_0, a_0 \sim \pi_\theta(\cdot \vert s_0), s_1 \sim p(\cdot \vert s_0, a_0)}\left[ \gamma \nabla_\theta V_{\pi_\theta}(s_1) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0}(\nabla_\theta \pi_\theta(a_0 \vert s_0)) Q_{\pi_\theta}(s_0, a_0) \right] + \gamma \mathbb{E}_{s_1 \sim \rho_1}\left[ \nabla_\theta V_{\pi_\theta}(s_1) \right].
\end{array}
$$

여기서 $$\rho_1$$은 주어진 정책 $$\pi_{\theta}$$에 대한 상태 $$s_1$$의 분포이다. 다시 말하면 상태, 행동 궤적의 Sampling Distribution이 정책 $$\pi_\theta$$인 경우 시간 단계 $$t=1$$에서의 상태 $$s_1$$의 분포를 말한다:

$$
\rho_1(s_1) = \sum_{s_0, a_0}\rho_0(s_0) \cdot \pi_\theta(a_0 \vert s_0) \cdot p(s_1 \vert s_0, a_0).
$$

비슷하게 $$\pi_\theta$$에 대한 상태 분포 $$\rho_k$$를 다음과 같이 정의할 수 있다:

$$
\rho_k(s_k) = \sum_{s_{k-1}, a_{k-1}}\rho_{k-1}(s_{k-1}) \cdot \pi_\theta(a_{k-1} \vert s_{k-1}) \cdot p(s_k \vert s_{k-1}, a_{k-1}).
$$

이 정의를 활용하면 다음의 관계를 확인할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{s_k \sim \rho_k}\left[ V_{\pi_\theta}(s_k) \right] \\
= \mathbb{E}_{s_k \sim \rho_k}\left[ \sum_{a_k}\left( \nabla_\theta \pi_\theta(a_k \vert s_k) \right) Q_{\pi_\theta}(s_k, a_k) \right] + \gamma \mathbb{E}_{s_{k+1 \sim \rho_{k+1}}} \left[ \nabla_\theta V_{\pi_\theta}(s_{k+1}) \right].
\end{array}
$$

이 관계를 활용하면 Policy Gradient를 다음과 같이 계산할 수 있다:

$$
\begin{array}{l}
\nabla_\theta \eta(\pi_\theta) \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \nabla_\theta V_{\pi_\theta}(s_0) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right] + \gamma \mathbb{E}_{s_1 \sim \rho_1}\left[ \nabla_\theta V_{\pi_\theta}(s_1) \right] \\
= \mathbb{E}_{s_0 \sim \rho_0}\left[ \sum_{a_0} \left( \nabla_\theta \pi_\theta(a_0 \vert s_0) \right) Q_{\pi_\theta}(s_0, a_0) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ + \gamma \mathbb{E}_{s_1 \sim \rho_1}\left[ \sum_{a_1} \left( \nabla_\theta \pi_\theta(a_1 \vert s_1) \right) Q_{\pi_\theta}(s_1, a_1) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ + \gamma^2\mathbb{E}_{s_2 \sim \rho_2}\left[ \nabla_\theta V_{\pi_\theta}(s_2) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \vdots \\
= \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t \sim \rho_t}\left[ \sum_{a_t}\left( \nabla_\theta \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right].
\end{array}
$$

여기서 감쇄 상수 $$\gamma$$의 값이 $$0 \leq \gamma \leq 1$$의 값을 가지기 때문에 뒤에 붙는 Value Function에 대한 부분은 $$0$$으로 수렴하게 된다. (증명 필요):

$$
\gamma^t \mathbb{E}_{s_t \sim \rho_t}\left[ \nabla_\theta V_{\pi_\theta}(s_t) \right] \longrightarrow 0.
$$

여기서 다음의 관계를 활용할 수 있다:

$$
\nabla_\theta \log \pi_\theta(a_t \vert s_t) = \frac{\nabla \pi_\theta(a_t \vert s_t)}{\pi_\theta(a_t \vert s_t)}.
$$

이 관계를 통해 $$\nabla_\theta \pi_\theta(a_r \vert s_t)$$를 $$\pi_\theta(a_t \vert s_t) \nabla_\theta \log \pi_\theta(a_t \vert s_t)$$로 대체하면:

$$
\begin{array}{l}
\nabla_\theta \eta(\pi_\theta) \\
= \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t \sim \rho_t}\left[ \sum_{a_t}\left( \nabla_\theta \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right] \\
= \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t \sim \rho_t}\left[ \sum_{a_t} \pi_\theta(a_t \vert s_t) \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right] \\
= \sum_{t=0}^\infty \mathbb{E}_{s_t \sim \rho_t, a_t \sim \pi_\theta(\cdot \vert s_t)} \left[ \gamma^t  \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right] \\
= \sum_{t=0}^\infty \mathbb{E}_{(s_t, a_t) \sim \pi_\theta} \left[ \gamma^t  \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right].
\end{array}
$$

이는 확률의 곱의 법칙에 의하여 다음과 같이 쓸 수 있다:

$$
\begin{array}{l}
\nabla_\theta \eta(\pi_\theta) \\
= \sum_{t=0}^\infty \mathbb{E}_{(s_{0:\infty}, a_{0:\infty}) \sim \pi_\theta} \left[ \gamma^t \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right] \\
= \sum_{t=0}^\infty \mathbb{E}_{\tau \sim \pi_\theta} \left[ \gamma^t \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right] \\
= \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right]. \ \ \ \ \text{Q.E.D.}
\end{array}
$$

## REINFORCE Algorithm
REINFORCE 알고리즘은 Monte Carlo Method 기반의 Policy Gradient 방법이다. 앞에서 Policy Gradient Theorem으로부터 우리는 다음의 결과를 활용할 수 있게 됐다:

$$
\nabla_\theta \eta(\pi_\theta) = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( \nabla_\theta \log \pi_\theta (a_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right].
$$

이를 통해, 주어진 에피소드 $$s_{0:\infty}, a_{0:\infty}$$에 대해서 시간 단계 $$t$$에서의 Policy Gradient의 Monte Carlo Estimator는 다음과 같이 표현될 수 있다:

$$
\nabla_\theta \eta(\pi_\theta) \approx \gamma^t \left( \nabla_\theta \log \pi_\theta (a_t \vert s_t) \right) Q_{\pi_\theta}(a_t, a_t).
$$

여기서 $$Q_{\pi_\theta}(s_t, a_t)$$의 Unbiased Estimator로 대신 $$R_t$$를 사용할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta} \left[ \left( \gamma^t \nabla_\theta \log \pi_\theta (a_t \vert s_t) \right) R_t \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{s_t, a_t}\left[ \gamma^t \nabla_\theta \log \pi_\theta (s_t \vert s_t) \mathbb{E}_{s_{t+1}, a_{t+1}, \cdots} [R_t] \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{s_t, a_t}\left[ \gamma^t \left(\nabla_\theta \log \pi_\theta (s_t \vert s_t) \right) Q_{\pi_\theta}(s_t, a_t) \right].
\end{array}
$$

$$\therefore \ \nabla_\theta \eta(\pi_\theta) \approx \gamma^t \nabla_\theta \log \pi_\theta(a_t \vert s_t) R_t.$$

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.02
    - 최초 게제
- 2022.07.10
    - Notation 수정
    - Policy Gradient Theorem 증명 부분 추가