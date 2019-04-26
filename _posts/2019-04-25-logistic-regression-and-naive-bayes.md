---
layout: post
use_math: true
title: "[Machine Learning] Logistic Regression 및 Naive Bayes 정리"
date: 2019-04-25 11:04:33
tagline: "머신 러닝의 가장 기초적인 개념인 Logistic Regression 및 Naive Bayes 방법 정리"
categories:
- Machine Learning
tags:
- machine learning
- statistics
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-04-25-logistic-regression-and-naive-bayes
---

## Optimal Classifier
Optimal Classifier의 정의: $$f^*(x) = \arg \max_{Y=y}P(Y=y \vert X=x)$$

Optimal Classifier를 찾기 위한 모델링 방법 2가지:
- Discriminative Model: $$P(Y\vert X)$$를 직접 모델링하여 추정
- Generative Model: $$P(Y\vert X) = \frac{P(X\vert Y)P(Y)}{P(X)}$$로 분해해서 각각의 확률을 추정하여 $$P(Y\vert X)$$를 추정
- 둘 사이는 MLE와 MAP와 같음… 이건 나중에 천천히 얘기해보는 것으로

강의에서는 Discriminative Model의 대표적인 예시로 Logistic Regression을, Generative Model의 대표적인 예시로 Naive Bayes를 다루게 됨

## Logistic Regression
Logistic Regression이란?
- 대표적인 Logistic Function 예시: Sigmoid, Tanh, 등등

$$P(Y\vert X)$$를 Logistic Function을 이용하여 모델링을 하고 그것을 Fitting하는 것을 Logistic Regression이라고 함
여기서는 Sigmoid를 사용하여 예시를 들었음: $$Sigmoid(x) = \frac{1}{1+e^{−x}}$$

Binary Classifier의 예시:
- y는 0 또는 1, x는 벡터
- 모델링(Bernoulli Distribution):

$$
\begin{align*}
P(Y=y\vert X=\mathbf{x};\boldsymbol{\theta}) & = p_{\boldsymbol{\theta}}(\mathbf{x})^y(1−p_{\boldsymbol{\theta}}(\mathbf{x}))^{1−y} \\
p_{\boldsymbol{\theta}}(\mathbf{x}) & = P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})\\
& = \frac{1}{1+e^{−\boldsymbol{\theta}^{\text{T}}\mathbf{x}}}
\end{align*}
$$

- 파라미터 $$\boldsymbol{\theta}$$는 Maximum Likelihood Estimation으로 찾아내게 됨!

$$
\begin{align*}
\widehat{\boldsymbol{\theta}}\
& = \arg \max_{\boldsymbol{\theta}} P(D ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in D} P(Y=y, X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in D} P(X=\mathbf{x} ; \boldsymbol{\theta}) P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
\end{align*}
$$

여기서 $$P(X=\mathbf{x} ; \boldsymbol{\theta})$$는 $$X$$와 $$\boldsymbol{\theta}$$가 독립이기 때문에 상수취급되며 따라서 Optimization에 영향을 주지 않기 때문에 생략이 가능할 것이다.

$$
\begin{align*}
\widehat{\boldsymbol{\theta}}
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in D} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x}))
\end{align*}
$$

특히 마지막 식은 Binary Cross-Entropy를 최소화하는 문제로 바꿀 수 있음을 확인할 수 있을 것이다. 아래와 같이 식을 수정해보자.

$$
\begin{align*}
\widehat{\boldsymbol{\theta}}
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y\sim P_D(y\vert \mathbf{x})} y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} N_D(\mathbf{x})P_D(Y=1 \vert X=\mathbf{x})\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + N_D(\mathbf{x})P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x}))
\end{align*}
$$

여기서 $$N_D(\mathbf{x})$$는 주어진 $$\mathbf{x}$$에 대한 데이터셋 내부의 샘플 갯수이며, $$P_D$$는 주어진 확률 변수에 대한 데이터셋 내부의 분포이다. 만약 데이터셋의 분포가 균일하여 $$N_D(\mathbf{x})$$가 모든 샘플 데이터 $$\mathbf{x}$$에 대해서 일정하다면 상수취급되어 Optimization 문제에서 생략될 것이다.

$$
\begin{align*}
\widehat{\boldsymbol{\theta}}
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} P_D(Y=1 \vert X=\mathbf{x})\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} P_D(Y=1\vert X=\mathbf{x})\log(P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})) + P_D(Y=0 \vert X=\mathbf{x})\log(P(Y=0\vert X=\mathbf{x};\boldsymbol{\theta})) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y \in \{0,1\}} P_D(Y=y\vert X=\mathbf{x}) \log(P(Y=y \vert X=\mathbf{x}; \boldsymbol{\theta})) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} H(P_D(Y\vert X=\mathbf{x}), P(Y \vert X=\mathbf{x} ; \boldsymbol{\theta}))
\end{align*}
$$

