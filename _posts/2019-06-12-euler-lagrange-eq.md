---
layout: post
use_math: true
title: "[Classical Mechanics] Euler-Lagrange Equation 정리"
date: 2019-06-12 23:42:00
tagline: "Euler-Lagrange Equation 유도 및 관련 내용 정리"
categories:
- Physics
tags:
- physics
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-12-euler-lagrange-eq
---

## Galilean Transformation
Galilean Transformation이란 어떤 Inertial Frame (관성계) $$\mathcal{S}$$에서의 균일한 운동으로부터 또 다른 Inertial Frame $$\mathcal{S}'$$에서의 균일한 운동으로의 Mapping이라고 정의한다.

Galilean Transformation의 종류는 아래와 같다:
- Temporal Translation: $$t \rightarrow t + \Delta t$$
- Spatial Translation: $$\mathbf{r} \rightarrow \mathbf{r} + \Delta \mathbf{r}$$
- Rotational Translation: $$\mathbf{r} \rightarrow \mathcal{R}\mathbf{r}, \ \mathcal{R}\in \mathcal{O}(3)$$
- Galilean Boost: $$\mathbf{r} \rightarrow \mathbf{r} + \mathbf{v} t$$

여기서 각각의 변수는
- $$t$$: 시간
- $$\mathbf{r}$$: 위치 벡터, $$\mathbf{r} \in \mathbb{R}$$
- $$\mathbf{v}$$: 속도 벡터

## Generalized Coordinates
`뉴턴 역학`에서는 외부에서 물체에 미치는 `힘`에 관심을 가지고 그 힘을 기술하기 위해서 `벡터량`들을 주로 다루게 된다. 따라서 벡터를 표현하기에 유용한 Cartesian Coordinate와 같은 좌표계를 사용하게 된다. 하지만 앞으로 다루려는 Lagrangian은 `라그랑주 역학`의 관점에서 다루게 되는 물리량이며 라구랑주 역학은 물체의 Kinetic Energy나 Potential Energy와 같은 `스칼라량`들에 관심을 가지게 된다. 이러한 스칼라량에 관한 분석을 용이하게 하기 위하여 라그랑주 역학은 Generalized Coordinate를 사용하게 된다.

Generalized Coordinates의 정의는 시간에 따른 System의 위치를 Unique하게 나타낼 수 있는 독립적인 물리량들을 좌표계로 구성하는 것을 말한다. 따라서 Generalized Coordinates $$\mathbf{q}$$의 차원(좌표의 개수)은 운동의 자유도와 일치하게 된다. 일반적으로 운동을 스칼라량으로 기술하기 위하여 Generalized Coordinates를 사용하게 된다.

![](/assets/img/2019-06-12-euler-lagrange-eq/2019-06-12-euler-lagrange-eq_2019-06-23-00-27-39.png)
(출처: [Wikipedia](https://ko.wikipedia.org/wiki/%EC%9D%BC%EB%B0%98%ED%99%94_%EC%A2%8C%ED%91%9C))

예를 들어보자. 위의 그림은 평면 위에서 운동하는 이중진자의 예시를 설명하는 그림이다. 위의 운동을 Cartesian Coordinate로 기술하면 $$(x_1, y_1, x_2, y_2)$$의 4개의 좌표를 사용해야 한다. 하지만 이 운동의 자유도는 2이기 때문에 2개의 좌표 $$(\theta_1, \theta_2 )$$로 이 운동을 기술할 수 있을 것이다. 따라서 여기서 사용되는 Generalized Coordinates $$\mathbf{q}$$는 $$\mathbf{q}=(\theta_1, \theta_2 )$$를 만족하게 된다.

이 경우 두 좌표계 사이의 변환은 다음과 같다.

$$
\begin{align*}
(x_1, y_1) & = (L_1\sin \theta_1, L_1\cos \theta_1) \\
(x_2, y_2) & = (L_1\sin \theta_1 + L_2\sin \theta_2, L_1\cos \theta_1 + L_2\cos \theta_2)
\end{align*}
$$

## Principle of Least Action (Hamilton's Principle)
`뉴턴 역학`은 어떤 물체에 힘이 작용하고 그 결과 어떤 운동이 발생했다고 설명한다. 따라서 원인과 결과를 생각하는 `인과론`적인 역학이라고 할 수 있다.

반면, `라그랑주 역학`은 운동은 어떤 물리량을 최소로 유지하며 이루어진다는 원리를 따른다고 본다. 이 관점은 운동은 어떤 목적을 가지고 이루어지며 따라서 라그랑주 역학은 `목적론`적인 역학이라고 할 수 있다.

라그랑주 역학에서 최소로 유지되는 어떤 물리량을 Action이라고 정의한다. 즉, 이 Action을 최소로 유지하며 운동이 이루어진다는 원리를 Principle of Least Action, 또는 Hamilton's Principle라고 한다.

Action은 다음과 같이 정의한다.

$$
S(\mathbf{q}(t)) = \int_{t_1}^{t_2} L(\mathbf{q}(t), \mathbf{q}(t), t) dt
$$

여기서 $$L$$은 Principle of Least Action을 만족하는 어떤 물리량이며 $$(\mathbf{q}(t), \mathbf{q}(t), t)$$에 대한 Functional이다.

## Lagrangian의 특징


