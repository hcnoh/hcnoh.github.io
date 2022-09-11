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
Bootstrapping이란 현재 상황에서 어떻게든 한다는 의미이다. 즉, 어떤 시스템이 이미 가지고 있는 기능이 없는 상태에서 그 시스템을 `어떻게든` 작동시키는 것이다. (http://www.aistudy.co.kr/computer/bootstrapping.htm)

그렇다면 강화 학습에서의 Bootstrapping은 구체적으로 어떤 의미일까? 이전 포스트에서 설명했던 방법들을 통해서 그 의미를 확인해보도록 하자. 이전 방법들에서 실제 가치 함수 $$V_\pi(s_t)$$의 Estimate $$\hat{v}_w(s_t)$$는 다음의 형식을 통해서 갱신이 이루어진다:

$$
w \longleftarrow w + \alpha (\text{Target} - \hat{v}_w(s_t)) \nabla_w \hat{v}_w(s_t).
$$

즉, 매 갱신마다 $$\hat{v}_w(s_t)$$는 $$\text{Target}$$을 향해서 한 걸음씩 다가가게 되는 것이다. 이러한 $$\text{Target}$$을 어떻게 정의하냐에 따라서 방법론에 대한 정의가 달라진다. 만약 $$\text{Target}$$으로 Return $$R_t$$를 사용한다면 이 방법론은 Monte Carlo (MC) 방법이 될 것이다. 만약 $$\text{Target}$$으로 $$r(s_t + a_t) + \gamma \hat{v}_w(s_{t+1})$$을 사용한다면 이 방법론은 Temporal Difference (TD) 방법이 될 것이다. TD 방법에서 $$R_t$$ 대신 $$r(s_t + a_t) + \gamma \hat{v}_w(s_{t+1})$$을 사용하는 것을 강화 학습에서의 Bootstrapping이라고 한다.

## 불확실성의 전파

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Wikipedia: Bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping)
- [http://www.aistudy.co.kr/computer/bootstrapping.htm](http://www.aistudy.co.kr/computer/bootstrapping.htm)

## 수정 사항
- 2022.09.11
    - 최초 게제