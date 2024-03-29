---
layout: post
use_math: true
title: "[강화 학습] 3. Baseline Method & Actor-Critic Method"
date: 2022-07-13 00:00:00
tagline: "Policy Gradient의 성능 향상을 위해서 Baseline Method를 포함한 기본적인 방법론들을 다루고 이를 바탕으로 Actor-Critic Method에 대해서 기술"
categories:
- 강화 학습 스터디
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
`Baseline Method`란 Policy Gradient $$\nabla_\theta \eta(\pi_\theta)$$에 대한 Estimator의 분산을 줄여주는 기본적인 방법이다. 이전 포스트에서 소개한 Policy Gradient Estimator는 다음과 같다:

$$
\nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t.
$$

이를 Baseline $$b(s_t)$$를 사용하여 다음과 같이 써보자:

$$
\nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)).
$$

그러면 우리는 다음과 같은 결과를 유도할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\mathbb{E}_{a_t}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t) \right] \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\sum_{a_t}\nabla_\theta \pi_\theta(a_t \vert s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right] - \mathbb{E}_{s_t}\left[ b(s_t)\nabla_\theta \sum_{a_t} \pi_\theta(a_t \vert s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)R_t \right].
\end{array}
$$

여기서는 다음의 연산 결과를 사용했다:

$$
\nabla_\theta \sum_{a_t} \pi_\theta(a_t \vert s_t) = \nabla_\theta 1 = 0.
$$

즉, Baseline을 사용한 Estimator는 기존 Estimator와 마찬가지로 Policy Gradient에 대한 Unbiased Estimator인 것을 확인했다. 이 Estimator는 기존 Estimator의 분산을 줄여주는데 사용될 수 있다. 이는 다음의 연산 과정을 통해 확인할 수 있다:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
= \mathbb{E}_{\tau \sim \pi_\theta}\left[ \left( \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right)^2 \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \right)^2 \\
= \mathbb{E}_{\tau \sim \pi_\theta}\left[ \left( \nabla_\theta\log\pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right)^2 \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)R_t \right] \right)^2 \\
= \mathbb{E}_{s_t, a_t}\left[ \left(\nabla_\theta\log\pi_\theta(a_t \vert s_t) \right)^2 \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[(R_t - b(s_t))^2\right] \right] \\
\ \ \ \ - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta\log\pi_\theta(a_t \vert s_t)R_t \right] \right)^2.
\end{array}
$$

만약 우리가 $$b(s_t)$$로 하여금 다음을 만족하게끔 정의한다면:

$$
\mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ \left( R_t - b(s_t) \right)^2 \right] \leq \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ R_t^2 \right],
$$

우리는 다음의 관계를 획득할 수 있다:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq \mathbb{E}_{s_t, a_t}\left[ \left( \nabla_\theta \log \pi_\theta(a_t \vert s_t) \right)^2 \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ R_t^2 \right] \right] - \left( \mathbb{E}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) R_t \right] \right)^2 \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right].
\end{array}
$$

즉, Baseline은 Estimator의 분산을 줄여주게 된다:

$$
\text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)(R_t - b(s_t)) \right] \leq \text{Var}_{\tau \sim \pi_\theta}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t)R_t \right].
$$

미분가능한 Baseline을 $$\hat{v}_w$$로 정의하자. 또한 이를 통해 다음의 손실 함수(Loss Function)를 고려해보자:

$$
\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( R_t - \hat{v}_w(s_t) \right)^2 \right].
$$

이 손실 함수를 감소시키는 Gradient는 다음과 같이 계산될 수 있다:

$$
\begin{array}{l}
-\nabla_w \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( R_t - \hat{v}_w(s_t) \right)^2 \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = -\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \nabla_w \left( R_t - \hat{v}_w(s_t) \right)^2 \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \cdot 2(R_t - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \propto\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \cdot (R_t - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t) \right].
\end{array}
$$

