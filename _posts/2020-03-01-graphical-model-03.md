---
layout: post
use_math: true
title: "[Graphical Model] 3. Markov Random Field: Factorization"
date: 2020-03-01 21:30:00
tagline: "Markov Random Field에서 Factorization 및 Gibbs 분포에 대한 소개"
categories:
- Graphical Model Study
tags:
- graphical model
- statistics
- machine learning
- statistical mechanics
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-03-01-graphical-model-03
---

이전 포스트에서 대표적인 Undirected Graphical Model인 Markov Random Field (MRF)에 대해서 소개하였다. 이번에는 MRF를 통해서 Ensemble의 각 상태에 대한 확률 분포를 어떻게 모형화를 할 것인지에 대한 내용을 다뤄볼 것이다.

## Gibbs Distribution
이전 포스트에서 원자 3개의 격자 구조로 이루어진 시스템을 생각하였다. 이번에도 같은 시스템을 설명하는 Ising Model을 통해서 설명을 이어나가도록 하겠다.

통계 물리에서는 Canonical Ensemble이라는 개념을 활용하여 Boltzmann Distribution를 유도하게 된다. Boltzmann Distribution는 어떤 시스템의 Ensemble의 한 상태 $$x$$에 대해서 에너지 함수가 $$\epsilon(x)$$라고 정의되는 경우, 그 상태 $$X$$에 대한 확률 분포 P_X를 다음과 같다고 정의하게 된다:

$$
P_{X}(x) \propto \exp(-\beta \epsilon(x)).
$$

여기서 $$\beta$$는 상수이다. Boltzmann Distribution의 유도는 나중에 기회가 되면 따로 다뤄보도록 하겠다.

이러한 Boltzmann Distribution를 Ising Model에 적용하여 다음과 같은 $$X=\{X_i \}_{i=1}^3$$에 대한 Joint Distribution을 쓸 수 있다. 가중치는 1로 가정하였다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-58-56.png)

$$
\begin{array}{rl}
P_{X}(\mathbf{x})
& \propto \exp(-\beta \epsilon(\mathbf{x})) \\
& \propto \exp(-\beta (H_1(\mathbf{x}) + H_2(\mathbf{x}) + H_3(\mathbf{x}))) \\
& \propto \exp(-\beta \sum_{i<j}x_i x_j).
\end{array}
$$

여기서 위의 결과를 각각의 Clique에 대한 Factor로 새로 쓸 수 있을 것이다:

$$
P_{X}(\mathbf{x}) \propto \prod_{i=1}^3 \phi_i(\mathbf{x})
$$

$$
\begin{array}{rl}
\phi_1(\mathbf{x}) & = \exp(-\beta H_1(\mathbf{x})), \\
\phi_2(\mathbf{x}) & = \exp(-\beta H_2(\mathbf{x})), \\
\phi_3(\mathbf{x}) & = \exp(-\beta H_3(\mathbf{x})). \\
\end{array}
$$

이 경우 Factorization이 된 $$P_X$$를 Gibbs Distribution이라고 한다. 참고로 Clique는 Graph의 Fully-connected Subgraph로 정의된다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-11-02-11.png)

## Clique Factorization
위와 같은 방식의 Factorization 방식을 Clique Factorization라고 한다. 즉, Clique에 대한 Factor들의 곱으로 Joint Distribution을 매개변수화(Parameterization)하겠다는 의도이다.

Clique Factorization의 정의는 다음과 같다:
- 주어진 Random Variable들 $$X=\{X_1, \cdots, X_N \}$$이 Undirected Graph $$G$$에 대해서 MRF를 형성할때 $$X$$의 Joint Distribution $$P_X$$는 $$G$$의 Clique 집합 $$C_G$$와 그에 대한 Factor 집합 $$\Phi=\{\phi_c(\mathbf{x}_c) \}_{c\in C_G}$$을 통해서 $$P_X(\mathbf{x};\Phi)$$로 매개변수화할 수 있다:

$$
P_X(\mathbf{x};\Phi) \propto \prod_{c \in C_G} \phi_c (\mathbf{x}_c).
$$

- 여기서 $$\mathbf{x}_c$$는 Clique $$c$$에 대한 Configuration이다.

물론 모형이 주어져있는 상태라면 주어진 Factor를 이용하여 Joint Distribution을 분해하면 상관없지만, 모형을 추정하기를 원하던가 모형을 정확히 알 수 없는 상황이라면 Clique Factorization는 좋은 Factorization 방법이 될 수 있다.

모형을 추정하기 위해서 Clique Factorization를 수행하는 경우에는 Maximal Clique만을 사용할 것인지 주어진 모든 Clique를 다 사용할 것인지 잘 선택해야 할 것이다.

## References
- [Introduction to Statistical Relational Learning](https://mitpress.mit.edu/books/introduction-statistical-relational-learning)
- [Information, Physics, and Computation](https://web.stanford.edu/~montanar/RESEARCH/book.html)
- [Probabilistic Graphical Models](https://mitpress.mit.edu/books/probabilistic-graphical-models)