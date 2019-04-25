---
layout: post
use_math: true
title: "[Machine Learning] Logistic Regression 및 Naive Bayes 정리"
date: 2019-04-25 11:04:33
tagline: "내 Ubuntu PC를 SSH를 이용하여 서버화하여 망 내의 다른 로컬에서 접속할 수 있게끔 서버 구축 방법 정리"
categories:
- Machine Learning
tags:
- machine learning
- statistics
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-04-25-logistic-regression-and-naive-bayes
---

## Optimal Classifier
Optimal Classifier의 정의: $$f^*(x) = \arg \max_{Y=y}P(Y=y \vert X=x)$$

Optimal Classifier를 찾기 위한 모델링 방법 2가지:
- Discriminative Model: $$P(Y\vert X)$$를 직접 모델링하여 추정
- Generative Model: $$P(Y\vert X) = \frac{P(X\vert Y)P(Y)}{P(X)}$$로 분해해서 각각의 확률을 추정하여 $$P(Y\vert X)$$를 추정
- 둘 사이는 MLE와 MAP와 같음… 이건 나중에 천천히 얘기해보는 것으로

강의에서는 Discriminative Model의 대표적인 예시로 Logistic Regression을, Generative Model의 대표적인 예시로 Naive Bayes를 다루게 됨

## Logistic Regression
Logistic Regression이란?
- 대표적인 Logistic Function 예시: Sigmoid, Tanh, 등등

$$P(Y\vert X)$$를 Logistic Function을 이용하여 모델링을 하고 그것을 Fitting하는 것을 Logistic Regression이라고 함
여기서는 Sigmoid를 사용하여 예시를 들었음: $$Sigmoid(x) = \frac{1}{1+e^{−x}}$$

Binary Classifier의 예시:
- y는 0또는 1, x는 벡터
- 모델링(Bernoulli Distribution):
$$P(Y=y\vert X=\mathbf{x}) = \mu(\mathbf{x})^y(1−\mu(\mathbf{x}))^{1−y}$$
$$\mu(\mathbf{x}) = \frac{1}{1+e^{−\boldsymbol{\theta}^{\text{T}}\mathbf{x}}}$$
- 파라미터 쎄타는 Maximum Likelihood Estimation으로 찾아내게 됨!
$$
\begin{align*}
\widehat{\boldsymbol{\theta}} & = \arg \max_{\boldsymbol{\theta}} P(D \vert \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{\mathbf{x}, y \in D} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}, y \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

