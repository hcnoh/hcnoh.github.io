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

여기서 각각의 변수는 아래와 같다:
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

$$t_1$$에서 $$t_2$$까지의 Action은 다음과 같이 정의한다.

$$
S[\mathbf{q}(t)] = \int_{t_1}^{t_2} L(\mathbf{q}(t), \dot{\mathbf{q}}(t), t) dt
$$

여기서 $$L$$은 Principle of Least Action을 만족하는 어떤 물리량이며 $$(\mathbf{q}(t), \dot{\mathbf{q}}(t), t)$$에 대한 Functional이다. 이제 Principle of Least Action을 만족하기 위해서는 이 물리량 $$L$$이 어떤 성질을 만족해야만 하는지 확인해야 할 것이다.

Principle of Least Action을 따른다는 것은 Action $$S[\mathbf{q}(t)]$$가 $$\mathbf{q}(t)$$에서 극값을 갖는다는 의미이다. 즉, 아주 작은 Spatial Variation ($$\mathbf{q} \rightarrow \mathbf{q} + \epsilon \mathbf{u}$$)에 대해서 그 변화량이 없다는 의미가 될 것이다.

$$
S[\mathbf{q}(t)+\delta \mathbf{q}(t)] - S[\mathbf{q}(t)] = 0
$$

여기서 우리는 $$\mathbf{q}(t)$$의 변화량 $$\delta \mathbf{q}(t)$$를 임의의 벡터 $$\mathbf{u}(t)$$와 매우 작은 상수 $$\epsilon$$을 이용하여 다음과 같이 쓸 수 있다.

$$
\delta \mathbf{q}(t) = \epsilon \mathbf{u}(t)
$$

$$\mathbf{u}(t)$$를 임의의 벡터라고 가정했지만 몇 가지 추가적인 조건이 붙게 된다. 우리가 여기서 기술하고 싶은 운동은 시간 $$t_1$$과 $$t_2$$사이의 운동 경로 변화이며 따라서 시간 $$t_1$$과 $$t_2$$에서의 $$\mathbf{q}$$값은 변화에 의한 변화가 일어나지 않기를 원한다. 즉, 다음과 같은 Boundary Condition이 추가적으로 붙게 될 것이다.

$$
\mathbf{u}(t_1) = \mathbf{u}(t_2) = \mathbf{0}
$$

이제 이 가정들을 바탕으로 $$S[\mathbf{q}(t)+\delta \mathbf{q}(t)]$$를 구해보도록 하자. $$S[\mathbf{q}(t)+\delta \mathbf{q}(t)]$$는 Taylor Expansion을 통해서 다음과 같이 쓸 수 있다.

$$
\begin{align*}
S[\mathbf{q}(t)+\delta \mathbf{q}(t)]
& = S[\mathbf{q}(t) + \epsilon \mathbf{u}(t)] \\
& = \int_{t_1}^{t_2} L(\mathbf{q}(t) + \epsilon \mathbf{u}(t), \dot{\mathbf{q}}(t) + \epsilon \dot{\mathbf{u}}(t), t) dt \\
& = \int_{t_1}^{t_2} L(\mathbf{q}(t), \dot{\mathbf{q}}(t), t) + \epsilon \frac{\partial L}{\partial \mathbf{q}} \cdot \mathbf{u}(t) + \epsilon \frac{\partial L}{\partial \dot{\mathbf{q}}} \cdot \dot{\mathbf{u}}(t) + O(\epsilon^2) dt \\
& \approx \int_{t_1}^{t_2} L(\mathbf{q}(t), \dot{\mathbf{q}}(t), t) + \epsilon \frac{\partial L}{\partial \mathbf{q}} \cdot \mathbf{u}(t) + \epsilon \frac{\partial L}{\partial \dot{\mathbf{q}}} \cdot \dot{\mathbf{u}}(t) dt
\end{align*}
$$

마지막 등식은 $$\epsilon$$이 매우 작기때문에 1차항만을 남기고 모두 소거하여 Approximation하는 과정을 나타낸다. 따라서 $$S[\mathbf{q}(t)+\delta \mathbf{q}(t)] - S[\mathbf{q}(t)]$$는 다음과 같다.

