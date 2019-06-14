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

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-20-01.png)
(Piech, Chris, et al. "Deep knowledge tracing." Advances in neural information processing systems. 2015.)

DKT 모델은 위 그림과 같은 간단한 RNN 모델로 구성되어 있다. DKT 모델은 아래와 같이 5개의 Time Step을 입력으로 받아서 6번째 Time Step에서의 전체 문제 세트 (여기서는 3문제로 구성되어 있다고 가졍) 각각에 대한 정답을 맞출 확률을 예측하게 된다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-24-03.png)

DKT 모델을 자세히 살펴보기 위해서는 이 모델이 One-Hot Encoding과 RNN이라는 두 단계로 구성되어 있다는 점을 짚고 넘어갈 필요가 있다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-27-06.png)

## One-Hot Encoding
One-Hot Encoding은 상당히 Naive하게 구성하였다. 전체 문제 세트가 $$M$$개라고 할 때 One-Hot Encoding은 기본적으로 $$2M$$ 차원으로 이루어지게 된다. 처음 $$M$$ 차원의 요소들은 각각 현재 Time Step에서 학습자가 어떤 문제를 풀었는지 Binary로 인코딩을 하게 된다.

나중 $$M$$ 차원의 요소들은 각각 푼 문제가 틀렸는지(0) 맞았는지(1)를 Binary로 인코딩을 하게 된다.

아래 그림을 통해서 예제를 살펴볼 수 있다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-31-30.png)

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-31-58.png)

## Inference
인코딩 이후에 RNN 모델의 Inference는 다음 그림과 같이 수행되게 된다. 여전히 매우 Naive한 접근이라는 것을 알 수 있다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-33-37.png)

## Training
Training은 아래 그림과 같이 다음 Time Step에서 푼 문제만을 고려하여 학습의 Label로 사용하여 수행되게 된다. 이 경우 Loss Function은 아래와 같이 Binary Cross-Entropy를 사용하게 된다.

$$
L = \sum_t l(\mathbf{y}^{\text{T}}] \delta(q_{t+1}), a_{t+1})
$$

여기서 $$l$$은 Binary Cross-Entropy가 된다.

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-40-30.png)

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-41-04.png)

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-41-31.png)

![](/assets/2019-06-14-deep-knowledge-tracing/2019-06-14-deep-knowledge-tracing_2019-06-14-23-42-01.png)


