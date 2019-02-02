---
layout: post
use_math: true
title: "[Time Series Analysis] Chap 1. Time Series Analysis 소개"
date: 2019-02-02 13:36:57
tagline: "Time Series 분석 스터디 내용 정리"
categories:
- Time Series Analysis
tags:
- time series analysis
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-02-02-time-series-analysis-ch01
---

이번 포스팅은 다음의 도서를 스터디하여 정리한 내용이다.
- Peter J Brockwell, Richard A Davis "Introduction to Time Series and Forecasting"

## Time Series Analysis란?
Time Series란 Observation $$x_t$$의 시퀀스이다. 대부분의 공학적인 문제 및 사회 과학적 문제, 그리고 경제학적인 문제들은 이러한 Time Series로 표현될 수 있다.

그렇다면 Time Series Analysis란 무엇일까? 데이터로 주어진 Series를 보고 Inference를 이끌어내는 기술들을 말한다. 즉, 데이터를 설명할 수 있는 Hypothetical Probability Model을 찾는 것이라고 할 수 있겠다.

## Zero-Mean 모델
IID Noise:
- Trend도 없고 Seasonal Component도 없는 모델이다.
- Observation들이 각각 독립적(Independent)이고 동일한 분포를 갖는(Identical) 랜덤 이다.
- Observation들은 모두 Zero-Mean의 특징을 갖는다.

Binary Process:
- 다음과 같은 특징을 가진 IID 랜덤 변수들의 시퀀스 모델이다: $$P[X_t=1]=p, \ \ \ P[X_t=-1]=1-p, \ \ \ p=\frac{1}{2}$$

Random Walk:
- IID 랜덤 변수들의 합계로 모델링된다.
- 즉, 다음과 같이 모델링이 될 수 있다: $$S_t = X_1+X_2+\cdots + X_t, \ \ \ \text{for} \ t=1,2,\cdots, \ \ \ \{X_t\}: \ \text{IID Noise}$$
- 특히 $$\{X_t\}$$가 Binary Process라면 $$\{S_t\}$$는 Simple Symmetric Random Walk라고 한다.

## Trend와 Seasonality를 가진 모델
이러한 모델은 보통 다음과 같은 형태로 모델링될 수 있다:

$$X_t = m_t + Y_t$$

$$m_t$$는 천천히 변화하는 Trend Component이고 $$Y_t$$는 Zero-Mean의 랜덤 변수이다. 보통 이러한 모델은 $$m_t$$를 다음과 같이 모델링하여서 Least Square Regression을 사용하여 파라미터 $$a_0, a_1, a_2$$를 찾아낸다.

$$m_t = a_0 + a_1t + a_2t^2$$

## Harmonic Regression
대부분의 Time Series는 날씨 등의 Season에 따라서 바뀌는 팩터의 영향을 받게 된다. 이러한 효과는 고정된 주기를 지닌 Periodic Component를 이용하여 모델링할 수 있다.

Seasonal Effect를 표현하기 위해서 다음과 같이 모델링할 수 있다:

$$X_t = s_t + Y_t$$

여기서 $$s_t$$는 다음과 같이 Harmonic들의 합으로 나타낼 수 있는 주기 $$d$$를 가진 시간 $$t$$에 대한 함수이다.

$$s_t = a_0 + \sum_{j=1}^k(a_j \cos(\lambda_j t) + b_j \sin(\lambda_j t))$$

여기서 $$a_0,\cdots, a_k, b_1,\cdots, b_k$$는 찾아내야 하는 파라미터이고 $$\lambda_1,\cdots, \lambda_k$$는 고정된 주파수이다. 마찬가지로 Least Square Regression을 이용하여 파라미터를 찾아낼 수 있다.

## Time Series 분석의 기본적인 접근 방법
Time Series 모델의 분석은 다음과 같은 방법으로 수행한다.
1. 그래프의 주된 특징을 분석하여 다음과 같은 특징을 지니고 있는지 파악한다.
  - Trend
  - Seasonal Component
  - 눈에 띄는 급격한 변화 및 Observation
2. Stationary Residual을 찾기 위해서 Trend 정보 및 Seasonal Component 정보를 삭제
3. Residual에 Fit하기 위한 모델을 선정
4. Residual에 대한 예측을 통해서 실제 시퀀스인 $$\{X_t\}$$에 대한 예측도 가능
5. Fourier 분석을 시도해보는 것이 좋음

여기서 잠깐 짚고 넘어가볼 부분이 있다. 위에서 모델링을 통한 Least Square Regression을 이용하여 모델의 파라미터를 찾아내는 방식은 최근의 빅데이터와 딥러닝(여기서는 RNN 계열)을 이용하는 방식에 비해서 일반적으로 더 낮은 성능을 보이고 있다.







