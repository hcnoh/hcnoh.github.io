---
layout: post
use_math: true
title: "[비전-언어 모델] 1. 비전-언어 모델 소개"
date: 2024-01-03 00:00:00
tagline: "최근 가장 많이 사용되고 있는 멀티모달 모델인 비전-언어 모델에 대한 개괄적인 소개 포스트"
categories:
- 비전-언어 모델 스터디
tags:
- deep learning
- natural language processing
- computer vision
- vision-language models
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2024-01-03-vision-language-models-01
---

이번 포스트에서는 최근 AI 업계의 트렌드인 멀티모달 모델, 그 중에서도 특히 많이 활용되는 비전-언어 모델에 대한 소개를 다뤄볼 예정이다. 이를 위하여 [링크](https://huggingface.co/blog/vision_language_pretraining)를 번역 및 참고하여 이번 포스트를 작성하였다.

## 목차
- [비전-언어 모델 소개](#비전-언어-모델-소개)
- [참고 자료](#참고-자료)
- [수정 사항](#수정-사항)

## 비전-언어 모델 소개
`비전-언어 모델`(Vision-Language Models)이란 간단하게 비전과 언어의 양상(Modality)를 통합하는 모델을 말한다. 조금 더 정확하게는 이미지와 자연어 텍스트를 모두 처리할 수 있는 능력을 가진 모델을 말할 수 있다. 비전-언어 모델의 이해를 도울 수 있는 간단한 예시를 살펴보도록 하자. 아래의 예시는 비전-언어 모델을 통한 Zero-shot 이미지 분류 예시이다. 우리는 모델에게 이미지와 약간의 프롬프트를 입력하여 모델로 하여금 입력 이미지에 가장 그럴듯한 프롬프트를 고르도록 할 것이다.

![](assets/img/2024-01-03-vision-language-models-01/zero-shot-image-classification.png)

*(출처: [https://huggingface.co/blog/vision_language_pretraining](https://huggingface.co/blog/vision_language_pretraining))*

이 예측을 수행하기 위해서는 모델은 입력 이미지와 텍스트를 모두 이해할 수 있어야만 한다. 이러한 이해를 위해서 모델은 분리되어 있거나 또는 합쳐져 있는 비전을 위한 인코더와 언어를 위한 인코더로 구성되어 있다.

한편 이러한 입력과 출력의 형태는 다양할 수 있다. 아래는 다양한 형태의 입력 예시이다:
- Text-Image Retrieval: 자연어 텍스트로부터의 이미지 검색
- 문장 채우기: 입력 이미지와 자연어 문장으로부터 객체 검출, e.g. A **young person** swings a **bat**.
- VQA(Visual Question Answering): 입력 이미지와 자연어 질문으로부터 정답을 추출
- 이미지 캡셔닝: 주어진 이미지로부터 설명문을 생성
- 이미지와 텍스트로부터 소셜 미디어의 혐오 발언 검출

## 학습 전략
일반적으로 비전-언어 모델은 다음과 같은 3가지 핵십 요소로 구성된다:
- 이미지 인코더 (Image Encoder)
- 텍스트 인코더 (Text Encoder)
- 두 인코더의 정보를 결합할 수 있는 전략

이 핵심 요소들은 모델 구조와 학습 전략을 고려하여 설계된 손실 함수 등을 통해서 서로 밀접하게 연관되어 있다. 비전-언어 모델의 연구 분야는 새로운 분야임에도 불구하고 이러한 모델 설계는 해가 지날수록 크게 변화되고 있다. 과거의 연구들은 사람이 직접 수작업한 이미지 묘사 기술과 사전 학습된 단어 임베딩 벡터, 또는 단어 빈도수 기반의 TF-IDF 특징을 활용하였다. 반면 최근 연구들은 이미지와 텍스트 특징을 독립적 또는 통합적으로 학습시키는 방식의 Transformer 기반의 이미지 인코더와 텍스트 인코더를 활용한다. 이러한 최신 모델들은 하위 작업들을 수행하기 위해서 전략적으로 사전 학습이 되었다.

이번 섹션에서는 이러한 비전-언어 모델 사전 학습의 일반적인 전략들을 확인해볼 것이다. 또한 이러한 전략들에 특화되어 있거나 또는 일반적으로 널리 사용될 수 있는 추가적인 것들도 함께 다뤄볼 것이다.

사전 학습의 전략을 커버하기 위하여 다음의 주제들을 다룰 예정이다:
- **Constrastive Learning**: Constrastive 방식을 통해서 이미지와 텍스트를 통합 특징 공간에 임베딩하는 방법론
- **PrefixLM**: 언어 모델에 Prefix로 이미지를 입력하는 방식을 통해서 이미지와 텍스트 임베딩을 통합적으로 학습하는 방법론
- **Cross Attention을 통한 멀티모달 결합**: Cross Attention 메커니즘을 통한 비전 정보를 언어 모델의 계층으로 결합하는 방법론
- **MLM / ITM**: 마스크 언어 모델 및 이미지-텍스트 매칭 방식을 활용하여 이미지 일부를 텍스트와 결합하는 방법론
- **No Tranining**: 각각의 비전 모델과 언어 모델을 따로 사용하여 반복적인 최적화를 수행하는 방법론

## 참고 자료
- [https://huggingface.co/blog/vision_language_pretraining](https://huggingface.co/blog/vision_language_pretraining)

## 수정 사항
- 2024.01.02
    - 최초 게제