이 Gradient는 손실 함수를 감소시켜 다음의 상황을 유도하게 된다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( R_t - \hat{v}_w(s_t) \right)^2 \right] \\
= \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t, a_t} \left[ \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ \left( R_t - \hat{v}_w(s_t) \right)^2 \right] \right] \longrightarrow 0 \\
\Longrightarrow \mathbb{E}_{s_t, a_t} \left[ \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ \left( R_t - \hat{v}_w(s_t) \right)^2 \right] \right] \longrightarrow 0 \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq \mathbb{E}_{s_t, a_t}\left[ \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ R_t \right]^2 \right].
\end{array}
$$

따라서, 위의 Gradient는 원래 Estimator의 분산을 줄여주게끔 하는 $$\hat{v}_w$$를 찾게 된다. 또한 추가적으로 이 Gradient는 손실 함수를 최소화하며 $$\hat{v}_w$$로 하여금 상태 가치 함수 $$V_{\pi_\theta}$$를 근사화하도록 한다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( R_t - \hat{v}_w(s_t) \right)^2 \right] = 0 \\
\ \ \ \  \ \ \ \  \ \ \ \ \Longrightarrow \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t \left( R_t - \hat{v}_w(s_t) \right) \right] = 0 \\
\ \ \ \  \ \ \ \  \ \ \ \ \Longrightarrow \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t} \left[ \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ R_t \right] - \hat{v}_w(s_t) \right] = 0 \\
\ \ \ \  \ \ \ \  \ \ \ \ \Longrightarrow \sum_{t=0}^\infty \gamma^t \mathbb{E}_{s_t} \left[ V_{\pi_\theta}(s_t) - \hat{v}_w(s_t) \right] = 0.
\end{array}
$$

## REINFORCE + Baseline Method
위의 결과를 바탕으로 Baseline Method를 도입한 REINFORCE 알고리즘은 다음과 같이 정리될 수 있다:

- REINFORCE + Baseline 알고리즘
    - 입력: 미분가능한 매개변수화된 정책 $$\pi_\theta(a \vert s)$$, 미분가능한 매개변수화된 상태 가치 $$\hat{v}_w(s)$$.
    - 초매개변수: Learning Rate $$\alpha^\theta > 0, \alpha^w > 0$$.
    - 정책 매개변수 $$\theta$$ 및 상태 가치 가중치 $$w$$ 초기화.
    - 반복:
        - 정책 $$\pi_\theta$$를 따르며 에피소드 $$\tau=(s_0, a_0, s_1, a_1, \cdots)$$ 생성.
        - 에피소드의 각 단계 $$t=0,1,\cdots, T-1$$에 대해서:
        $$
        \begin{array}{rl}
        R_t & \longleftarrow \text{Return from step $t$} \\
        \delta & \longleftarrow R_t - \hat{v}_w(s_t) \\
        w & \longleftarrow w + \alpha^w \gamma^t \delta \nabla_w \hat{v}_w(s_t) \\
        \theta & \longleftarrow \theta + \alpha^\theta \gamma^t \delta \nabla_\theta \log \pi_\theta(a_t \vert s_t)
        \end{array}
        $$

## Whitening (Return Normalization)
분산을 줄이는 또 다른 방법으로는 `Whitening`이 있다. Whitening은 Return을 정규화하는 방법을 말한다. 특정 에피소드를 진행하는 도중에서 발생한 모든 Return들의 평균 및 표준편차를 통해서 각각의 시간 단계의 Return $$R_t$$를 정규화하는 방식으로 Whitening을 수행할 수 있다:

$$
\begin{array}{rl}
R^*_t & \longleftarrow \frac{R_t - \bar{R}}{\sigma_R}, \\
\theta & \longleftarrow \theta + \alpha \gamma^t \nabla_\theta \log \pi_\theta(a_t \vert s_t) R^*_t.
\end{array}
$$

이를 통해 ($$\sigma_R > 1$$인 경우) Whitening이 분산을 줄이는데 도움이 되는 것을 확인할 수 있다:

$$
\text{Var}\left[ R^*_t \right] = \text{Var}\left[ \frac{R_t - \bar{R}}{\sigma_R} \right] = \frac{1}{\sigma_R^2} \text{Var}\left[ R_t \right].
$$

