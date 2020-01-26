---
layout: post
use_math: true
title: "[Graphical Model Tutorial] Chap 2. Markov Random Field"
date: 2020-01-26 10:19:09
tagline: "Markov Random Field에 대한 전반적인 소개 및 예시"
categories:
- Graphical Model
tags:
- graphical model
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2020-01-26-graphical-model-02
---

## Markov Random Field
지난번에는 확률변수들 사이의 의존성에 방향이 포함된 상황을 어떻게 형식적으로 다룰 것이냐에 대한 내용인 Bayesian Network에 대해서 다뤄보았다. 이번에는 방향이 없는 경우에는 어떻게 할 것이냐에 대한 내용을 다뤄보도록 하겠다.

Bayesian Network가 DAC였던 것과는 다르게 Markov Random Field는 Undirected Graphical Model 구조에 기반을 둔 모델이다. Bayesian Network와 마찬가지로 Node는 확률변수를 나타내며 Edge는 그 확률변수들 사이의 상호작용을 나타내게 된다. 다만 방향성이 없기 때문에 조건부 확률분포로 가정할 수는 없으며 중력, 전자기력같은 영향력을 상상하면 될 것 같다.

확률변수 사이의 무슨 중력에 대해서 이야기하는지 도저히 이해가 안갈 수 있지만 간단한 예시를 통해서 확인해보도록 하자.

## Markov Random Field 예시: 전하 모델
전하 3개가 균일하게 분포되어 있다고 가정하자. 이 때 각각의 전하는 +1 또는 -1 전하만을 가질 수 있다. 3개의 전하는 당연하게도 서로에게 영향을 끼칠 것이다.

그렇다면 이러한 상황에서 이 시스템의 가장 안정적인 (그럴듯한, 있을법한) 전하 배치는 어떻게 될까? 아래 그림을 살펴보자.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-26-01.png)

위의 그림에서 붉은색으로 표시된 부분이 바로 가장 안정적인 전하 배치라고 볼 수 있을 것이다. 그 이유는 Potential 에너지를 고려하면 알 수 있다.

![](/assets/img/2020-01-26-graphical-model-02/2020-01-26-graphical-model-02_2020-01-26-10-27-05.png)

즉, Potential 에너지가 가장 작은 전하 배치가 가장 안정적인 전하 배치가 될 것이다.

## Gibbs Measure
