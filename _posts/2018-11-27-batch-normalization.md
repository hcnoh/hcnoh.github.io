---
layout: post
use_math: true
title: "Batch Normalization 개념 정리"
date: 2018-11-27 19:54:59
tagline: "딥러닝에서 자주 쓰이는 Regularization 방법인 Batch Normalization에 대한 논문을 읽고 개념을 정리"
categories:
- Deep Learning
tags:
- deep learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-27-batch-normalization
---

이번 포스팅은 다음의 논문을 스터디하여 정리하였다:
- [링크1](https://arxiv.org/abs/1502.03167)

## Introduction
딥러닝을 학습하는 알고리즘으로 가장 일반적으로 많이 쓰이는 방법은 바로 Stochastic gradient descent (SGD)이다. SGD를 간단하게 설명하면 파라미터 $$\theta$$에 대해서 정의된 특정 cost $$L(\theta)$$를 최적화하기 위하여 $$L(\theta)$$의 Gradient를 이용하는 방법이다.

$$ \theta_{k+1} = \theta_k - \alpha \nabla_{\theta} L(\theta) |_{\theta_k} $$

수식 삽입 테스트: $$ x_t = 3 $$ ??
