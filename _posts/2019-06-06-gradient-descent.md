---
layout: post
use_math: true
title: "[Optimization] Gradient Descent에 대한 기본적인 내용 정리"
date: 2019-06-06 00:15:10
tagline: "머신 러닝 및 딥러닝에서 파라미터를 업데이트하는 경우에 가장 많이 사용되는 Optimization 방법인 Gradient Descent에 대한 기본적인 내용 정리"
categories:
- Optimization
tags:
- optimization
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-06-gradient-descent
---

Gradient Descent 또는 Gradient Ascent란 어떤 함수 $$f$$를 최적화하기 위해서 사용하는 Iterative한 알고리즘이다. 이 알고리즘의 원리를 파악하기 위해서는 먼저 다음의 개념을 살펴볼 필요가 있다.

## 함수의 입력 공간에서의 업데이트 방향 탐색
어떤 함수 $$f$$가 입력 $$\mathbf{x}$$가 살고있는 입력 공간 $$\mathcal{X}$$에 대하여 정의되어 있다고 가정하자. ($$f:\mathcal{X}\longmapsto \mathcal{Y}$$) 이 경우에 공간 $$\mathcal{X}$$에서 $$f$$를 최대화하는 어떤 $$\mathbf{x}^*$$를 찾을 수 있을 것이라고 가정하자. 즉, 함수 $$f$$는 공간 $$\mathcal{X}$$ 상에 유한한 함수값을 가지는 함수로 잘 정의가 되어있는 것이라고 가정한다. 임의의 $$\mathbf{x}\in \mathcal{X}$$에 대해서 $$\mathbf{x}^*$$의 방향으로 가기 위해서는 어떤 방향으로 $$\mathbf{x}$$를 움직일 수 있을 것일지에 대한 문제로 생각할 수 있을 것이다. 그리고 이 방향은 $$f$$를 최대한 증가시키는 방향이 될 것이다.

즉, 정리하면 임의의 $$\mathbf{x}_t\in \mathcal{X}$$에 대해서 $$f$$를 최대한 증가시키는 방향인 $$\mathbf{u}_t$$를 찾을 수 있고($$\vert \mathbf{u}_t \vert = 1$$라는 가정이 있음), 매번 $$\mathbf{x}_t$$를 아래와 같은 업데이트 룰을 통해서 Iterative하게 업데이트시키게 된다면 결국 $$\mathbf{x}^*$$로 점점 다가가게 될 것이라고 생각할 수 있다.

$$\mathbf{x}_{t+1} \longleftarrow \mathbf{x}_t + \alpha \mathbf{u}_t$$

여기서 $$\alpha \in \mathbb{R}$$는 임의의 양의 실수가 될 것이다. 이 경우 Local Extrema에 빠질 가능성도 물론 있지만 여기서는 함수 $$f$$가 Concave하다고 가정을 하도록 하자. 그 다음 문제는 임의의 $$\mathbf{x}_t\in \mathcal{X}$$에 대해서 $$\mathbf{u}_t$$를 찾을 수 있는지가 될 것이다. 이 문제를 풀기 위해서는 $$f(\mathbf{x}_t + \alpha \mathbf{u})$$를 Taylor Expansion으로 풀어서 써놓는 것에서 시작한다.

## Taylor Expansion
$$f(\mathbf{x}_t + \alpha \mathbf{u})$$에 대한 Taylor Expansion은 다음과 같다.

$$
\begin{align*}
f(\mathbf{x}_t + \alpha \mathbf{u})
& = f(\mathbf{x}_t) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u} + \frac{\alpha}{2}\mathbf{u}^{\text{T}}\nabla_{\mathbf{x}}^2 f(\mathbf{x}_t) \mathbf{u} + \cdots \\
& \approx f(\mathbf{x}_t) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u}
\end{align*}
$$

여기서 우리는 $$\mathbf{u}_t$$가 $$f(\mathbf{x}_t+\alpha \mathbf{u})$$를 최대화하는 Unit 벡터 $$\mathbf{u}$$가 되기를 원한다.

$$
\begin{align*}
\mathbf{u}_t
& = \arg \max_{\mathbf{u}} f(\mathbf{x}_t) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u} \\
& = \frac{1}{\vert \nabla_{\mathbf{x}}f(\mathbf{x}_t) \vert} \nabla_{\mathbf{x}}f(\mathbf{x}_t)
\end{align*}
$$

비슷한 방법으로 $$f$$를 최소화하기 위한 업데이트 룰도 다음과 같이 쓸 수 있다.

$$\mathbf{x}_{t+1} \longleftarrow \mathbf{x}_t - \alpha \mathbf{u}_t, \ \text{where}, \ \alpha \in \mathbb{R}, \alpha >0$$

즉 위와 같이 Gradient를 이용하여 함수 $$f$$를 Iterative하게 업데이트하는 방법을 Gradient Descent 또는 Gradient Ascent라고 한다. $$f$$를 최대화시키는 알고리즘을 Gradient Ascent, $$f$$를 최소화시키는 알고리즘을 Gradient Descent라고 한다.
