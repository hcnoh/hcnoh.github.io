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

## 참고 자료
- [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)

## 수정 사항
- 2022.07.17
    - 최초 게제