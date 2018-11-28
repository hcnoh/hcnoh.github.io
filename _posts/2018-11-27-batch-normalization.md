---
layout: post
use_math: true
title: "Batch Normalization 개념 정리"
date: 2018-11-27 19:54:59
tagline: "딥러닝에서 자주 쓰이는 Regularization 방법인 Batch Normalization에 대한 논문을 읽고 개념을 정리"
categories:
- Deep Learning
tags:
- deep learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-27-batch-normalization
---

이번 포스팅은 다음의 논문을 스터디하여 정리하였다:
- [링크1](https://arxiv.org/abs/1502.03167)

## Stochastic Gradient Descent
딥러닝을 학습하는 알고리즘으로 가장 일반적으로 많이 쓰이는 방법은 바로 Stochastic Gradient Descent (SGD)이다. SGD를 설명하기 전 Gradient Descent를 먼저 간단하게 설명하면 파라미터 $$\theta$$에 대해서 정의된 특정 Cost $$L(\theta)$$를 최적화하기 위하여 $$L(\theta)$$의 Gradient를 이용하는 방법이다. $$L$$의 Gradient는 파라미터 $$\theta$$ 스페이스 상에서 $$L$$을 최대화하는 방향의 벡터값을 갖게 되므로 여기에 Learning rate $$\alpha$$를 곱해서 현재 파라미터 $$\theta$$에서 빼주면 현재 Cost $$L$$이 감소하는 방향으로 파라미터 $$\theta$$를 업데이트할 수 있게 된다.

즉, 업데이트 룰은 다음과 같다.

$$ \theta_{k+1} = \theta_k - \alpha \nabla_{\theta} L(\theta) |_{\theta=\theta_k} $$

이러한 업데이트를 통하여 다음의 최적화 문제를 풀 수 있게 된다.

$$\theta = \arg \min_\theta L(\theta)$$

Data-driven 방식을 통하여 Gradient Descent를 사용하기 위해서는 주어진 학습용 데이터에 대한 Cost를 정의하여 그 Cost를 최소화하기 위한 $$\theta$$를 찾아내야 한다. 즉, 주어진 데이터셋 $$D$$에 대한 최적화 문제는 다음과 같다.

$$\theta = \arg \min_\theta L(x, \theta), \forall x \in D$$

이러한 최적화 문제를 풀기 위한 Gradient Descent 업데이트 룰 또한 다음과 같이 정의할 수 있을 것이다.

$$\theta_{k+1} = \theta_k - \alpha \mathbb{E}_{x\in D} \nabla_{\theta} L(x, \theta) |_{\theta=\theta_k}$$
