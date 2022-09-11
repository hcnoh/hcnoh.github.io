---
layout: post
use_math: true
title: "[Reinforcement Learning] 4. Bootstrapping과 불확실성의 전파"
date: 2022-09-11 00:00:00
tagline: "Policy Gradient의 성능 향상을 위해서 사용되는 Bootstrapping 방법과 그 효과를 유발하는 원리를 불확실성의 전파를 통해서 기술"
categories:
- Reinforcement Learning Study
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

## Bootstrapping
`Bootstrapping`이란 현재 상황에서 어떻게든 한다는 의미이다. 즉, 어떤 시스템이 이미 가지고 있는 기능이 없는 상태에서 그 시스템을 `어떻게든` 작동시키는 것이다. (http://www.aistudy.co.kr/computer/bootstrapping.htm)

그렇다면 강화 학습에서의 Bootstrapping은 구체적으로 어떤 의미일까? 이전 포스트에서 설명했던 방법들을 통해서 그 의미를 확인해보도록 하자. 이전 방법들에서 실제 가치 함수 $$V_\pi(s_t)$$의 Estimate $$\hat{v}_w(s_t)$$는 다음의 형식을 통해서 갱신이 이루어진다:

$$
w \longleftarrow w + \alpha (\text{Target} - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t).
$$

즉, 매 갱신마다 $$\hat{v}_w(s_t)$$는 $$\text{Target}$$을 향해서 한 걸음씩 다가가게 되는 것이다. 이러한 $$\text{Target}$$을 어떻게 정의하냐에 따라서 방법론에 대한 정의가 달라진다. 만약 $$\text{Target}$$으로 Return $$R_t$$를 사용한다면 이 방법론은 `Monte Carlo`(MC) 방법이 될 것이다. 만약 $$\text{Target}$$으로 $$r(s_t + a_t) + \gamma \hat{v}_w(s_{t+1})$$을 사용한다면 이 방법론은 `Temporal Difference`(TD) 방법이 될 것이다. (두 방법에 대한 자세한 사항은 추후에 설명하도록 하겠다.) TD 방법에서 $$R_t$$ 대신 $$r(s_t + a_t) + \gamma \hat{v}_w(s_{t+1})$$을 사용하는 것을 강화 학습에서의 Bootstrapping이라고 한다.

## 불확실성의 전파 (Propagation of Uncertainty)
`불확실성의 전파`(Propagation of Uncertatinty)란 변수의 불확실성들이 그 불확실성으로 기반으로 함수의 불확실성에 미치는 영향을 말한다. 변수 집합 $$\mathbf{X} \in \mathbb{R}^n$$에 대한 임의의 (비선형) 함수 $$f:\mathbb{R}^n \rightarrow \mathbb{R}$$에 대해서, $$f$$는 1차 테일러 확장(First-order Taylor Expansion)을 통해서 특정 점 $$\boldsymbol{\mu}_\mathbf{X}$$ 근처에서 선형으로 근사화될 수 있다:

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

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Wikipedia: Bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping)
- [http://www.aistudy.co.kr/computer/bootstrapping.htm](http://www.aistudy.co.kr/computer/bootstrapping.htm)

## 수정 사항
- 2022.09.11
    - 최초 게제
- 2022.09.12
    - 불확실성의 전파 내용 정리