이 경우 $$\widehat{P}(Y \vert X)$$는 우리가 실제로 알고 싶은 확률 $$P(Y \vert X)$$를 데이터셋 $$D$$를 이용하여 추정한 것이다. (이 방법을 몬테카를로라고 하던가...?) 추정 값이 실제 값과 같다고 가정한다면 우리고 풀고자 하는 Optimization 문제는 우리 모델 $$P(Y\vert X ; \boldsymbol{\theta})$$와 실제 확률 $$P(Y\vert X)$$ 사이의 Binary Cross-Entropy를 최소화하는 문제와 동치가 된다.

추가적으로 Cross-Entropy는 KL-Divergence로 다시 쓸 수 있다.

$$
\begin{align*}
H(\widehat{P}(Y\vert X), P(Y \vert X ; \boldsymbol{\theta})) & = H(\widehat{P}(Y \vert X)) + D_{KL}(\widehat{P}(Y \vert X) \Vert P(Y \vert X ; \boldsymbol{\theta}))
\end{align*}
$$

여기서 $$\boldsymbol{\theta}$$를 찾기 위해서 Gradient Descent (Ascent)를 사용하게 됨!

## Gradient Desecent (Ascent)

Gradient Descent란:
- 우리가 어떤 함수 $$f(\mathbf{x})$$를 최적화하기 위해서 사용하는 방법
- 이 함수를 $$\mathbf{x}$$공간 상에서 Unit Vector $$\mathbf{u}$$방향으로 약간 움직여본다고 가정:

$$f(\mathbf{x}) \longrightarrow f(\mathbf{x} + h\mathbf{u})$$

- Taylor Expansion:

$$
\begin{align*}
f(\mathbf{x} + h\mathbf{u}) & = f(\mathbf{x}) + h\nabla_{\mathbf{x}}f(\mathbf{x})^{\text{T}}\mathbf{u} + \frac{h}{2}\mathbf{u}^{\text{T}}\nabla_{\mathbf{x}}^2 f(\mathbf{x}) \mathbf{u} + \cdots \\
& \approx f(\mathbf{x}) + h\nabla_{\mathbf{x}}f(\mathbf{x})^{\text{T}}\mathbf{u}
\end{align*}
$$

- 만약 $$f$$를 최대화하고 싶다면 (Gradient Ascent):

$$
\begin{align*}
\mathbf{u}^* & = \arg \max_{\mathbf{u}} f(\mathbf{x}) + h\nabla_{\mathbf{x}}f(\mathbf{x})^{\text{T}}\mathbf{u} \\
& = \frac{1}{\vert \nabla_{\mathbf{x}}f(\mathbf{x}) \vert} \nabla_{\mathbf{x}}f(\mathbf{x})
\end{align*}
$$

- 최소화인 경우도 마찬가지로 (Gradient Descent):

$$
\begin{align*}
\mathbf{u}^* & = \arg \min_{\mathbf{u}} f(\mathbf{x}) + h\nabla_{\mathbf{x}}f(\mathbf{x})^{\text{T}}\mathbf{u} \\
& = -\frac{1}{\vert \nabla_{\mathbf{x}}f(\mathbf{x}) \vert} \nabla_{\mathbf{x}}f(\mathbf{x})
\end{align*}
$$

## Naive Bayes
Optimal Classifier의 정의: $$f^*(x) = \arg \max_{Y=y}P(Y=y \vert X=x)$$

Naive Bayes Classifier를 구하기 위해서는 위의 식을 분해해야 아래와 같은 Optimization 문제를 풀어야 한다.

$$
\begin{align*}
f^*(x) & = \arg \max_{Y=y}P(Y=y \vert X=x) \\
& = \arg \max_{Y=y} P(X=x \vert Y=y)P(Y=y)
\end{align*}
$$

마찬가지로 Binary Classifier의 예시:
- y는 0 또는 1, x는 벡터

$$
\begin{align*}
f^*(\mathbf{x}) & = \arg \max_{Y=y}P(Y=y \vert X=\mathbf{x}) \\
& = \arg \max_{Y=y} P(X=\mathbf{x} \vert Y=y)P(Y=y)
\end{align*}
$$

이 경우 이 확률을 추정하기 위해서 필요한 데이터셋의 크기를 생각해보자. 만약 $$\mathbf{x}$$의 Dimension이 $$d$$이고, 각각의 Dimension의 Element는 Binary라고 가정하였을 경우 필요한 $$(\mathbf{x}, y)$$ Tuple의 갯수는 $$(2^d-1)k$$가 될 것이다. 이 크기는 너무 크다는 문제가 있으며 이것을 줄이기 위한 다른 추가적인 가정을 필요로 하게 된다.

