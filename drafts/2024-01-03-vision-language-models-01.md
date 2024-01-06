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
- [학습 전략](#학습-전략)
    - [Contrastive Learning](#constrastive-learning)
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

이 핵심 요소들은 모델 구조와 학습 전략을 고려하여 설계된 손실 함수 등을 통해서 서로 밀접하게 연관되어 있다. 비전-언어 모델의 연구 분야는 새로운 분야임에도 불구하고 이러한 모델 설계는 해가 지날수록 크게 변화되고 있다. 과거의 연구들은 사람이 직접 수작업한 이미지 묘사 기술과 사전 훈련된 단어 임베딩 벡터, 또는 단어 빈도수 기반의 TF-IDF 특징을 활용하였다. 반면 최근 연구들은 이미지와 텍스트 특징을 독립적 또는 통합적으로 학습시키는 방식의 Transformer 기반의 이미지 인코더와 텍스트 인코더를 활용한다. 이러한 최신 모델들은 하위 작업들을 수행하기 위해서 전략적으로 사전 훈련이 되었다.

이번 섹션에서는 이러한 비전-언어 모델 사전 훈련의 일반적인 전략들을 확인해볼 것이다. 또한 이러한 전략들에 특화되어 있거나 또는 일반적으로 널리 사용될 수 있는 추가적인 것들도 함께 다뤄볼 것이다.

사전 훈련의 전략을 커버하기 위하여 다음의 주제들을 다룰 예정이다:
- **Constrastive Learning**: Constrastive 방식을 통해서 이미지와 텍스트를 통합 특징 공간에 임베딩하는 방법론
- **PrefixLM**: 언어 모델에 Prefix로 이미지를 입력하는 방식을 통해서 이미지와 텍스트 임베딩을 통합적으로 학습하는 방법론
- **Cross Attention을 통한 멀티모달 결합**: Cross Attention 메커니즘을 통한 비전 정보를 언어 모델의 계층으로 결합하는 방법론
- **MLM / ITM**: 마스크 언어 모델 및 이미지-텍스트 매칭 방식을 활용하여 이미지 일부를 텍스트와 결합하는 방법론
- **No Tranining**: 각각의 비전 모델과 언어 모델을 따로 사용하여 반복적인 최적화를 수행하는 방법론

여기 나와있는 목록은 현재 존재하는 모든 방법론을 나열한 것이 아니라 빠진 것이 있을 수 있으니 더 자세한 사항은 [링크](https://arxiv.org/abs/2210.09263)를 참고하면 좋다.

### Constrastive Learning

![](assets/img/2024-01-03-vision-language-models-01/contrastive-learning.png)

*(출처: [https://openai.com/research/clip](https://openai.com/research/clip))*

`Constrastive Learning`은 컴퓨터 비전 분야에서 널리 사용되었던 사전 훈련 방법론이며 또한 비전-언어 모델의 사전 훈련에도 매우 효과적이라는 것이 증명된 방법론이다. 최근 연구들인 [CLIP](https://arxiv.org/abs/2103.00020), [CLOOB](https://arxiv.org/abs/2110.11316), [ALIGN](https://arxiv.org/abs/2102.05918), [DeCLIP](https://arxiv.org/abs/2110.05208) 등은 이미지 인코더와 텍스트 인코더를 거대 데이터와 Constrastive 손실 함수를 통해서 통합적으로 학습하여 비전과 언어 양상 사이를 연결하였다. 여기서 사용된 거대 데이터는 (이미지, 설명문) 쌍으로 구성된다. Constrastive Learning은 입력 이미지와 입력 텍스트를 동일한 특징 공간으로 매핑하고 둘이 매치가 된다면 거리를 가깝게, 그렇지 않다면 거리를 멀게끔 학습시키는 방법론이다.

CLIP에서는 특징 공간에서의 거리를 두 임베딩 사이의 코사인 유사도로 정의하였고 ALIGN이나 DeCLIP 등의 연구는 잡음이 섞인 데이터셋을 고려하여 자신들만의 메트릭을 따로 정의하였다.

[LiT](https://arxiv.org/abs/2111.07991)라는 또다른 연구는 사전 훈련된 CLIP의 이미지와 텍스트 인코더를 활용하여 이미지 인코더는 그대로 둔 채로 텍스트 인코더만을 파인 튜닝하는 간단한 방법을 제안했다. 저자들은 이 아이디어를 다음과 같이 해석한다: *텍스트 인코더로 하여금 이미지 인코더로부터의 이미지 임베딩을 더 잘 읽을 수 있도록 하는 방법이다.* 이 접근은 CLIP에 비해서 더 효과적이고 데이터 효율성이 좋다는 것이 증명됐다.

[FLAVA](https://arxiv.org/abs/2112.04482) 등의 다른 연구들은 Contrastive Learning과 다른 사전 훈련 방법론들을 조합하여 비전 및 언어 임베딩을 획득하는 방법론을 제안했다.

### PrefixLM

![](assets/img/2024-01-03-vision-language-models-01/prefixLM.png)

*(출처: [https://arxiv.org/abs/2108.10904](https://arxiv.org/abs/2108.10904))*

그 다음 비전-언어 모델 학습 방법론은 `PrefixLM` 방법론이다. [SimVLM](https://arxiv.org/abs/2108.10904), [VirTex](https://arxiv.org/abs/2006.06666v3) 등의 모델들이 PrefixLM 방법론을 적용한 모델들이다. 이 모델들은 Transformer 인코더, Transformer 디코더로 구성된 통합된 멀티모달 구조를 가지고 있으며 Autoregressive 언어 모델의 형태와 매우 유사하다.

조금 더 자세히 살펴보도록 하자. Prefix를 다루는 언어 모델들은 보통 주어진 입력 문장을 Prefix로 하여 그 다음 토큰을 예측하도록 학습된다. 예를 들면, "A man is standing at the corner"라는 문장이 주어졌을 때 "A man is standing at the"를 Prefix로 삼고 모델로 하여금 "Corner" 등과 같은 토큰 에측을 통해 주어진 Prefix에 대해서 합당한 다음 문장을 생성하도록 학습시킨다.

**Frozen PrefixLM**

![](assets/img/2024-01-03-vision-language-models-01/frozen-prefixLM.png)

*(출처: [https://huggingface.co/blog/vision_language_pretraining](https://huggingface.co/blog/vision_language_pretraining))*

## 참고 자료
- [https://huggingface.co/blog/vision_language_pretraining](https://huggingface.co/blog/vision_language_pretraining)
- [Vision-Language Pre-training: Basics, Recent Advances, and Future Trends](https://arxiv.org/abs/2210.09263)
- [CLIP: Connecting text and images](https://openai.com/research/clip)
- [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)
- [CLOOB: Modern Hopfield Networks with InfoLOOB Outperform CLIP](https://arxiv.org/abs/2110.11316)
- [Scaling Up Visual and Vision-Language Representation Learning With Noisy Text Supervision](https://arxiv.org/abs/2102.05918)
- [Supervision Exists Everywhere: A Data Efficient Contrastive Language-Image Pre-training Paradigm](https://arxiv.org/abs/2110.05208)
- [LiT: Zero-Shot Transfer with Locked-image text Tuning](https://arxiv.org/abs/2111.07991)
- [FLAVA: A Foundational Language And Vision Alignment Model](https://arxiv.org/abs/2112.04482)
- [SimVLM: Simple Visual Language Model Pretraining with Weak Supervision](https://arxiv.org/abs/2108.10904)
- [VirTex: Learning Visual Representations from Textual Annotations](https://arxiv.org/abs/2006.06666v3)

## 수정 사항
- 2024.01.02
    - 최초 게제