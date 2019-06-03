---
layout: post
use_math: true
title: "[Statistics] Cross-Entropy의 통계적인 의미"
date: 2019-04-25 11:04:33
tagline: "머신 러닝 및 딥러닝에서 Loss Function으로 자주 사용되는 Cross-Entropy의 유도 과정 및 통계적인 의미 정리"
categories:
- Statistics
tags:
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-03-cross-entropy
---

## 문제 세팅
Cross-Entropy를 설명하기 위한 문제는 기본적으로 Binary Classification에서 Optimal Classifier를 어떻게 찾을 지에 대한 문제 세팅을 가지게 된다. Optimal Classifier에 대한 정의는 Logistic Regression에 대한 [포스팅](https://hcnoh.github.io/2019-04-25-logistic-regression-and-naive-bayes)에 정리가 되어있으니 참고하면 될 것 같다. 기본적으로 Binary라는 가정을 가지고 가지만 Multi Class에 대해서도 간단하게 확장이 가능하다.

어쨌든 우리는 여기서 Binary Classification을 위한 Logistic Regression 문제를 풀어나갈 것이다. 이전 [포스팅](https://hcnoh.github.io/2019-04-25-logistic-regression-and-naive-bayes)에서도 확인할 수 있듯이 Binary Classification을 위하여 다음과 같은 Bernoulli 분포를 가정한 모델을 사용하게 될 것이다.

$$
\begin{align*}
P(Y=y \vert X=\mathbf{x};\boldsymbol{\theta})
& = p_{\boldsymbol{\theta}}(\mathbf{x})^y(1−p_{\boldsymbol{\theta}}(\mathbf{x}))^{1−y} \\
p_{\boldsymbol{\theta}}(\mathbf{x})
& = P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})
\end{align*}
$$




