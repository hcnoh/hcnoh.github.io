---
layout: post
use_math: true
title: "[Machine Learning] 1. Probability"
date: 2022-06-25 00:00:00
tagline: "머신 러닝의 기본 토대가 되는 확률 및 통계 개념들에 대해서 간단히 정리"
categories:
- Machine Learning Study
tags:
- probability
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-06-25-machine-learning-01
---

머신 러닝에 대해서 스터디를 진행하였고, 내용을 정리하여 연재를 진행하려고 한다. 주된 참고 자료로는 케빈 머피의 저서인 [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)을 활용하였다. 앞으로 꾸준히 스터디 자료를 업로드할 계획이다.

## 확률이란?
확률(Probability)는 두 가지 관점으로 해석할 수 있다. 먼저 첫 번째는 Frequentist Interpretation이다. Frequentist Interpretation은 확률이 장기적으로 사건들의 발생 빈도를 나타낸다는 관점이다. 예를 들면, 만약 우리가 동전을 매우 많이 던진다면 우린 그 동전이 약 절반의 빈도수를 가지며 앞면을 나타낼 것이라고 기대할 수 있다.

그 다음으로는 Bayesian Interpretation이다. Bayesian Interpretation에서는 확률이 무언가에 대한 우리의 불확실성을 정량화하는데 활용된다고 여기게 된다. 즉, 확률은 근본적으로 반복되는 시도와 관계있다기 보다는 사건에 정보와 관련되어 있다고 생각하는 관점이다. 예를 들면, 우리가 동전을 던지는 경우 다음 시도에서 앞면과 뒷면이 같은 가능성을 가진다고 믿을 수 있다. 이러한 믿음을 나타내는 것이 확률이라는 관점이 바로 Bayesian Interpretation이다.

## 확률론 간단 리뷰
- $$p(A)$$: 사건 $$A$$가 참일 확률
    - 예시: "내일 비가 올 것이다."와 같은 형식의 Logical Expression은 사건 $$A$$의 예시가 될 수 있다.
- $$p(\overline{A})$$: 사건 $$A$$가 거짓일 확률
    - $$p(\overline{A}) = 1 - p(A)$$
- 앞으로는 이진 사건 (Binary Event)의 형식으로 사건을 다룰 예정이며, $$A=1$$은 $$A$$가 참, $$A=0$$는 $$A$$가 거짓을 나타낸다.

## 이산 확률 변수

## 다양한 확률 연산

## Generative Classifier

## 독립

## 연속 확률 변수

## 평균, 분산, 공분산, 상관관계

## Change of Variables

## Monte Carlo Approximation

## 참고 자료
- [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)

## 수정 사항
- 2022.06.25
    - 최초 게제