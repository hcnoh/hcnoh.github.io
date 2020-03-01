---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 4. Markov Random Field: Factor Graph"
date: 2020-03-01 22:15:00
tagline: "마르코프 랜덤 필드를 다루기 위한 인수 그래프 및 베이지안 네트워크에서의 적용 방법 소개"
categories:
- Graphical Model
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

## References
- [Introduction to Statistical Relational Learning](https://mitpress.mit.edu/books/introduction-statistical-relational-learning)
- [Information, Physics, and Computation](https://web.stanford.edu/~montanar/RESEARCH/book.html)
- [Probabilistic Graphical Models](https://mitpress.mit.edu/books/probabilistic-graphical-models)