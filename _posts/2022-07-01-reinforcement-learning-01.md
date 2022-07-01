---
layout: post
use_math: true
title: "[Reinforcement Learning] 1. 강화 학습 소개"
date: 2022-07-01 00:00:00
tagline: "강화 학습의 소개 및 강화 학습 내용을 기술하기 위한 기초적인 내용들 정리"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-07-01-machine-learning-01
---

강화 학습에 대해서 스터디를 진행하였고, 스터디 내용을 정리하여 연재를 진행하려고 한다. 주된 참고 자료는 강화 학습의 중요 알고리즘에 대한 논문 및 발표 자료들을 직접 활용하였다.

본 포스트 및 향후 업로드될 시리즈에서는 강화 학습의 내용을 기술해 나감에 있어서 Policy Gradient 위주로 내용 정리를 해나갈 계획이며, 이에 따라 Policy Gradient 계열의 중요 알고리즘인 TRPO, PPO 등을 자세히 다루게 될 것이다.

## 강화 학습이란?
강화 학습(Reinforcement Learning, RL)은 머신 러닝의 한 분야이며 환경(Environment) 내부에서 Agent의 최적의 Policy를 찾는 것을 목표로 한다. 여기서 Agent와 환경의 관계에 대해서 정리하고 넘어가도록 한다.
- Agent는 현재 환경의 State를 관찰하고, 그에 따른 Action을 수행한다.
- 환경은 Agent의 Action을 받아서 그에 따른 결과로 State의 변화를 일으키며, 추가적으로 Action에 따른 보상(Reward)을 Agent에게 되돌려준다.

즉, Agent와 환경은 서로 상호작용을 하게 되며, 이 상황에서 Agent의 누적 보상을 최대화하도록 하는 최적의 Policy를 찾는 것이 강화 학습의 목적이 된다.

## Value Function
Agent가 어떤 순간에

## 참고 자료
- [Wikipedia](https://en.wikipedia.org/wiki/Reinforcement_learning)

## 수정 사항
- 2022.07.01
    - 최초 게제