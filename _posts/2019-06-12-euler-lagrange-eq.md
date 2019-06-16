---
layout: post
use_math: true
title: "[Classical Mechanics] Euler-Lagrange Equation 정리"
date: 2019-06-12 23:42:00
tagline: "Euler-Lagrange Equation 유도 및 관련 내용 정리"
categories:
- Physics
tags:
- physics
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-12-euler-lagrange-eq
---

## Galilean Transformation
Galilean Transformation이란 어떤 Inertial Frame (관성계) $$\mathcal{S}$$에서의 균일한 운동으로부터 또 다른 Inertial Frame $$\mathcal{S}'$$에서의 균일한 운동으로의 Mapping이라고 정의한다.

Galilean Transformation의 종류는 아래와 같다:
- Temporal Translation: $$t \rightarrow t + \Delta t$$
- Spatial Translation: $$\mathbf{r} \rightarrow \mathbf{r} + \Delta \mathbf{r}$$
- Rotational Translation: $$\mathbf{r} \rightarrow \mathcal{R}\mathbf{r}, \ \mathcal{R}\in \mathcal{O}(3)$$
- Galilean Boost: $$\mathbf{r} \rightarrow \mathbf{r} + \mathbf{v} t$$

여기서 각각의 변수는
- $$t$$: 시간
- $$\mathbf{r}$$: 위치 벡터, $$\mathbf{r} \in \mathbb{R}$$
- $$\mathbf{v}$$: 속도 벡터

## Generalized Coordinates
앞으로 Generalized Coordinates $$\mathbf{q}$$를 자주 보게 될 것이다. Generalized Coordinates의 정의는 시간에 따른 System의 위치를 Unique하게 나타낼 수 있는 독립적인 물리량들을 말한다.

예를 들면 어떤 회전하는 물체는 Cartesian Coordinate에서는 $$x, y, z$$로 위치를 나타낼 수 있으며 $$Cylinderical Coordinate$$에서는 $$r, \theta, z$$로 위치를 나타낼 수 있다. 따라서 이러한 $$x, y, z$$의 변수들 한 묶음은 Generalized Coordinates를 구성하게 되며 마찬가지로 $$r, \theta, z$$의 변수들 한 묶음도 또 다른 Generalized Coordinates를 구성하게 된다.

## Principle of Least Action (Hamilton's Principle)

## Lagrangian의 특징


