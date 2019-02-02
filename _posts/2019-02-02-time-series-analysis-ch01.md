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
- 즉, 다음과 같이 모델링이 될 수 있다: $$S_t = X_1+X_2+\cdots + X_t \ \text{for} \ t=1,2,\cdots, \ \ \ \{X_t\}: \ \text{IID Noise}$$
- 특히 $$\{X_t\}$$가 Binary Process라면 $$\{S_t\}$$는 Simple Symmetric Random Walk라고 한다.

## Trend와 Seasonality를 가진 모델
이러한 모델은 보통 $$X_t = m_t + Y_t$$의 형태로 모델링될 수 있다. $$m_t$$는 천천히 변화하는 Trend Component이고 $$Y_t$$는 Zero-Mean의 랜덤 변수이다. 보통 이러한 모델은 $$m_t$$를 다음과 같이 모델링하여서 Least Square Regression을 사용하여 파라미터 $$a_0, a_1, a_2$$를 찾아낸다.

$$m_t = a_0 + a_1t + a_2t^2$$