Naive Bayes의 가정: Conditional Independence
- 다음을 만족할 때 $$X_1$$은 Given $$Y$$에 대해서 $$X_2$$와 Conditional Independent하다고 정의한다.

$$
P(X_1 \vert X_2, Y) = P(X_1 \vert Y)
$$

따라서 Naive Bayes의 Optimization 문제는 아래와 같이 쓸 수 있다.

$$
\begin{align*}
f^*(\mathbf{x}) & = \arg \max_{Y=y} P(X=\mathbf{x} \vert Y=y)P(Y=y) \\
& = \arg \max_{Y=y} P(Y=y) \prod_{i\in \{1,2,\cdots, d\}} P(X_i=x_i \vert Y=y) \\
& \text{where} \ \mathbf{x} = (x_1,x_2,\cdots, x_d)
\end{align*}
$$

이 경우 확률을 추정하기 위한 데이터셋의 크기가 매우 감소하는 것을 확인할 수 있다. $$P(X_i=x_i\vert Y=y)$$를 추정하기 위해 필요한 $$(x_i, y)$$ Tuple는 $$k$$개가 될 것이고 $$x_i$$가 $$d$$개가 필요할 것이기 때문에 총 Tuple의 갯수는 $$dk$$가 될 것이다.

Conditional Independence 가정이 합당한지? (강의 자료 참고)

## Logistic Regression과 Naive Bayes의 관계
두 방법을 비교하기 위해서 Naive Bayes의 가정이 몇 가지 더 추가되게 된다. 먼저 $$X$$는 Continuous하다는 가정과 $$X$$가 주어진 파라미터에 대해서 Gaussian Distribution을 따른다는 가정이다. 즉, $$P(X_i=x_i\vert Y=y, p_{\boldsymbol{\theta}}, \sigma^2)$$를 다음과 같이 쓸 수 있다.

$$P(X_i=x_i\vert Y=y, p_{\boldsymbol{\theta}}, \sigma^2) = \frac{1}{\sigma \sqrt{2 \pi}}e^{-\frac{(x_i-p_{\boldsymbol{\theta}})^2}{2\sigma^2}}$$

추가적으로, $$P(Y=y)$$에 대해서는 다음과 같은 가정을 사용하게 된다.

$$P(Y=y) = \pi_1$$

만약 Binary Classfication 문제라는 가정이 추가된다면 다음과 같이 쓸 수 있을 것이다.

$$P(X_i=x_i\vert Y=1, p_{\boldsymbol{\theta}}_1^i, (\sigma_1^i)^2) = \frac{1}{\sigma_1^i \sqrt{2 \pi}}\exp\left(-\left(\frac{x_i-p_{\boldsymbol{\theta}}_1^i}{2\sigma_1^i}\right)^2\right)$$

$$P(X_i=x_i\vert Y=0, p_{\boldsymbol{\theta}}_2^i, (\sigma_2^i)^2) = \frac{1}{\sigma_2^i \sqrt{2 \pi}}\exp\left(-\left(\frac{x_i-p_{\boldsymbol{\theta}}_2^i}{2\sigma_2^i}\right)^2\right)$$

$$P(Y=1) = \pi_1, \ \ \ P(Y=0) = \pi_2$$

따라서 위의 가정들을 바탕으로 $$P(Y=y \vert X)$$를 다시 쓰면 다음과 같다.

$$
\begin{align*}
P(Y=y \vert X) & = \frac{P(X\vert Y=y)P(Y=y)}{P(X)} \\
& = \frac{P(X\vert Y=y)P(Y=y)}{P(X)}{P(X\vert Y=1)P(Y=1) + P(X\vert Y=0)P(Y=0)} \\
& = \frac{P(Y=y)\prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i \vert Y=y)}{P(Y=1)\prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i \vert Y=1) + P(Y=0)\prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i \vert Y=0)}
\end{align*}
$$

$$
\begin{align*}
P(Y=1\vert X) & = \frac{\pi_1 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=1)}{\pi_1 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=1) + \pi_2 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=0)} \\
& = \frac{1}{1 + \frac{\pi_2 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=0)}{\pi_1 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=1)}} \\
& = \frac{1}{1 + \exp[-(\log\pi_1 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=1) - \log\pi_2 \prod_{i\in \{1,2,\cdots, d\}}P(X_i=x_i\vert Y=0))]} \\
& = \frac{1}{1 + \exp[-(\sum_{i\in \{1,2,\cdots, d \}}\log P(X_i=x_i\vert Y=1) - \sum_{i\in \{1,2,\cdots, d \}}\log P(X_i=x_i \vert Y=0)) + \log \pi_2 - \log \pi_1]}
\end{align*}
$$





