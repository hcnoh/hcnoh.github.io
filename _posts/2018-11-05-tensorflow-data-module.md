---
layout: post
title: "TensorFlow의 tf.data 모듈 기본 사용법 정리"
date: 2018-11-05 10:52:42
tagline: "TensorFlow에서 학습 데이터를 불러올 수 있게 해주는 모듈인 tf.data 모듈 및 TFRecord에 대한 기본적인 사용법 정리"
categories:
- TensorFlow
tags:
- tensorflow
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-11-05-tensorflow-data-module
---

이번 포스팅은 `TensorFlow` 공식 홈페이지에 나온 내용을 참고하여 작성하였다.

## tf.data 모듈이란?
`TensorFlow`를 처음 접하는 경우에 만나게 되는 객체 및 메서드로 `tf.placeholder` 및 `sess.run(feed_dict={})`를 들 수 있다. 이 둘의 기능은 우리가 구현한 모델에 학습용 또는 추론용 데이터를 모델에 입력(보통 `feeding`이라고 표현)시키기 위한 도구라고 볼 수 있다. `tf.placeholder`는 `feeding`할 데이터의 형식 및 모양을 정의하는 어떤 틀이라고 볼 수 있다. 이렇게 정의된 `tf.placeholder`는 이미 정의된 `tf.Session`을 실행시키는 메서드인 `sess.run`과 함께 사용되어 모델에 데이터를 `feeding`시킬 수 있다.

