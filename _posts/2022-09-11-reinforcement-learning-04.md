---
layout: post
use_math: true
title: "[강화 학습] 4. Bootstrapping과 불확실성의 전파"
date: 2022-09-11 00:00:00
tagline: "Policy Gradient의 성능 향상을 위해서 사용되는 Bootstrapping 방법과 그 효과를 유발하는 원리를 불확실성의 전파를 통해서 기술"
categories:
- 강화 학습 스터디
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-09-11-reinforcement-learning-04
---

이번 포스트에서는 이전 [포스트](https://hcnoh.github.io/2022-07-13-reinforcement-learning-03)에서 분량 관계상 다루지 못하였던 Bootstrapping에 대해서 다루게 된다. 이를 위하여 불확실성의 전파(Propagation of Uncertainty)를 다루고 이를 바탕으로 Bootstrapping의 효과에 대해서 설명한다.

## 강화 학습에서의 편향(Bias)과 분산(Variance)
이전 포스트들에서 소개했던 방법들은 실제 가치 함수인 $$V_\pi(s_t)$$에 대한 Estimate $$\hat{v}_w(s_t)$$는 다음의 형식을 통해서 갱신이 이루어진다:

$$
w \longleftarrow w + \alpha (\text{Target} - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t).
$$

즉, 매 갱신마다 $$\hat{v}_w(s_t)$$는 $$\text{Target}$$을 향해서 한 걸음씩 다가가게 되는 것이다. 이러한 $$\text{Target}$$을 어떻게 정의하냐에 따라서 방법론에 대한 정의가 달라진다. 만약 $$\text{Target}$$으로 Return $$R_t$$를 사용한다면 이 방법론은 `Monte Carlo`(MC) 방법이 될 것이다. 만약 $$\text{Target}$$으로 $$r(s_t + a_t) + \gamma \hat{v}_w(s_{t+1})$$을 사용한다면 이 방법론은 `Temporal Difference`(TD) 방법이 될 것이다. (두 방법에 대한 자세한 사항은 추후에 설명하도록 하겠다.)

강화 학습에서의 `편향-분산 Tradeoff` (Bias-Variance Tradeoff)는 MC와 TD 방법의 차이로부터 확인할 수 있다. 강화 학습의 관점에서 MC 또는 TD 방법의 편향과 분산은 그들의 $$\text{Target}$$에 대한 편향과 분산으로 계산될 수 있다:

$$
\begin{array}{rl}
\text{Bias} & = \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ \text{Target} \right] - V_\pi(s_t), \\
\text{Variance} & = \text{Var}_{a_{t:\infty}, s_{t+1:\infty}} \left[ \text{Target} \right].
\end{array}
$$

## 불확실성의 전파 (Propagation of Uncertainty)
`불확실성의 전파`(Propagation of Uncertatinty)란 변수의 불확실성들이 그 불확실성으로 기반으로 함수의 불확실성에 미치는 영향을 말한다. 변수 집합 $$\mathbf{X} \in \mathbb{R}^n$$에 대한 임의의 (비선형) 함수 $$f:\mathbb{R}^n \rightarrow \mathbb{R}$$에 대해서, $$f$$는 `1차 테일러 확장`(First-order Taylor Expansion)을 통해서 특정 점 $$\boldsymbol{\mu}_\mathbf{X}$$ 근처에서 선형으로 근사화될 수 있다:

$$
f(\mathbf{X}) \approx f(\boldsymbol{\mu}_\mathbf{X}) + \sum_{i=1}^n \left. \frac{\partial f}{\partial X_i} \right\vert_{\boldsymbol{\mu}_\mathbf{X}} (X_i - \mu_{X_i}).
$$

만약 여기서 변수 사이의 상관성을 무시하던지, 또는 변수 사이의 독립성을 가정한다면 변수들은 다음의 공통된 형식을 가질 수 있다:

$$
\begin{array}{rl}
\mathbb{E}_{\mathbf{X}}[f] = \mu_f & \approx f(\mu_{\mathbf{X}}), \\
\text{Var}_\mathbf{X}[f] = \sigma_f^2 & \approx \sum_{i=1}^n \left( \left. \frac{\partial f}{\partial X_i} \right\vert_{\boldsymbol{\mu}_\mathbf{X}} \right)^2 \sigma_{X_i}^2.
\end{array}
$$

즉, 변수 $$\mathbf{X}$$의 분산에 의하여 함수 $$f$$의 분산도 영향을 받게 되는 것이다. 변수의 불확실성은 변수의 분산으로 표현된다. 다시 말해서 변수 $$\mathbf{X}$$의 불확실성이 함수 $$f$$의 불확실성으로 전파되는 것이다.

