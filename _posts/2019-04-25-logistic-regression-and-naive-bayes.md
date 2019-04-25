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

$$
\begin{align*}
\widehat{\boldsymbol{\theta}} & = \arg \max_{\boldsymbol{\theta}} P(D \vert \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{\mathbf{x}, y \in D} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}, y \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

