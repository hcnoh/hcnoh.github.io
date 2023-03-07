---
layout: post
use_math: true
title: "[Object Detection] 1. 객체 검출 소개"
date: 2023-03-02 00:00:00
tagline: "컴퓨터 비전 분야에서 중요한 태스크 중 하나인 객체 검출에 대한 기본적인 내용 소개"
categories:
- Object Detection Study
tags:
- deep learning
- object detection
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2023-03-02-object-detection-01
---

새로운 업무를 수행하기 위해서 객체 검출(Object Detection) 스터디를 진행하였고 그 과정에서 내용을 정리하여 새로운 포스트를 연재할 계획이다. 객체 검출의 큰 두 줄기를 따라가며 최신의 연구들까지 다뤄보는 것을 목표로 한다.

## 객체 검출 소개
`객체 검출`(Object Detection)이란 영상 처리(Image Processing)의 한 분야이며 영상 내에서 특정 객체를 검출하여 해당 객체의 위치를 파악하고 이 객체가 어떤 분류에 속하는지 분류까지 수행하는 태스크를 말한다. 객체 검출에 대해서 자세히 알아보기 전에 영상 처리의 대표적인 태스크들을 먼저 알아보자.

![](assets/img/2023-03-02-object-detection-01/2023-03-07-16-55-22.png)

위 그림은 영상 처리의 대표적인 태스크들을 설명하는 그림이다. 영상 처리는 크게 영상 내부에 물체가 하나인 경우(Single Object)와 여러개인 경우(Multiple Objects)로 나뉜다. 영상 내부에 물체가 하나인 경우 그 물체가 무엇인지 분류하는 태스크를 `분류`(Classification) 태스크라고 한다. 여기에 추가적으로 그 물체의 위치까지 검출하는 태스크를 `Classificaion + Localization` 태스크라고 한다.

그 다음으로 영상 내부에 물체가 여러개인 경우 그 물체들의 위치를 `Bounding Box`라고 하는 박스 형태로 검출하고, 그 물체 각각이 어떤 물체인지 분류까지 하는 태스크를 `객체 검출`(Object Detection)이라고 한다.

## 객체 검출 모델

### R-CNN 계열

### YOLO 계열

## 참고 자료
- [Object Detection이란? Object Detection 용어 정리](https://leedakyeong.tistory.com/entry/Object-Detection%EC%9D%B4%EB%9E%80-Object-Detection-%EC%9A%A9%EC%96%B4%EC%A0%95%EB%A6%AC)
- [Thinking About Specializing In Computer Vision? Here is Two Important Terms You Need To Know!](https://medium.com/@TerranceWhitehurst/thinking-about-specializing-in-computer-vision-here-is-two-important-terms-you-need-to-know-fbe654484541)
- [Object Detection 기초 개념과 성능 측정 방법](https://techblog-history-younghunjo1.tistory.com/178)

## 수정 사항
- 