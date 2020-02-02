---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 2. Markov Random Field"
date: 2020-01-26 10:19:09
tagline: "Markov Random Field에 대한 전반적인 소개 및 예시"
categories:
- Graphical Model
tags:
- graphical model
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-01-26-graphical-model-02
---

## Markov Random Field
지난번에는 확률변수들 사이의 의존성에 방향이 포함된 상황을 어떻게 형식적으로 다룰 것이냐에 대한 내용인 Bayesian Network에 대해서 다뤄보았다. 이번에는 방향이 없는 경우에는 어떻게 할 것이냐에 대한 내용을 다뤄보도록 하겠다.

Bayesian Network가 DAC였던 것과는 다르게 Markov Random Field는 Undirected Graphical Model 구조에 기반을 둔 모델이다. Bayesian Network와 마찬가지로 Node는 확률변수를 나타내며 Edge는 그 확률변수들 사이의 상호작용을 나타내게 된다. 다만 방향성이 없기 때문에 조건부 확률분포로 가정할 수는 없으며 중력, 전자기력같은 영향력을 상상하면 될 것 같다.

확률변수 사이의 무슨 중력에 대해서 이야기하는지 도저히 이해가 안갈 수 있지만 간단한 예시를 통해서 확인해보도록 하자.

## Markov Random Field 예시: 전하 모델
전하 3개가 균일하게 분포되어 있다고 가정하자. 이 때 각각의 전하는 +1 또는 -1 전하만을 가질 수 있다. 3개의 전하는 당연하게도 서로에게 영향을 끼칠 것이다.

그렇다면 이러한 상황에서 이 시스템의 가장 안정적인 (그럴듯한, 있을법한) 전하 배치는 어떻게 될까? 아래 그림을 살펴보자.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-26-01.png)

위의 그림에서 붉은색으로 표시된 부분이 바로 가장 안정적인 전하 배치라고 볼 수 있을 것이다. 그 이유는 Potential 에너지를 고려하면 알 수 있다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-27-05.png)

즉, Potential 에너지가 가장 작은 전하 배치가 가장 안정적인 전하 배치가 될 것이다.

## Gibbs Measure
여기서 시스템의 안정성을 확률분포의 개념과 연결짓기 위해서 Gibbs Measure라는 개념을 도입하게 된다. Gibbs Measure는 어떤 시스템 $$X$$가 $$x$$라는 상태를 가지게 될 확률을 아래와 같이 정의한다:

$$
P(X = x) = \frac{1}{Z} \exp(-\beta E(X)).
$$

여기서 $$Z$$는 Normalization 상수이며, $$\beta$$ 역시 상수이다. 통계물리에서는 Canonical Ensemble이라는 개념을 이용하여 Gibbs Measure를 유도하는데 나중에 기회가 되면 다뤄보도록 하고 여기서는 그냥 넘어가도록 하겠다.

이러한 Gibbs Measure를 전하 모델에 적용하여 쓰면 다음과 같은 결합분포를 쓸 수 있다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-58-56.png)

$$
\begin{array}{rl}
P(X=x)
& \approx \exp(-\beta E(x)) \\
& = \exp(-\beta (H_1(x) + H_2(x) + H_3(x))) \\
& = \exp(-\beta (H_1(x))) \cdot \exp(-\beta (H_2(x))) \cdot \exp(-\beta (H_3(x))) \\
& = \phi_1(x) \cdot \phi_2(x) \cdot \phi_3(x)
\end{array}
$$

즉, 하나의 Fully-connected Graph로 이루어진 시스템에 대한 확률분포는 각각의 Fully-connected Subgraph에 대한 분포들의 곱으로 쓸 수 있다. 이러한 Fully-connected Subgraph를 Clique라고 부른다.

## Clique Factorization
위와 같은 방식의 Factorization 방식을 Clique Factorization이라고 한다. 즉, Clique에 대한 Factor들의 곱으로 결합분포를 Parameterization하겠다는 의도이다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-11-02-11.png)

Clique Factorization의 정의는 다음과 같다:
- 주어진 Markov Random Field $$H$$에 대하여 찾을 수 있는 모든 Clique들이 $$\{ \mathbf{C}_1, \cdots, \mathbf{C}_k\}$$일 때, $$H$$에 대한 결합분포 $$P$$는 다음과 같이 Parameterization될 수 있다:

$$
P(X_1, \cdots, X_n) = \prod_{i=1}^k \phi_i (\mathbf{C}_i)
$$

이 정의를 바탕으로 전하 모델을 Factorization하여 써보면 다음과 같다:

$$
\begin{array}{rl}
P(X_1=x_1, X_2=x_2, X_3=x_3)
& \approx \exp(-\beta H_1(x_1, x_2)) \cdot \exp(-\beta H_1(x_2, x_3)) \cdot \exp(-\beta H_1(x_3, x_1)) \\
& = \phi_1(x_1, x_2) \cdot \phi_2(x_2, x_3) \cdot \phi_3(x_3, x_1)
\end{array}
$$

물론 모델이 주어져있는 상태라면 주어진 Factor를 이용하여 Factorization을 하면 상관없지만, 모델을 추정하기를 원하던가 모델을 정확히 알 수 없는 상황이라면 Clique Factorization은 좋은 Factorization 방법이 될 수 있다.

모델을 추정하기 위해서 Clique Factorization을 수행하는 경우에는 Maximal Clique만을 사용할 것인지 주어진 모든 Clique를 다 사용할 것인지 잘 선택해야 할 것이다.

## Factor Graph
위에서 Factor 및 Factorization에 대한 이야기를 잠깐 하였다. 그렇다면 Factorization은 정확히 어떤 것일까?

먼저 Factor를 정의하자. Factor는 확률변수들의 집합의 Value에 대한 함수로 정의된다:

$$
\phi: \text{Val}(X) \rightarrow \mathbb{R}^+.
$$

여기서 $$X=\{X_1, \cdots, X_N\}$$은 확률변수들의 집합이다.
