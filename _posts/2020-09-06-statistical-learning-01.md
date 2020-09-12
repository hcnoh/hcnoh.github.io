---
layout: post
use_math: true
title: "[Statistical Learning] 1. Statistical Learning 소개"
date: 2020-09-06 11:30:00
tagline: "기계 학습의 기본적인 형식 중 하나인 Statistical Learning에 대한 문제 정의와 그 특징에 대한 소개"
categories:
- Machine Learning Study
tags:
- statistical learning
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-09-06-statistical-learning-01
---

우리가 기계 학습을 통해서 얻고자 하는 것은 결국 이 세계를 구축하는 시스템을 묘사하고있는 Model을 획득하는 것이라고 말할 수 있을 것이다. 이러한 Model을 획득하는 방법은 크게 두 가지가 있다고 볼 수 있다.

첫 번째 방법은 우리의 지식을 통해서 직접 Model을 설계하는 방법이 있다. 이 방법은 우리가 획득하고자 하는 Model의 Domain 지식을 활용하여 Model을 설계하고 검증하는 과정이 포함될 것이다. 특히 어떤 Domain에서는 이 과정을 수행하는데 있어서 필요한 지식이 정말 많이 필요하게 될 것이고, 또는 그러한 지식을 가지고있는 전문가의 몸값이 너무 큰 문제로 인하여 비용이 너무 많이 들 수도 있을 것이다. 물론 최악에는 그러한 지식을 가진 전문가가 존재하지 않을 수도 있다.

두 번째 방법은 기존에 축적된 데이터를 활용하여 Model을 구축하는 것이다. 특히 최근에는 기술의 발전으로 인하여 수많은 종류의 데이터에 접근할 수 있게 되었고, 이러한 거대한 데이터는 특히 많은 변수를 지닌 거대한 Model을 추정하기에도 충분할 정도가 되었다.

최근의 기계 학습은 이러한 거대한 데이터를 바탕으로 Model을 획득하는 방법으로 활용이 이루어지고 있다.

## Data
이번 포스트를 진행하는데에 있어서 우리는 데이터의 중요성을 이해하고 그 특징을 정리하고 넘어갈 필요가 있다. 먼저 우리에게 시스템이 주어졌을 때, 시스템의 변수 집합 $$X=\{ X_1, \cdots, X_N \}$$에 대해서 생각할 수 있을 것이다. 데이터는 이러한 시스템의 변수 집합 $$X$$의 모든 가능태들인 Data Instance $$\mathbf{x}^{(j)}$$들의 뭉치로 이해할 수 있을 것이다:

$$
\mathcal{D} = \{ \mathbf{x}^{(1)}, \cdots, \mathbf{x}^{(M)} \}.
$$

대부분의 기계 학습의 논의에서는 이러한 Data Instance들이 i.i.d.(Independent and Identically Distributed)로 생성되었다는 가정을 가지고 진행되게 된다:

$$
\mathbf{x}^{(j)} \sim P^*_X \ \text{for} \ j = 1, \cdots, M.
$$

여기서 $$X=\{ X_1, \cdots, X_N \}$$은 Random Variable의 집합이다. 이러한 데이터는 편하게 i.i.d.로 생성된 데이터라고 말한다.

이러한 데이터는 또한 시스템의 변수 집합 $$X$$에 대한 모든 가능태들이 등장하였는지 여부에 따라서 두 가지로 분류될 수 있다.

첫 번째는 Complete Data이다. Complete Data는 Fully Observed Data라고도 불리는데 시스템의 변수 집합 $$X$$의 모든 가능태들이 등장한 데이터 셋을 Complete Data라고 정의한다. 반대로 모든 가능태들이 등장하지는 않은 데이터 셋은 Incomplete Data라고 정의하게 된다. Incomplete Data는 Partially Observed Data라고도 불린다.

## Density Estimation
방금 언급했듯이 우리에게 주어지는 데이터 셋은 i.i.d.로 생성되었다고 가정하게 되는데, 그 과정에서 확률 분포 $$P^*_X$$를 언급하였다. 이 확률 분포는 우리에게 주어진 데이터 셋을 생성한 확률 분포라고 우리가 `가정`한 것이라고 보면 된다. 하지만 우리는 이것의 존재를 가정한 것이지, 어떤 분포인지까지 가정하지는 않았다. 즉, 우리가 해야할 것은 이 확률 분포를 최대한 정확히 `추정`하는 것이다.

이러한 확률 분포를 추정하는 것을 Density Estimation이라고 한다. Density Estimation은 관찰된 데이터를 통해서 관찰되지 않은 확률 분포를 추정하는 것이라고 정의할 수 있다. 즉, $$P^*_X$$와 최대한 가까운 $$\tilde{P}_X$$를 찾는 과정이라고 볼 수 있다.

그렇다면 최대한 가깝다는 것은 어떻게 정의할 수 있을까? 가깝다고 말하기 위한 기준이되는 Measure를 선택하고 그것을 통해서 가장 가까운 분포를 찾아내는 일련의 과정이 필요하게 될 것이다. 이 과정이 바로 Statistical Learning이 된다.