$$
\begin{align*}
S[\mathbf{q}(t)+\delta \mathbf{q}(t)] - S[\mathbf{q}(t)]
& \approx \epsilon \int_{t_1}^{t_2} \frac{\partial L}{\partial \mathbf{q}} \cdot \mathbf{u}(t) + \frac{\partial L}{\partial \dot{\mathbf{q}}} \cdot \dot{\mathbf{u}}(t) dt \\
& = \epsilon \int_{t_1}^{t_2} \frac{\partial L}{\partial \mathbf{q}} \cdot \mathbf{u}(t) - \left( \frac{d}{dt} \frac{\partial L}{\partial \dot{\mathbf{q}}} \right) \cdot \mathbf{u}(t) dt + \epsilon \left[ \frac{\partial L}{\partial \dot{\mathbf{q}}} \cdot \mathbf{u}(t) \right]_{t=t_1}^{t=t_2} \\
& = \epsilon \int_{t_1}^{t_2} \frac{\partial L}{\partial \mathbf{q}} \cdot \mathbf{u}(t) - \left( \frac{d}{dt} \frac{\partial L}{\partial \dot{\mathbf{q}}} \right) \cdot \mathbf{u}(t) dt \\
& = \epsilon \int_{t_1}^{t_2} \left[ \frac{\partial L}{\partial \mathbf{q}} - \left( \frac{d}{dt} \frac{\partial L}{\partial \dot{\mathbf{q}}} \right) \right] \cdot \mathbf{u}(t) dt \\
& = 0
\end{align*}
$$

두 번째 등식은 Partial Integral을 수행한 것이고 세 번째 등식은 Boundary Condition $$\mathbf{u}(t_1)=\mathbf{u}(t_2)=\mathbf{0}$$으로부터 나온 것이다. 네 번째 등식 및 $$\mathbf{u}(t)$$가 임의의 벡터라는 조건에 의하여 다음과 같은 결과를 도출할 수 있다.

$$
\frac{\partial L}{\partial \mathbf{q}} - \left( \frac{d}{dt} \frac{\partial L}{\partial \dot{\mathbf{q}}} \right) = 0
$$

이 결과가 바로 Euler-Lagrange Equation이다. 이 Euler-Lagrange Equation을 만족하는 물리량은 Principle of Least Action을 따르는 물리량이며 Lagrangian이라고 정의한다. 이러한 Lagrangian의 대표적인 예로 Kinetic Energy $$T$$와 Potential Energy $$V$$의 차인 $$L=T-V$$가 있다.

추가적으로 라그랑주 역학에서 Euler-Lagrange Equation은 뉴턴 역학에서의 Equation of Motion과 동치이다.

## Lagrangian의 특징
Lagrangian은 위에서 예로 들었던 $$L=T-V$$ 뿐 아니라 다양한 값이 될 수 있다. 하지만 이러한 다양한 Lagrangian들 사이에는 어떤 중요한 관계가 존재한다. 그 관계는 아래와 같다.

$$
\begin{align*}
L_1(\mathbf{q}, \dot{\mathbf{q}}, t)
& = L_2(\mathbf{q}, \dot{\mathbf{q}}, t) + \frac{d}{dt}f(\mathbf{q}, \dot{\mathbf{q}}, t)
\end{align*}
$$

즉, 어떤 특정 Lagrangian $$L_1$$에 대하여 위의 관계를 만족하는 Functional $$f(\mathbf{q}, \dot{\mathbf{q}}, t)$$가 존재한다면, Functional $$L_2$$는 Lagrangian $$L_1$$와 마찬가지로 현재의 Point $$(\mathbf{q}, \dot{\mathbf{q}}, t)$$에서 최소의 Action을 갖게 되며 따라서 물리량 $$L_2$$ 역시 Lagrangian이다.

증명은 다음과 같다.

$$
\begin{align*}
S_1 = \int_{t_1}^{t_2}L_1 dt
& = \int_{t_1}^{t_2}L_2 + \frac{d}{dt}f(\mathbf{q}, \dot{\mathbf{q}}, t) dt \\
& = S_2 + \left[ f(\mathbf{q}, \dot{\mathbf{q}}, t) \right]_{t_1}^{t_2} \\
& = S_2 + f(\mathbf{q}(t_2), \dot{\mathbf{q}}(t_2), t_2) - f(\mathbf{q}(t_1), \dot{\mathbf{q}}(t_1), t_1)
\end{align*}
$$

여기서 $$f(\mathbf{q}(t_2), \dot{\mathbf{q}}(t_2), t_2) - f(\mathbf{q}(t_1), \dot{\mathbf{q}}(t_1), t_1)$$은 Action의 Spatial Variation ($$\mathbf{q} \rightarrow \mathbf{q} + \epsilon \mathbf{u}$$)에 영향을 주지 않게 되어 Euler-Lagrange Equation을 유도하는 과정에서 상수로 취급되어 사라지게 된다. 즉 $$L_1$$과 $$L_2$$는 Point $$(\mathbf{q}, \dot{\mathbf{q}}, t)$$에서 최소의 Action을 갖게 된다.

