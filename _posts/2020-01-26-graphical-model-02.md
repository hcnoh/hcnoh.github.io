---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 2. Markov Random Field"
date: 2020-01-26 10:19:09
tagline: "마르코프 랜덤 필드에 대한 전반적인 소개 및 예시"
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

지난번에는 랜덤 변수들 사이의 의존성에 방향이 포함된 상황을 어떻게 형식적으로 다룰 것이냐에 대한 내용인 베이지안 네트워크에 대해서 다뤄보았다. 이번에는 방향이 없는 경우에는 어떻게 할 것이냐에 대한 내용을 다뤄보도록 하겠다.

## Introduction of Markov Random Field
베이지안 네트워크가 방향성 비순환 그래프 모형이었던 것과는 다르게 마르코프 랜덤 필드 (Markov Random Field, MRF)는 비방향성 그래프 모형 (Undirected Graphical Model) 구조에 기반을 둔 모델이다. 베이지안 네트워크와 마찬가지로 노드는 랜덤 변수를 나타내며 엣지는 그 랜덤 변수들 사이의 상호작용을 나타내게 된다. 다만 방향성이 없기 때문에 조건부 확률분포로 가정할 수는 없으며 중력, 전자기력같은 영향력을 상상하면 될 것 같다.

랜덤 변수 사이의 무슨 중력에 대해서 이야기하는지 도저히 이해가 안갈 수 있지만 간단한 예시를 통해서 확인해보도록 하자.

## An Example of MRF: Ising Model
이징 모형 (Ising Model)은 통계 물리에서 서로 상호작용하는 원자들로 구성된 시스템의 에너지를 다루기 위해서 등장한 모델이다.

각각의 원자들은 각각 자기 자신의 스핀(Spin)의 방향이 할당되어 있으며 이 값은 이진값으로 표현되게 된다. 또한 원자들은 격자 구조로 균일하게 분포되어 있다.

여기서는 원자 3개로 구성된 격자 구조를 생각해보도록 하자. 이 때 각각의 원자는 +1 또는 -1 값을 통해서 스핀 방향을 표현하게 된다. 3개의 원자들은 스핀 방향에 따라서 서로에게 영향을 주게 된다.

그렇다면 이러한 상황에서 이 시스템의 가장 안정적인 (그럴듯한, 있을법한) 스핀 방향의 할당은 어떻게 될까? 아래 그림을 살펴보자.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-26-01.png)

위의 그림은 가능한 스핀 방향 할당들의 앙상블(Ensenble)을 표현하고 있다. 위에서 붉은색으로 표시된 부분이 바로 가장 안정적인 할당들이라고 볼 수 있다.

그 이유를 설명하기 위해서는 이징 모형의 에너지 함수를 먼저 살펴봐야 할 것이다. 다음은 이징 모형의 에너지 함수이다:

$$
\eps_{i,j}(x_i, x_j) = w_{i,j}x_i x_j.
$$

여기서 웨이트 $$w_{i,j}$$는 대칭 함수이다:

$$
w_{i,j} = w_{j, i}
$$

이 격자 구조에서 웨이트 값들이 모두 동일하다고 가정한다면, 격자 구조 내에서의 모든 순서쌍에 대한 에너지의 총 합이 각각의 앙상블의 상태에 대한 총 에너지 값이 될 것이다. 또한 붉은색으로 표시된 상태들이 가장 작은 에너지를 갖게 된다는 것은 간단한 계산을 통해서 확인할 수 있을 것이다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-27-05.png)

## Formal Definition of MRF
위의 이징 모형의 예시로부터 우리는 마르코프 랜덤 필드의 몇 가지 특징을 확인해 볼 수 있을 것이다.

첫 번째 특징은 인접하지 않은 두 원자들의 스핀 방향은 다른 모든 원자들의 스핀 방향이 관찰된다면 서로 조건부 독립 관계를 갖는다는 점이다.

두 번째 특징은 원자 각각의 스핀 방향은 자기 주위에 인접한 원자들의 스핀 방향이 관찰된다면 그 외의 원자들의 스핀 방향들과 모두 조건부 독립 관계를 갖는다는 점이다.

세 번째 특징은 원자 묶음 2개에 대해서 그 묶음 2개를 완벽하게 분리하는 새로운 묶음의 원자들의 스핀 방향이 관찰된다면 각각의 원자 묶음 2개의 스핀 방향은 서로들간의 스핀 방향들과 조건부 독립 관계를 갖는다는 점이다.

첫 번째 특징은 페어와이즈 마르코프 성질 (Pairwise Markov Property)라고 한다. 두 번째 특징은 로컬 마르코프 성질 (Local Markov Property)라고 한다. 로컬 마르코프 성질은 이전 포스트인 베이지안 네트워크에 대한 포스트에서 등장했던 것을 기억할 것이다. 세 번째 특징은 글로벌 마르코프 성질 (Global Markov Property)이다.

일반적으로 글로벌 마르코프 성질을 만족한다면 이전 두 가지 마로코프 성질들도 함께 만족한다고 알려져 있다. 만족하지 않는 경우도 있으나 일반적이지 않은 상황이며 여기서 다룰 내용은 아니라고 생각되어 다루지 않도록 하겠다.

어쨌든 글로벌 마르코프 성질을 이용하여 마르코프 랜덤 필드를 다시 정의하면 다음과 같다:
- $$G=(V, E)$$를 비방향성 그래프라고 하자. 만약 주어진 랜덤 변수 집합 $$\{ X_i\}_{i\in V}$$이 아래의 조건이 만족한다면 이 랜덤 변수들은 그래프 $$G$$에 대해서 MRF를 형성한다:
    - 주어진 랜덤 변수들의 집합의 부분집합 $$X_A, X_B, X_S$$에 대해서 $$X_S$$가 나머지 두 부분집합을 연결하는 모든 경로를 다 지나가는 상황에서 다음의 조건을 만족:

    $$
    X_A \perp X_B | X_S
    $$

## Gibbs Distribution
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

먼저 Factor를 정의하자. Factor는 랜덤 변수들의 집합의 Value에 대한 함수로 정의된다:

$$
\phi: \text{Val}(X) \rightarrow \mathbb{R}^+.
$$

여기서 $$X=\{X_1, \cdots, X_N\}$$은 랜덤 변수들의 집합이다.

## References
- [Introduction to Statistical Relational Learning](https://mitpress.mit.edu/books/introduction-statistical-relational-learning)
- [Information, Physics, and Computation](https://web.stanford.edu/~montanar/RESEARCH/book.html)
- [Probabilistic Graphical Models](https://mitpress.mit.edu/books/probabilistic-graphical-models)