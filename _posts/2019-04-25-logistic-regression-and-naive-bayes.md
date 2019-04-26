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
일반적으로 Classification 문제는 샘플 공간 $$\mathcal{X}$$ 상에 있는 샘플 $$\mathbf{x}_1, \mathbf{x}_2, \cdots \in \mathcal{X}$$ 각각에 대해서 이 샘플은 어떤 클래스 $$y \in \mathcal{Y}$$로 분류할 것인가에 관한 문제로 생각할 수 있을 것이다. 이러한 문제 세팅에서 자연스럽게 우리는 Classifier $$f$$를 정의할 수 있다. Classifier $$f:\mathcal{X} \longmapsto \mathcal{Y}$$는 주어진 샘플 공간으로부터 클래스 공간으로 Mapping해주는 함수라고 정의할 수 있을 것이다. 예를 들면 다음과 같다. 여러 음식들($$\mathcal{X}$$)을 과일이냐 아니냐($$\mathcal{Y}=\{0, 1 \}$$)로 분류하는 Classifier $$f$$를 예로 들어보자. $$f$$는 다음과 같이 동작할 것이다.

$$
f(\text{Apple}) = 1, \ \ \ f(\text{Banna}) = 1, \ \ \ f(\text{Steak}) = 0, \cdots
$$

물론 어떤 Classifier $$g$$는 다음과 같이 동작할 수도 있을 것이다.

$$
g(\text{Apple}) = 1, \ \ \ g(\text{Banna}) = 0, \ \ \ g(\text{Steak}) = 0, \cdots
$$

이 경우 $$f$$는 바나나에 대해서는 정확히 분류해냈지만 $$g$$는 그렇지 못한 셈이 되었다. 우리는 바나나가 과일이 맞다는 것을 알 수 있으므로 $$g$$ 보다는 $$f$$가 더 성능이 좋다고 판단할 수 있다.

위와 같은 예시를 바탕으로 Optimal Classifier를 정의할 수 있다. 일단 Optimal Classifier의 정의는 다음과 같다.

$$f^*(\mathbf{x}) = \arg \max_{Y=y}P(Y=y \vert X=\mathbf{x})$$

위의 과일 예시로 다시 설명을 해보자. 실제 자연계의 모델은 주어진 음식 $$\mathbf{x}$$에 대해서 과일인지 아닌지에 대한 분포 $$P(Y\vert X=\mathbf{x})$$를 모델링하고 있다고 가정할 수 있다. (물론 이 가정은 틀렸을 수도 있다.) 이러한 분포를 최대로 만족하는 Classifier $$f$$가 바로 Optimal Classifier가 될 것이다. 이러한 Optimal Classifier를 찾기 위해서는 다음과 같은 Optimization 문제를 풀어야 할 것이다.

$$
f^* = \arg \max_f P(Y=f(\mathbf{x}) \vert X=\mathbf{x}), \ \forall \mathbf{x} \in \mathcal{X}
$$

Machine Learning의 방법론은 이러한 Optimal Classifier를 찾기 위한 모델링을 크게 2가지로 분류하고 있다. Discriminative Model과 Generative Model이다.

## Discriminative Model 및 Generative Model

Discriminative Model은 $$P(Y\vert X)$$에 대한 가정을 추가하여 $$P(Y\vert X)$$를 직접 모델링을 하게 되며 이렇게 찾아낸 $$P(Y\vert X)$$를 이용하여 Decision Boundary를 찾는 것이이다. Discriminative Model의 대표적인 방법은 Linear Regression 및 Logistic Regression이 있다.

Generative Model은 Bayes Theorem을 이용하여 $$P(Y\vert X)$$를 $$P(X \vert Y)P(Y)$$로 분해하여 $$P(X\vert Y)$$와 $$P(Y)$$에 대한 가정을 추가하여 간접적으로 $$P(Y\vert X)$$를 모델링하여 Decision Boundary를 찾게 된다. Generative Model의 대표적인 방법은 Naive Bayes Classification이 있다.

일반적으로 확률 모델 $$P(X\vert Y)$$나 $$P(Y)$$에 대한 좋은 가정을 할 수 있다면 적은 데이터로도 충분히 좋은 성능을 찾아낼 수 있다는 것이 Generative Model의 장점이다. 반면 Discriminative Model은 데이터가 충분히 많다면 훨씬 더 좋은 성능을 낼 수 있다.. 이러한 관점으로 봤을 때 Discriminative Model은 MLE(Maximum Likelihood Estimation)와 같다고 볼 수 있으며 Generative Model은 MAP(Maximum A Posteriori)와 같다고도 볼 수 있다.