앞으로는 이러한 선형 근사를 사용하는 경우에는 변수 사이의 독립성을 자동으로 가정하도록 한다.

불확실성의 전파를 이용하여 앞으로 사용하게 될 다음의 두 가지 Lemma를 증명할 수 있다.

**Lemma 1)**

$$
\text{Var}_{\tau \sim \pi} \left[ R_t \right] \geq \text{Var}_{\tau \sim \pi} \left[ V_\pi(s_t) \right].
$$

Lemma 1의 증명은 다음과 같다. 먼저 $$\text{Var}_{\tau \sim \pi} \left[ R_t \right]$$를 계산하면:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi} \left[ R_t \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \sum_{k=0}^\infty \gamma^{2k} \text{Var}_{\tau \sim \pi} \left[ r(s_{t+k}, a_{t+k}) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \approx \sum_{k=0}^\infty \gamma^{2k} \left[ \left( \left. \frac{\partial r}{\partial s_{t+k}} \right|_{s_{t+k}, a_{t+k}} \right)^2 \sigma_{s_{t+k}}^2 + \left( \left. \frac{\partial r}{\partial a_{t+k}} \right|_{s_{t+k}, a_{t+k}} \right)^2 \sigma_{a_{t+k}}^2 \right].
\end{array}
$$

위의 계산 과정에서는 모든 $$s_{t+k}, a_{t+k}, k=0, 1, \cdots$$는 각각 서로 독립이라고 가정하였다. 마찬가지의 방법으로 $$\text{Var}_{\tau \sim \pi} \left[ V_\pi(s_t) \right]$$를 계산하면:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi} \left[ V_\pi(s_t) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right] \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ \mathbb{E}_{a_t}\left[ r(s_t, a_t) \right] + \mathbb{E}_{s_{t+1:\infty}, a_{t+1:\infty}} \left[ \sum_{k=1}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right] \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \approx \left( \left. \frac{\partial \mathbb{E}_{a_t}\left[ r(s_t, a_t) \right]}{\partial s_t} \right\vert_{\mu_{s_t}, \mu_{a_t}} \right)^2 \sigma_{s_t}^2 \\
\ \ \ \  \ \ \ \  \ \ \ \ \approx \left( \left. \frac{\partial r(s_t, \mu_{a_t})}{\partial s_t} \right\vert_{\mu_{s_t}, \mu_{a_t}} \right)^2 \sigma_{s_t}^2 \\
\ \ \ \  \ \ \ \  \ \ \ \ \approx \left( \left. \frac{\partial r}{\partial s_t} \right\vert_{\mu_{s_t}, \mu_{a_t}} \right)^2 \sigma_{s_t}^2.
\end{array}
$$

따라서: $$\text{Var}_{\tau \sim \pi} \left[ R_t \right] \geq \text{Var}_{\tau \sim \pi} \left[ V_\pi(s_t) \right]$$이다.

**Lemma 2)**

$$
\text{Var}_{\tau \sim \pi} \left[ R_t \right] \geq \text{Var}_{\tau \sim \pi} \left[ r(s_t, a_t) + \gamma V_\pi(s_{t+1}) \right].
$$

증명은 다음과 같다:

