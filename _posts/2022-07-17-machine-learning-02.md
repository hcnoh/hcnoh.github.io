---
layout: post
use_math: true
title: "[Machine Learning] 2. Generative Models for Discrete Data"
date: 2022-07-17 00:00:00
tagline: "Class-conditional Density를 활용하여 데이터의 생성 방식에 대해서 정의하고 이를 통해 Generative Classifier를 정의하는 생성 모델 방식에 대한 내용 정리"
categories:
- Machine Learning Study
tags:
- probability
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-07-17-machine-learning-02
---

이전 [포스트](https://hcnoh.github.io/2022-06-25-machine-learning-01)는 머신 러닝을 다루기 위한 기본적인 확률론 내용들을 다뤘다. 이번 포스트에서는 Class-conditional Density를 통해 데이터의 생성 방식을 고려하는 생성 모델에 대해서 이산 데이터를 바탕으로 정리하고 이를 통한 분류 문제 해결 방식에 대해서 정리한다.

## Concept Learning
`Concept Learning`이란 양성 표본(Positive Sample)과 음성 표본(Nagative Sample)으로부터 이진 분류를 수행을 학습하는 과정을 말한다. 심리학에서는 사람은 양성 표본만을 가지고도 Concept Learning을 수행할 수 있다고 한다. 하지만 주어진 양성 표본 하나만을 가지고 예측을 수행하는 것은 매우 애매모호하다. 따라서 우리는 양성 표본만을 가지고도 학습을 수행하는 방법들을 다뤄볼 것이다. 이를 위해서는 `Bayesian Concept Learning`이라는 개념을 도입해야 한다.

Concept Learning에서 풀고자 하는 문제를 정의해보자. 먼저 어떤 함수 $$f$$는 주어진 표본 $$x$$에 대해서 $$x$$가 개념 $$C$$의 표본이라면 $$f(x)=1$$, 그렇지 않다면 $$f(x)=0$$을 만족한다고 정의하자. Concept Learning의 목표는 이러한 함수 $$f$$를 학습하는 것이다. 이러한 문제는 `이진 분류`(Binary Classification) 문제라고도 한다.

이진 분류 문제를 해결하기 위해서는 일반적으로 양성 표본과 음성 표본 둘 다를 필요로 한다. 하지만 앞에서 언급했듯이 우리는 양성 표본만을 가지고도 이진 분류 문제를 접근할 수 있는 방법을 찾으려고 한다.

주어진 하나의 양성 표본만을 가지고 예측을 수행한다고 해보자. 이 경우 우리는 표본들 사이의 유사도를 측정하여 이 문제를 해결하고 싶을 것이다. 즉 양성 표본과 유사한 표본들에 대해서는 양성 표본이라고 결론지음으로서 이 문제를 접근하고자 할 것이다. 

이러한 유사도를 표현하는 방법으로는 확률 분포 $$p(\tilde{x} \vert \mathcal{D})$$, 즉 주어진 데이터 $$\mathcal{D}$$에 대한 새로운 표본 $$\tilde{x}$$가 $$\tilde{x}\in C$$를 만족하는 확률 분포가 있을 수 있다. 이는 추후에 이야기하겠지만 `Posterior Predictive Distribution`이라고 정의한다. 또한 유사도는 은닉 개념 $$C$$에 대한 추측을 통해서도 획득을 할 수 있을 것이다. 이는 `Hypothesis`에 대한 `귀납적 추론`이라고 한다.

그렇다면 우리는 어떻게 증거 $$\mathcal{D}$$를 동일하게 만족하는 수많은 Hypothesis들 중에서 어떤 것을 가장 좋은 Hypothesis로 선택할 수 있을까? 이를 위해서는 Bayesian 접근법이 필요하다.

## Bayesian Satatistics
먼저 `Bayesian Probability`란 확률을 어떤 현상에 대한 빈도수 대신 지식의 상태 혹은 개인의 믿음에 대한 크기를 나타내는 합리적인 기대값으로의 확률에 대한 해석이다. 확률에 대한 Bayesian Interpretation은 Hypothesis를 추리하는 명제 논리에 대한 확장으로 볼 수 있다.

`Bayesian Statistics`는 확률을 어떤 사건에 대한 `믿음의 정도`(Degree of Belief)로 표현하는 Bayesian Interpretation의 관점을 바탕으로 연구를 수행하는 통계학의 한 분야이다. 믿음의 정도는 아마 사건에 대한 `사전적인 지식`(Prior Knowledge)이 될 것이다. 예를 들면, 이전 실험들의 결과라던가 그 사건에 대한 개인적인 믿음 같은 것들이 이러한 사전적인 지식으로 들 수 있을 것이다.

Bayesian 통계 방법론들은 `Bayes 정리`를 새로운 데이터를 획득한 이후에 기존 확률값들을 계산하고 갱신하는데 적극적으로 활용한다. 이 경우에 Bayesian Statistics는 확률을 믿음의 정도로 다루기 때문에 Bayes 정리에서 사용될 매개변수 또는 매개변수 집합에 대한 믿음을 정량화하는 확률 분포에 해당하는 확률 변수를 직접 할당할 수 있는 것이다.

쉽게 다시 설명하면, 새로운 데이터 $$\mathcal{D}$$를 증거로 삼아서 매개변수 $$\theta$$에 대한 믿음의 정도를 갱신하고자 한다. 이는 아래와 같은 Bayes 정리를 통해서 수행될 수 있다:

$$
p(\theta \vert \mathcal{D}) = \frac{p(\mathcal{D} \vert \theta)p(\theta)}{p(\mathcal{D})}.
$$

여기서 확률 분포 $$p(\theta \vert \mathcal{D})$$ 및 $$p(\theta)$$를 할당하기 위해서는 빈도수로써의 확률 관점으로는 불가능하다. 대신 Bayesian 관점을 통해서 확률 분포 $$p(\theta \vert \mathcal{D})$$ 및 $$p(\theta)$$를 각각 믿음의 정도로 본다면 이 믿음의 정도를 할당하여 해당 계산을 수행할 수 있다.

## 참고 자료
- [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)

## 수정 사항
- 2022.07.17
    - 최초 게제