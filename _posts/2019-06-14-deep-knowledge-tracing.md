---
layout: post
use_math: true
title: "[Recommender System] Deep Knowledge Tracing 논문 정리"
date: 2019-06-14 16:04:32
tagline: "교육용 어플리케이션에서 문제 추천을 위한 학습자 수준 진단 모델인 Deep Knowledge Tracing을 스터디하여 정리"
categories:
- Deep Learning
tags:
- deep learning
- recommender system
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-14-deep-knowledge-tracing
---

이번 포스팅은 다음의 논문을 스터디하여 정리하였다:
- [링크1](http://papers.nips.cc/paper/5654-deep-knowledge-tracing.pdf)

## Knowledge Tracing
Knowledge Tracing이란 학습자의 퍼포먼스를 바탕으로 학습자의 전체 지식 수준을 평가하는 Task를 말한다. Knowledge Tracing을 적용하기 위해서 해당 모델이 가정하고 있는 몇 가지 상황에 대해서 정리해보면 다음과 같다. 먼저 활용하고자 하는 정보인 학습자 퍼포먼스는 바로 학습자가 풀어놓은 문제 풀이 결과를 말한다. 또한 평가하기 위한 지표인 학습자의 지식 수준은 학습자가 주어진 문제들을 잘 풀어낼 확률을 말하게 된다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-16-12-12.png)

위 그림에서는 학습자의 퍼포먼스로 주어진 문제 세트에 대한 학습자의 정오답 결과가 주어지게 되며 Knowledge Tracing 모델은 이 정오답 결과를 바탕으로 학습자의 지식 수준에 해당하는 전체 문제 세트에 대한 정답률 예측 결과를 내보내게 된다.

또한 가장 중요한 가정은 바로 문제 풀이 순서에 따라서 학습자의 학습 효율이 달라질 수 있다는 점이다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-16-13-04.png)

위의 그림에서와 같이 주어진 문제 세트의 순서에 따라서 학습자의 지식 수준이 달라지는 것을 확인할 수 있다. 이러한 가정들을 바탕으로 Knowledge Tracing이 수행되게 된다.

## Deep Knowledge Tracing
Deep Knowledge Tracing (DKT) 모델은 처음으로 Knowledge Tracing 분야에 Deep Learning을 적용한 논문이다. 특히 RNN을 사용하였으며 추가적인 Contribution은 학습된 모델을 활용할 수 있는 Task들도 제시했다는 점을 들 수 있겠다. 그 Task들은 정리하면 아래와 같다:
- 문제 시퀀스 최적화
- 문제 사이의 연관성 정의

