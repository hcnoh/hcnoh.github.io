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

$$\theta_{k+1} = \theta_k - \alpha \mathbb{E}_{x\in D} [ \nabla_{\theta} L(x, \theta) |_{\theta=\theta_k} ]$$

실제로 이 룰을 활용하는 경우에는 모든 데이터셋 $$D$$를 전부 활용하기 힘든 여러가지 이유들로 인하여 미니배치를 반복적으로 샘플링하여 업데이트하는 방법인 Stochastic Gradient Descent를 사용한다. 미니배치의 크기를 $$N$$이라고 했을 때, 실제 Gradient를 다음과 같은 Stochastic Gradient로 근사화를 할 수 있다.

$$\mathbb{E}_{x\in D}[ \nabla_\theta L(x, \theta) ] \approx \frac{1}{N} \sum_{i=1}^N \nabla_\theta L(x_i, \theta)$$

물론 이러한 SGD같은 방법에도 ADAM, Adagrad 등의 여러 베리에이션이 존재하지만 그 내용들은 다음에 다루도록 하고 여기서는 이정도만 정리하고 넘어가도록 하겠다. 어쨌든 [Batch Normalization 논문](https://arxiv.org/abs/1502.03167)에서는 SGD가 다음과 같은 특징들이 있다고 정리하고 있다.
  - Stochastic Gradient는 실제 Gradient의 추정값이며 이것은 미니배치의 크기 $$N$$이 커질수록 더 정확한 추정값을 가지게 된다.
  - 미니배치를 뽑아서 연산을 수행하기 때문에 최신 컴퓨팅 플랫폼에 의하여 병렬적인 연산 수행이 가능하여 더욱 효율적이다.

## SGD의 문제점
SGD의 문제점이라지만 사실 딥 뉴럴넷의 문제점이라고 보는 것이 맞을 듯 싶다. 논문에서는 레이어를 여러 층으로 쌓음으로써 생기는 문제점들에 대해서 얘기하고 있다. 어쨌든 논문에서 얘기하는 SGD의 문제점은 뉴럴넷을 학습하기 위한 하이퍼 파라미터들의 초기값 설정을 굉장히 신중하게 해줘야만 한다는 점이다. 이전 네트워크에서의 파라미터 변화는 그 다음, 그 다음 네트워크들을 거치며 점점 변화량이 증폭된다. 따라서 신중하지 못한 파라미터 초기화의 문제점은 레이어가 쌓이면서 더욱 큰 문제로 발전하게 될 것이다.

뉴럴넷의 입력 분포의 변화가 일어난다면 뉴럴넷의 각 레이어들은 그 새로운 분포에 끊임없이 적응해야하는 문제가 있다. 이 경우에 뉴럴넷은 Covariate Shift를 겪었다고 말한다.

다음과 같은 뉴럴넷 연산을 살펴보자.

$$ l = F_2(F_1(u, \theta_1), \theta_2) $$

$$F_1$$, $$F_2$$는 임의의 Transformation이고, 파라미터 $$\theta_1$$, $$\theta_2$$는 Loss $$l$$을 최소화하는 방향으로 학습이 진행된다.

여기서 하위 네트워크 $$F_2$$의 파라미터 $$\theta_2$$는 다음과 같은 방식으로 학습이 된다고 볼 수 있다.

$$ \theta_2 \leftarrow \theta_2 - \frac{\alpha}{m}\sum_{i=1}^m \nabla_{\theta_2}F_2(x_i, \theta_2) $$

이건 마치 $$F_2(x, \theta_2)$$를 단독으로 학습하는 것과 동일하다. 따라서 학습을 더 원할하게 만들어주는 입력 분포는 모델을 구성하는 하위 네트워크들에 대해서도 동일하게 적용된다고 볼 수 있다!

여기까지 나온 개념을 일단 정리해 보자면:
  - 뉴럴넷의 입력 분포 변화는 일어나지 않는 것이 좋다! 왜? 뉴럴넷 파라미터들이 그 분포에 새로 적응해야 하기 때문!
    - 즉, 학습을 원할하게 하기 위해 효율적인 입력 분포는 일정하게 유지되는 입력 분포
  - 네트워크를 여러 층을 쌓은 경우에 하위 네트워크에 대한 입력 분포의 효율성에도 동일하게 적용된다!
    - 즉 앞단의 레이어의 출력(즉, 뒷단의 레이어의 입력이 될 값)의 분포도 일정하게 유지하는 것이 좋음!
    
논문에서 언급하고있는 또 다른 문제점은 Gradient Descent 및 뉴럴넷의 Nonlinearity에 의한 Saturated Regime에 관한 문제이다. 일반적으로 뉴럴넷의 Activation은 Sigmoid를 사용하는데 이 Sigmoid의 특성상 절대값이 일정 수준 이상으로 큰 입력에 대해서는 Gradient가 거의 사라지는 문제가 발생한다.

즉, 다음의 뉴럴넷 구조를 살펴보자.

$$z = g(Wu + b)$$

여기서 g는 다음과 같은 Sigmoid 함수이다.

$$g = \frac{1}{1 + e^{-x}}$$

이때 Sigmoid 함수의 특성상 입력 $$x$$의 절대값의 크기가 커질수록, 즉 $$0$$으로부터 멀어질수록 Gradient 값이 매우 작아지게 된다. 따라서 $$0$$ 근방이 아닌 입력들에 대해서는 잘 학습이 되지 않을 것이며 만약 $$x$$의 분포가 $$0$$으로부터 멀어진다면 더이상 학습이 진행이 되지 않고 모델은 특정 파라미터 값에서 Saturation이 되게 될 것이다. 이 현상을 Saturated Regime에 빠졌다고 하는 것 같다. Saturated Regime에 대해서는 좀 더 조사해보고 정리하도록 하겠다

어쨌든 이러한 이유들이 뉴럴넷 모델의 각 레이어들의 입력들의 분포를 일정하게 유지시켜주는 것이 왜 필요한지에 대한 설명이 될 것 같다.

## Covariate Shift를 줄이기 위한 시도
먼저 Internal Covariate Shift에 대한 정의를 하고 넘어가도록 하자.

- Internal Covariate Shift의 정의: 네트워크의 학습 도중에 파라미터의 변화로 인한 네트워크 Activation(출력)들의 분포 변화

학습 효율을 높이기 위해서는 이런 Internal Covariate Shift를 줄이기 위한 노력이 필요하다. 기존의 여러 연구 결과들은 입력값들이 Whitened된다면, 즉, Zero Mean과 Unit Variance를 가지게 되고 각각의 입력값들이 Decorrelated된다면, 뉴럴넷이 훨씬 빠르게 수렴할 것이라고 말하고 있다. 또한 모든 레이어들이 같은 Whitening 방식을 공유한다면 훨씬 이득을 가질 수 있다고 한다.