## Actor-Critic Method
위에서 Baseline을 설명하면서 우리는 Baseline $$\hat{v}_w(s)$$가 상태 가치 함수 $$V_\pi(s)$$를 근사화하도록 시도한다는 사실을 확인할 수 있었다. 여기에 추가로 기존 Policy Gradient에서 Return $$R_t$$ 대신 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$을 활용하여도 Policy Gradient의 Unbiasness는 해치지 않는다는 것을 다음을 통해서 확인할 수 있다:

$$
\begin{array}{l}
\mathbb{E}_{\tau \sim \pi_\theta} \left[ \nabla_\theta \log \pi_\theta (a_t \vert s_t) (r(s_t, a_t) + \gamma V_\pi(s_{t+1})) \right] \\
= \mathbb{E}_{s_t, a_t}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) (r(s_t, a_t) + \gamma \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ V_\pi(s_{t+1}) \right]) \right] \\
= \mathbb{E}_{s_t, a_t}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) (r(s_t, a_t) + \gamma \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ \mathbb{E}_{s_{t+2:\infty}, a_{t+1:\infty}} \left[ R_{t+1} \right] \right]) \right] \\
= \mathbb{E}_{s_t, a_t}\left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) (r(s_t, a_t) + \gamma \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}}\left[ R_{t+1} \right]) \right] \\
= \mathbb{E}_{\tau \sim \pi_\theta} \left[ \nabla_\theta \log \pi_\theta(a_t \vert s_t) (r(s_t, a_t) + \gamma R_{t+1}) \right] \\
= \mathbb{E}_{\tau \sim \pi_\theta} \left[ \nabla_\theta \log \pi_\theta (a_t \vert s_t) R_t \right].
\end{array}
$$

여기서 $$R_t$$ 대신 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$을 활용하는 방법을 `Bootstrapping`이라고 한다. Bootstrapping에 대해서는 다음 포스트에서 자세히 설명하도록 하겠다. 추가로 $$V_\pi(s)$$ 대신 매개변수 $$w$$로 매개변수화된 함수 $$\hat{v}_w(s)$$를 활용하게 된다. 여기서 $$\hat{v}_w(s)$$는 실제 가치 함수 $$V_\pi(s)$$에 대한 `Critic`으로 정의한다. 이 알고리즘을 통해서 `Actor`의 역할을 하는 Agent의 행동을 평가하겠다는 의미이다. 이 알고리즘을 `Actor-Critic 알고리즘`으로 부르는 이유이다.

## Actor-Critic Algorithm
다음은 Actor-Critic 알고리즘을 정리한 것이다:

- REINFORCE + Baseline 알고리즘
    - 입력: 미분가능한 매개변수화된 정책 $$\pi_\theta(a \vert s)$$, 미분가능한 매개변수화된 상태 가치 $$\hat{v}_w(s)$$.
    - 초매개변수: Learning Rate $$\alpha^\theta > 0, \alpha^w > 0$$.
    - 정책 매개변수 $$\theta$$ 및 상태 가치 가중치 $$w$$ 초기화.
    - 반복:
        - 정책 $$\pi_\theta$$를 따르며 에피소드 $$\tau=(s_0, a_0, s_1, a_1, \cdots)$$ 생성.
        - 에피소드의 각 단계 $$t=0,1,\cdots, T-1$$에 대해서:
        $$
        \begin{array}{rl}
        \delta & \longleftarrow r(s_t, a_t) + \gamma \hat{v}_w(s_{t+1}) - \hat{v}_w(s_t) \ \ (\hat{v}_w(s_T) = 0) \\
        w & \longleftarrow w + \alpha^w \gamma^t \delta \nabla_w \hat{v}_w(s_t) \\
        \theta & \longleftarrow \theta + \alpha^\theta \gamma^t \delta \nabla_\theta \log \pi_\theta(a_t \vert s_t)
        \end{array}
        $$

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.14
    - 최초 게제
- 2022.07.30
    - REINFORCE + Baseline Method 및 Whitening 내용 정리
- 2022.09.11
    - Notation 수정
    - Actor-Critic Method, Actor-Critic Algorithm 내용 정리