$$
\begin{array}{l}
\text{Var}_{\tau \sim \pi} \left[ r(s_t, a_t) + \gamma V_\pi(s_{t+1}) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ r(s_t, a_t) \right] + \text{Var}_{\tau \sim \pi} \left[ \gamma V_\pi(s_{t+1}) \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ \leq \text{Var}_{\tau \sim \pi} \left[ r(s_t, a_t) \right] + \text{Var}_{\tau \sim \pi} \left[ \gamma R_{t+1} \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ r(s_t, a_t) + \gamma R_{t+1} \right] \\
\ \ \ \  \ \ \ \  \ \ \ \ = \text{Var}_{\tau \sim \pi} \left[ R_t \right].
\end{array}
$$

여기에서도 모든 $$s_{t+k}, a_{t+k}, k=0, 1, \cdots$$는 각각 서로 독립이라는 가정을 사용하였다.

## Bootstrapping
`Bootstrapping`이란 현재 상황에서 어떻게든 한다는 의미이다. 즉, 어떤 시스템이 이미 가지고 있는 기능이 없는 상태에서 그 시스템을 `어떻게든` 작동시키는 것이다. (http://www.aistudy.co.kr/computer/bootstrapping.htm)

그렇다면 강화 학습에서의 Bootstrapping은 구체적으로 어떤 의미일까? 강화 학습에서의 Bootstrapping은 정확한 값이 아닌 다른 Estimate들을 바탕으로 Estimate를 갱신하는 방법을 말한다. 정확한 값을 모르더라도 현재 알 수 있는 Estimate를 통해서 `어떻게든` 갱신한다는 점에서 Bootstrapping이라고 부를 수 있게 된다.

이전 방법들에서 $$\text{Target}$$은 실제 가치 $$V_\pi(s_t)$$의 Estimator, 즉 $$R_t$$ 또는 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$ 등이 되어야만 한다는 점을 확인했었다.

여기에서 우리는 $$R_t$$는 관찰된 값이며 따라서 정확하다는 점을 확인할 수 있다. 또한 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$은 $$R_t$$보다 낮은 분산을 갖는 것을 Lemma 2를 통해서 확인할 수 있었다.

그렇다면 무조건 낮은 분산을 갖는 Estimator인 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$을 사용하는 것이 좋은 것 아닐까? 여기에는 한 가지 문제가 발생한다. 우리는 $$\text{Target}$$으로 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$을 직접적으로 사용할 수 없다. 왜냐하면 시간 단계 $$t+1$$에서 우리는 $$V_\pi(s_{t+1})$$의 정확한 값을 관찰할 수 없기 때문이다. $$V_\pi(s_{t+1})$$의 값을 관찰하려면 최종 상태까지 진행하면서 모든 보상들을 관찰해야 할 것이다:

$$
\begin{array}{rl}
V_\pi(s_{t+1}) & = \mathbb{E}_{a_{t+1:\infty}, s_{t+2:\infty}} \left[ R_t \right] \\
& = \mathbb{E}_{a_{t+1:\infty}, s_{t+2:\infty}} \left[ \sum_{k=0}^\infty \gamma^k r(s_{t+k}, a_{t+k}) \right].
\end{array}
$$

이러한 관찰은 $$R_t$$를 관찰하는 것 대비 분산에서의 이점을 누릴 수 없게 된다.

대신 실제 가치 $$V_\pi(s_{t+1})$$을 대신하여 Estimate $$\hat{v}_w(s_{t+1})$$를 사용할 수 있다. 여기서 $$R_t$$ 대신 $$r(s_t, a_t) + \gamma \hat{v}_w(s_{t+1})$$을 Estimator로 사용하는 방법을 `Bootstrapping`이라고 한다.

여기서 $$r(s_t, a_t) + \gamma \hat{v}_w(s_{t+1})$$는 실제 $$\text{Target}$$인 $$r(s_t, a_t) + \gamma V_\pi(s_{t+1})$$와 정확히 같은 값은 아닐 것이다. 이는 이 Estimator에 편향이 있음을 의미한다:

$$
\begin{array}{l}
\mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ r(s_t, a_t) + \gamma \hat{v}_w(s_{t+1}) \right] - V_\pi(s_t) \\
= \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ r(s_t, a_t) + \gamma \hat{v}_w(s_{t+1}) - R_t \right] \\
= \mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ \gamma \hat{v}_w(s_{t+1}) - \gamma R_t \right] \\
= \gamma \mathbb{E}_{s_{t+1}} \left[ \hat{v}_w(s_{t+1}) - V_\pi(s_{t+1}) \right] \\
\neq 0.
\end{array}
$$

반면 $$R_t$$는 $$V_\pi(s_t)$$에 대한 Unbiased Estimator이다:

$$
\mathbb{E}_{a_{t:\infty}, s_{t+1:\infty}} \left[ R_t \right] - V_\pi(s_t) = 0.
$$

즉, 이 결과를 통해서 우리는 Bootstrapping이 $$\text{Target}$$의 Estimator를 선택할 때 분산을 줄이는 대신 편향이 발생한 Estimator를 활용할 수 있게 해준다. 분산이 작다면 더 안정적으로 훈련이 진행되어 더 빠른 수렴을 가능하게 할 것이고, 편향이 작다면 훈련 과정에서 더 정확한 값으로의 수렴을 가능하게 할 것이다. 이는 Tradeoff 관계에 있는 것이며 상황에 맞게 잘 사용해야 할 것이다.

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Wikipedia: Bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping)
- [http://www.aistudy.co.kr/computer/bootstrapping.htm](http://www.aistudy.co.kr/computer/bootstrapping.htm)

## 수정 사항
- 2022.09.11
    - 최초 게제
- 2022.09.12
    - 불확실성의 전파 내용 정리
- 2022.09.15
    - Bootstrapping 내용 정리
- 2024.03.10
    - Lemma 1 증명 수식에서 누락된 부분 추가