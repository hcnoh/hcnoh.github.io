---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 4. Markov Random Field: Factor Graph"
date: 2020-03-01 22:15:00
tagline: "마르코프 랜덤 필드를 다루기 위한 인수 그래프 및 베이지안 네트워크에서의 적용 방법 소개"
categories:
- Machine Learning
tags:
- graphical model
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-03-01-graphical-model-04
---

이전 포스트에서 클리크 분해를 통해서 깁스 분포를 통해 마르코프 랜덤 필드의 모형화 과정을 간단하게 소개했다. 이번 포스트에서는 인수 그래프를 통해서 마르코프 랜덤 필드를 표현하는 방식과 베이지안 네트워크에서의 적용을 통해서의 활용 예시를 들어보도록 하겠다.

## Factor Graph
먼저 인수를 명확하게 정의하자. 인수는 앙상블을 이루고있는 시스템의 각각의 상태값에 대한 함수로 정의된다:

$$
\phi: \text{Val}(X) \rightarrow \mathbb{R}^+.
$$

여기서 $$X=\{X_1, \cdots, X_N\}$$은 랜덤 변수들의 집합이다.

그렇다면 인수 그래프 (Factor Graph)는 무엇일까? 만약 주어진 랜덤 변수 집합 $$X=\{X_1, \cdots, X_N\}$$가 MRF를 형성한다면 그에 해당하는 비방향성 그래프 $$(V, E)$$가 존재할 것이다. 만약 주어진 인수 집합 $$\Phi=\{\phi_a\}_{a\in F}$$에 대해서 이분 그래프 (Bipartite Graph) $$G=(V, F, E)$$를 그린다면 그 그래프를 우리는 인수 그래프라고 부른다.

여기서 인수 그래프 $$G$$는 $$V$$와 $$F$$의 원소들이 각각 서로 연관이 있는 경우에만 엣지로 연결되게 된다.

조금 더 이해를 돕기 위해서 이전 포스트에서 등장했던 지각 모형을 이용하여 예시를 들어보도록 하자.

## Factor Graph: Example
일단 지각 모형은 베이지안 네트워크를 설명하기 위한 모형이었지만, 사실 베이지안 네트워크도 마르코프 랜덤 필드의 한 종류라고 봐도 될 것이다.

먼저 각 노드들은 그대로 노드로 둔 상태에서 인수가 어떻게 정의될 수 있는지 살펴봐야 할 것이다. 베이지안 네트워크에서 인수는 다음과 같다.

$$
\begin{array}{rl}
\phi_1(x_1) & = P_{X_1}(x_1) \\
\phi_2(x_1, x_2) & = P_{X_2|X_1}(x_2|x_1) \\
\phi_3(x_3) & = P_{X_3}(x_3) \\
\phi_4(x_2, x_3, x_4) & = P_{X_4|X_2, X_3}(x_4|x_2, x_3)
\end{array}
$$

이를 바탕으로 인수 그래프를 그리면 아래와 같을 것이다.

![](/assets/img/2020-03-01-graphical-model-04/2020-03-01-graphical-model-04_2020-03-06-20-49-07.png)

## References
- [Introduction to Statistical Relational Learning](https://mitpress.mit.edu/books/introduction-statistical-relational-learning)
- [Information, Physics, and Computation](https://web.stanford.edu/~montanar/RESEARCH/book.html)
- [Probabilistic Graphical Models](https://mitpress.mit.edu/books/probabilistic-graphical-models)