## Logistic Regression
Logistic Regression은 위에서 설명하였듯이 Discriminative Model의 한 종류이다. 따라서 $$P(Y\vert X)$$에 대해서 가정을 하여 $$P(Y\vert X;\boldsymbol{\theta})$$로 모델링을 하게 된다. 문제를 좀 더 간단하게 표현하기 위해서 클래스 공간 $$\mathcal{Y}$$는 Binary라는 가정을 추가하자. 물론 Multi 클래스에 대해서 간단한 방법으로 확장이 가능하다. $$P(Y\vert X)$$가 Binary 랜덤 변수에 대한 분포를 나타내므로 Bernoulli 분포로 가정을 하는 것이 자연스러울 것이다.

$$
\begin{align*}
P(Y=y \vert X=\mathbf{x};\boldsymbol{\theta})
& = p_{\boldsymbol{\theta}}(\mathbf{x})^y(1−p_{\boldsymbol{\theta}}(\mathbf{x}))^{1−y} \\
p_{\boldsymbol{\theta}}(\mathbf{x})
& = P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})
\end{align*}
$$

여기서 $$p_{\boldsymbol{\theta}}(\mathbf{x})$$를 Logistic Function으로 가정하는 것이 Logistic Regression이다. 대표적인 Logistic Function들은 다음과 같다.

![](/assets/img/2019-04-25-logistic-regression-and-naive-bayes/01.png)

(출처: [https://aailab.kaist.ac.kr/xe2/page_GBex27](https://aailab.kaist.ac.kr/xe2/page_GBex27))

여기서는 가장 대표적인 Logistic Function인 Sigmoid를 사용하기로 하자. Sigmoid를 사용하여 $$p_{\boldsymbol{\theta}}(\mathbf{x})$$를 다음과 같이 정의할 수 있다.

$$
p_{\boldsymbol{\theta}}(\mathbf{x}) = \frac{1}{1+e^{−\boldsymbol{\theta}^{\text{T}}\mathbf{x}}}
$$

이렇게 모델링이 된 $$P(Y\vert X;\boldsymbol{\theta})$$를 주어진 데이터셋 $$D$$를 이용하여 Fitting시키는 문제를 Logistic Regression이라고 한다.

Logistic Regression을 풀기 위해서는 Optimal Classifier를 찾는 Optimization 문제로부터 시작하는 것이 좋다.

$$
\begin{align*}
f^*(\mathbf{x})
& = \arg \max_{Y=y} P(Y=y \vert X=\mathbf{x})
\end{align*}
$$

여기서 우리는 실제 $$P(Y=y \vert X=\mathbf{x})$$를 알 수 없으므로 우리가 가정한 모델 $$P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})$$를 대신 사용하여 Optimal Classifier를 정의하게 된다.

$$
\begin{align*}
f^*(\mathbf{x})
& = \arg \max_{Y=y} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

우리는 이제 이 Optimization 문제로부터 실제 $$P(Y=y\vert X=\mathbf{x})$$를 모델링할 수 있는 최적의 파라미터 $$\boldsymbol{\theta}$$를 찾고자 한다. 이 경우 우리에게 주어진 데이터셋 $$D$$를 활용하게 된다. 즉, 데이터셋 $$D$$를 가장 잘 설명할 수 있는 파라미터 $$\boldsymbol{\theta}$$를 찾는 MLE 방법을 사용하게 된다. 일단 먼저 MLE의 형식대로 Optimization 문제를 다시 써보면 다음과 같다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} P(D ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in (\mathcal{X}, \mathcal{Y})} P(Y=y, X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in (\mathcal{X}, \mathcal{Y})} P(X=\mathbf{x}) P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

여기서 우리가 가정한 모델 $$P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})$$를 사용하여 Joint Distribution $$P(Y=y, X=\mathbf{x} ; \boldsymbol{\theta})$$를 $$P(X=\mathbf{x}) P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})$$로 분해하였다. $$P(X=\mathbf{x})$$는 $$\mathbf{x}$$의 값이 Optimization 문제에 영향을 주지 않기 때문에 상수로 취급하여 제거할 수 있다.

