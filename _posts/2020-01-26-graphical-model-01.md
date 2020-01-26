---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 1. Graphical Model 소개 및 Bayesian Network"
date: 2020-01-26 08:12:09
tagline: "Graph 및 Graphical Model에 대한 전반적인 소개와 Bayesian Network에 대한 소개"
categories:
- Graphical Model
tags:
- graphical model
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-01-26-graphical-model-01
---

## Graph
Graph란 Node와 Edge로 이루어진 구조를 말한다. 일반적으로 Graph를 정의하기 위해서는 Node Set $$V$$와 Edge Set $$E$$가 먼저 정의되어야 한다. Edge는 하나의 Node 쌍에 대해서 정의가 되게 되며 Node 두 개를 연결하는 개념으로 생각할 수 있다.

어쩄든 Graph $$G$$는 $$G=(V, E)$$의 순서쌍으로 정의가 된다.

특히, Edge에 방향이 있는 경우를 Directed Graph라고 부르게 된다.

그렇다면 Graph는 왜 사용하는 것일까? 보통 Graph를 사용하는 경우는 여러 요소들 사이의 상관관계를 시각화하고 그 관계를 형식적으로 다루고자하는 경우에 사용하게 된다.

![](/assets/img/2020-01-26-graphical-model-01/2020-01-26-graphical-model-01_2020-01-26-08-23-19.png)
(그림 출처: [https://ratsgo.github.io/data%20structure&algorithm/2017/11/18/graph/](https://ratsgo.github.io/data%20structure&algorithm/2017/11/18/graph/))

## Graphical Model
Graphical Model은 Probabilistic Graphical Model, 또는 Structured Probabilistic Model이라고도 불린다. 보통 확률변수들 사이의 조건부 의존 구조를 표현하기 위해서 많이 사용되게 된다. 대표적인 예시로는 Bayesian Network가 있는데 Bayesian Network에서 Node는 확률변수, Edge는 그 확률변수 사이의 조건부 확률로 정의가 되게 된다.

크게 두 종류로 나뉘게 되는데 먼저 방금 언급했던 Bayesian Network가 있고 또 다른 하나는 Markov Random Field가 있다.

## Bayesian Network
Bayesian Network는 기본적으로 Directed Acyclic Graph (DAG) 구조를 가지게 된다. Node는 확률변수를 나타내며, Edge는 두 확률변수 사이의 방향성을 지닌 영향력, 즉 조건부 확률분포를 나타내게 된다. 방향성이 있는 이유는 조건부 확률분포가 방향성을 지니기 때문에 그러한 것이다.

![](/assets/img/2020-01-26-graphical-model-01/2020-01-26-graphical-model-01_2020-01-26-08-38-02.png)

## Bayesian Network 예시: 지각 모델
다음과 같은 예시를 생각해보자. 나는 지각을 너무 자주한다. 어느날 내가 더이상 지각을 하지 않기를 바라는 마음에서 친구가 나에게 내기를 제안한다. 내가 다음날 또 지각을 한다면 친구의 승, 지각을 하지 않는다면 내가 승리하는 내기이다. 나는 이 내기를 참여할지 말지를 내가 지각할 확률에 기반하여 결정을 하고싶다. 따라서 나는 자신이 지각하는 원인들을 파악하고 다음날 지각할 확률을 Bayesian 관점에서 구해보려고 시도한다.

내가 고민해본 결과, 나의 지각 원인은 다음과 같은 총 세 가지 변수를 들 수 있다고 결론을 내렸다:
- 알람이 제대로 울렸는가? Yes/No: 확률변수 $$X_1$$
- 늦잠을 잤는가? Yes/No: 확률변수 $$X_2$$
- 1호선이 제때 왔는가? Yes/No: 확률변수 $$X_3$$

우리는 이 세 가지 확률변수를 바탕으로 네 번째 확률변수 $$X_4$$ (지각을 하였는가? Yes/No)에 대한 주변분포를 추론하고자 한다.

$$
P(X_4) = \sum_{X_1, X_2, X_3} P(X_1, X_2, X_3, X_4)
$$

이러한 주변화 계산을 위해서는 결합분포 $$P(X_1, X_2, X_3, X_4)$$에 대한 추론이 선행되어야 할 것이다. 이를 위해서는 각각의 확률변수에 대한 가정이 필요하다. Bayesian 관점에서 나의 가정들은 아래와 같다:
- 알람과 1호선은 서로 독립적인 사건:
    - 알람이 제대로 울릴 것인가에 대한 나의 믿음: 0.9
    - 1호선이 제때 올 것인가에 대한 나의 믿음: 0.2
- 늦잠은 알람에 의존적인 사건:
    - 늦잠을 잘 것인가에 대한 나의 믿음:
        - 알람이 제대로 울리면 0.1
        - 알람이 제대로 울리지 않으면 0.7
- 지각은 늦잠과 1호선에 의존적인 사건:
    - 늦잠을 안자고 1호선이 제때 오면 0.2
    - 늦잠을 안자고 1호선이 제떄 안오면 0.7
    - 늦잠을 자고 1호선이 제때 오면 0.8
    - 늦잠을 자고 1호선이 제때 안오면 0.9

이러한 가정들을 바탕으로 다음과 같은 Bayesian Network를 그릴 수 있다.

![](/assets/img/2020-01-26-graphical-model-01/2020-01-26-graphical-model-01_2020-01-26-09-12-52.png)

또한 가정을 바탕으로 Edge 값인 조건부 확률분포들을 아래와 같이 정의할 수 있다.

$$
P(X_3) = \left\{ \begin{array}{ll} 0.2 & \text{if} \ X_3=1, \\ 0.8 & \text{otherwise}. \end{array} \right.
$$

$$
P(X_2|X_1) = \left\{ \begin{array}{ll} 0.3 & \text{if} \ X_1=0, X_2=0, \\ 0.7 & \text{if} \ X_1=0, X_2=1, \\ 0.9 & \text{if} \ X_1=1, X_2=0, \\ 0.1 & \text{if} \ X_1=1, X_2=1. \end{array} \right.
$$

$$
P(X_3) = \left\{ \begin{array}{ll} 0.2 & \text{if} \ X_3=1, \\ 0.8 & \text{otherwise}. \end{array} \right.
$$

$$
P(X_4|X_2,X_3) = \left\{ \begin{array}{ll}
0.1 & \text{if} \ X_2=0, X_3=0, X_4=0, \\  0.1 & \text{if} \ X_2=0, X_3=0, X_4=1, \\  0.1 & \text{if} \ X_2=0, X_3=1, X_4=0, \\ 0.1 & \text{if} \ X_2=0, X_3=1, X_4=1, \\  0.1 & \text{if} \ X_2=1, X_3=0, X_4=0, \\  0.1 & \text{if} \ X_2=1, X_3=0, X_4=1, \\  0.1 & \text{if} \ X_2=1, X_3=1, X_4=0, \\ 0.1 & \text{if} \ X_2=1, X_3=1, X_4=1. \\  \end{array} \right.
$$

이제 Bayes 규칙을 이용하여 결합분포를 추론해보자.

$$
\begin{array}{ll}
P(X_1, X_2, X_3, X_4)
& = P(X_4|X_1, X_2, X_3)\cdot P(X_1, X_2, X_3) \\
& = P(X_4|X_1, X_2, X_3)\cdot P(X_3 | X_1, X_2) \cdot P(X_1, X_2) \\
& = P(X_4|X_1, X_2, X_3)\cdot P(X_3 | X_1, X_2) \cdot P(X_2 | X_1) \cdot P(X_1) \\
& = P(X_4 | X_2, X_3) \cdot P(X_3) \cdot P(X_2 | X_1) \cdot P(X_1)
\end{array}
$$

특히 여기서 마지막 두 줄은 Bayesian Network를 다룰때 활용되는 중요한 가정인 Local Markov Assumption을 사용하는 부분이다. 이것은 잠시 후에 다뤄볼 것이다.

$$
P(X_4|X_1, X_2, X_3)\cdot P(X_3 | X_1, X_2) \cdot P(X_2 | X_1) \cdot P(X_1) = P(X_4 | X_2, X_3) \cdot P(X_3) \cdot P(X_2 | X_1) \cdot P(X_1)
$$

어쨌든 최종적으로 구한 결합분포는 아래와 같다.

$$
P(X_1, X_2, X_3, X_4) = P(X_4 | X_2, X_3) \cdot P(X_3) \cdot P(X_2 | X_1) \cdot P(X_1)
$$

이런 방식으로 결합분포를 각 확률변수에 대한 조건부분포들에 대한 곱으로 써놓는 것을 Factorization이라고 정의한다. 이것을 Bayesian Network의 관점에서 다시 정의하면 다음과 같다:

$$
P(X_1, \cdots, X_n) = \prod_{i=1}^n P(X_i | \mathbf{Pa}(X_i))
$$