따라서 Optimization 문제는 다음과 같이 정리될 수 있다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \prod_{(\mathbf{x}, y) \in (\mathcal{X}, \mathcal{Y})} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \left[ y\log (p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right]
\end{align*}
$$

특히 마지막 식은 이 Optimization 문제가 Binary Cross-Entropy를 최소화하는 문제로 바꿀 수 있음을 확인할 수 있을 것이다. 아래와 같이 식을 수정해보자.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \left[ y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y\sim P_D(y\vert \mathbf{x})} \left[ y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ N_D(\mathbf{x})P_D(Y=1 \vert X=\mathbf{x})\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + N_D(\mathbf{x})P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right]
\end{align*}
$$

여기서 $$N_D(\mathbf{x})$$는 주어진 $$\mathbf{x}$$에 대한 데이터셋 내부의 샘플 갯수이며, $$P_D$$는 주어진 확률 변수에 대한 데이터셋 내부의 분포이다. 만약 데이터셋의 분포가 균일하여 $$N_D(\mathbf{x})$$가 모든 샘플 데이터 $$\mathbf{x}$$에 대해서 일정하다면 상수로 취급되어 Optimization 문제에서 생략될 것이다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ P_D(Y=1 \vert X=\mathbf{x}) \log(p_{\boldsymbol{\theta}}(\mathbf{x})) + P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ P_D(Y=1\vert X=\mathbf{x})\log(P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})) + P_D(Y=0 \vert X=\mathbf{x})\log(P(Y=0\vert X=\mathbf{x};\boldsymbol{\theta})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y \in \{0,1\}} P_D(Y=y\vert X=\mathbf{x}) \log(P(Y=y \vert X=\mathbf{x}; \boldsymbol{\theta})) \\
& = \arg \min_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} H(P_D(Y\vert X=\mathbf{x}), P(Y \vert X=\mathbf{x} ; \boldsymbol{\theta}))
\end{align*}
$$

Binary Classifier를 학습시키기 위하여 사용하는 Optimization 문제는 데이터셋 $$D$$의 모든 $$\mathbf{x}$$에 대하여 데이터셋 $$D$$로부터 찾아낸 분포 $$P_D(Y\vert X=\mathbf{x})$$와 우리 모델 $$P(Y\vert X=\mathbf{x} ; \boldsymbol{\theta})$$ 사이의 Binary Cross-Entropy를 최소화하는 문제와 동치가 된다. 여기서 Cross-Entropy를 KL-Divergence로 다시 쓰면 아래와 같다.

$$
\begin{align*}
H(P_D(Y\vert X=\mathbf{x}), P(Y \vert X=\mathbf{x} ; \boldsymbol{\theta})) & = H(P_D(Y \vert X=\mathbf{x})) + D_{KL}(P_D(Y \vert X=\mathbf{x}) \Vert P(Y \vert X=\mathbf{x} ; \boldsymbol{\theta}))
\end{align*}
$$

즉, 우리가 찾고자 하는 최적의 파라미터 $$\boldsymbol{\theta}^*$$는 데이터셋 $$D$$의 모든 $$\mathbf{x}$$에 대하여 $$P_D(Y\vert X=\mathbf{x})$$에 $$P(Y\vert X=\mathbf{x} ; \boldsymbol{\theta})$$를 확률적으로 Fitting하는 $$\boldsymbol{\theta}$$인 것을 확인할 수 있다.

이러한 Optimization 문제는 Closed Form으로 풀어내기 쉽지 않으며 따라서 Iterative한 방법으로 접근을 하게 된다. 가장 대표적인 Iterative한 방법은 바로 Gradient Descent(Ascent)이다.

## Gradient Desecent(Ascent)
Gradient Descent 또는 Gradient Ascent란 어떤 함수 $$f$$를 최적화하기 위해서 사용하는 Iterative한 알고리즘이다. 이 알고리즘의 원리를 파악하기 위해서는 먼저 다음의 개념을 살펴볼 필요가 있다.

어떤 함수 $$f$$가 $$\mathbf{x}$$가 살고있는 공간 $$\mathcal{X}$$에 대하여 정의되어 있다고 가정하자. ($$f:\mathcal{X}\longmapsto \mathcal{Y}$$) 이 경우에 공간 $$\mathcal{X}$$에서 $$f$$를 최대화하는 어떤 $$\mathbf{x}^*$$를 찾을 수 있을 것이라고 가정하자. 즉, 함수 $$f$$는 공간 $$\mathcal{X}$$ 상에 유한한 함수값을 가지는 함수로 잘 정의가 되어있는 것이라고 가정한다. 임의의 $$\mathbf{x}\in \mathcal{X}$$에 대해서 $$\mathbf{x}^*$$의 방향으로 가기 위해서는 어떤 방향으로 $$\mathbf{x}$$를 움직일 수 있을 것일지에 대한 문제로 생각할 수 있을 것이다. 그리고 이 방향은 $$f$$를 최대한 증가시키는 방향이 될 것이다.

즉, 정리하면 임의의 $$\mathbf{x}_t\in \mathcal{X}$$에 대해서 $$f$$를 최대한 증가시키는 방향인 $$\mathbf{u}_t$$를 찾을 수 있고($$\vert \mathbf{u}_t \vert = 1$$라는 가정이 있음), 매번 $$\mathbf{x}_t$$를 아래와 같은 업데이트 룰을 통해서 Iterative하게 업데이트시키게 된다면 결국 $$\mathbf{x}^*$$로 점점 다가가게 될 것이라고 생각할 수 있다.

$$\mathbf{x}_{t+1} \longleftarrow \mathbf{x}_t + \alpha \mathbf{u}_t$$

여기서 $$\alpha \in \mathbb{R}$$는 임의의 양의 실수가 될 것이다. 이 경우 Local Extrema에 빠질 가능성도 물론 있지만 여기서는 함수 $$f$$가 Concave하다고 가정을 하도록 하자. 그 다음 문제는 임의의 $$\mathbf{x}_t\in \mathcal{X}$$에 대해서 $$\mathbf{u}_t$$를 찾을 수 있는지가 될 것이다. 이 문제를 풀기 위해서는 $$f(\mathbf{x}_t + \alpha \mathbf{u})$$를 Taylor Expansion으로 풀어서 써놓는 것에서 시작한다.

$$
\begin{align*}
f(\mathbf{x}_t + \alpha \mathbf{u})
& = f(\mathbf{x}_t) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u} + \frac{\alpha}{2}\mathbf{u}^{\text{T}}\nabla_{\mathbf{x}}^2 f(\mathbf{x}_t) \mathbf{u} + \cdots \\
& \approx f(\mathbf{x}) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u}
\end{align*}
$$

여기서 우리는 $$\mathbf{u}_t$$가 $$f(\mathbf{x}_t+\alpha \mathbf{u})$$를 최대화하는 Unit 벡터 $$\mathbf{u}$$가 되기를 원한다.

$$
\begin{align*}
\mathbf{u}_t
& = \arg \max_{\mathbf{u}} f(\mathbf{x}_t) + \alpha \nabla_{\mathbf{x}}f(\mathbf{x}_t)^{\text{T}}\mathbf{u} \\
& = \frac{1}{\vert \nabla_{\mathbf{x}}f(\mathbf{x}_t) \vert} \nabla_{\mathbf{x}}f(\mathbf{x}_t)
\end{align*}
$$

비슷한 방법으로 $$f$$를 최소화하기 위한 업데이트 룰도 다음과 같이 쓸 수 있다.

$$\mathbf{x}_{t+1} \longleftarrow \mathbf{x}_t - \alpha \mathbf{u}_t, \ \text{where}, \ \alpha \in \mathbb{R}, \alpha >0$$

즉 위와 같이 Gradient를 이용하여 함수 $$f$$를 Iterative하게 업데이트하는 방법을 Gradient Descent 또는 Gradient Ascent라고 한다. $$f$$를 최대화시키는 알고리즘을 Gradient Ascent, $$f$$를 최소화시키는 알고리즘을 Gradient Descent라고 한다.

## Naive Bayes
Naive Bayes 방법은 위에서 설명한대로 Generative Model의 한 종류이다. 따라서 $$P(Y\vert X)$$를 직접 가정하여 모델링하는 대신 $$P(X\vert Y)$$ 및 $$P(Y)$$에 대한 가정이 필요하게 된다. 위에서 Logistic Regression을 설명할 때 사용한 문제 세팅을 그대로 가져와서 Optimization 문제를 다시 써보면 다음과 같이 쓸 수 있다.

$$
\begin{align*}
f^*(\mathbf{x})
& = \arg \max_{Y=y} P(Y=y \vert X=\mathbf{x})
\end{align*}
$$

Generative Model은 위에서 설명한대로 아래와 같이 Optimization 문제를 분해하여 생각하게 된다.

$$
\begin{align*}
f^*(\mathbf{x})
& = \arg \max_{Y=y} P(X=\mathbf{x} \vert Y=y)P(Y=y)
\end{align*}
$$

여기서 Generative Model은 다양한 방법으로 $$P(X=\mathbf{x} \vert Y=y)$$을 모델링하게 되는데 여기서는 한 가지 문제가 발생하게 된다. 바로 이 확률을 추정하기 위해서 필요한 데이터셋의 크기이다. 만약 $$\mathbf{x}$$의 Dimension이 $$d$$이고, 각각의 Dimension의 Element는 Binary라고 가정하였을 경우 $$P(X=\mathbf{x} \vert Y=y)$$를 추정하기 위해서 필요한 $$(\mathbf{x}, y)$$ Tuple의 갯수는 $$(2^d-1)k$$가 될 것이다. 이 크기는 너무 크다는 문제가 있으며 이것을 줄이기 위한 다른 추가적인 가정을 필요로 하게 된다.

여기서 Naive Bayes는 한 가지 가정을 추가하여 이 문제를 해결하려고 시도하게 된다. 바로 Conditional Independence 가정이다. Conditional Independence는 다음과 같이 정의된다. 확률 변수 $$X_1, X_2, Y$$에 대해서 각각이 다음을 만족할 때 $$X_1$$은 Given $$Y$$에 대해서 $$X_2$$와 Conditional Independent하다고 정의한다.

$$
P(X_1 \vert X_2, Y) = P(X_1 \vert Y)
$$

따라서 이러한 Conditional Independence 가정을 통해서 Naive Bayes의 Optimization 문제는 아래와 같이 쓸 수 있다.

$$
\begin{align*}
f^*(\mathbf{x})
& = \arg \max_{Y=y} P(X=\mathbf{x} \vert Y=y)P(Y=y) \\
& = \arg \max_{Y=y} P(Y=y) \prod_{i\in \{1,2,\cdots, d\}} P(X^{(i)}=x_^{(i)} \vert Y=y) \\
& \text{where} \ X=(X^{(1)}, X^{(2)}, \cdots, X^{(d)}), \ \mathbf{x} = (x^{(1)}, x^{(2)}, \cdots, x^{(d)})
\end{align*}
$$

이 경우 확률을 추정하기 위한 데이터셋의 크기가 매우 감소하는 것을 확인할 수 있다. $$P(X_i=x_i\vert Y=y)$$를 추정하기 위해 필요한 $$(x_i, y)$$ Tuple는 $$k$$개가 될 것이고 $$x_i$$가 $$d$$개가 필요할 것이기 때문에 총 Tuple의 갯수는 $$dk$$가 될 것이다.

Conditional Independence 가정이 합당한지에 대해서는 Graphical Model에 대한 공부가 더 필요하다. 나중에 공부하여 정리하도록 하겠다.

어쨌든 Naive Bayes 방법을 정리하면, 일반적인 Generative Model처럼 $$P(X\vert Y)$$에 대해서 가정을 통하여 모델링을 시도하게 되지만 여기서 사용하는 가정이 Conditional Independence 가정이 사용된다면 그 방법은 Naive Bayes 방법을 사용한다고 말할 수 있겠다.

## Logistic Regression과 Naive Bayes의 관계
두 방법을 비교하기 위해서 Naive Bayes의 가정이 몇 가지 더 추가되게 된다. 일단 기본적으로 $$P(X\vert Y)$$가 기본적으로 $$X$$의 모든 Dimension Element에 대해서 Conditional Independence라는 가정에 추가하여 특정 파라미터 $$\boldsymbol{\theta}$$로 Parameterization하였다고 가정한다.

먼저 $$X$$는 Continuous하다는 가정과 $$X$$가 주어진 파라미터에 대해서 Gaussian Distribution을 따른다는 가정이다. 즉, $$P(X_i=x_i\vert Y=y, p_{\boldsymbol{\theta}}, \sigma^2)$$를 다음과 같이 쓸 수 있다.

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





