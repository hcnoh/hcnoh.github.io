---
layout: post
use_math: true
title: "[Machine Learning] 1. Probability"
date: 2022-06-25 00:00:00
tagline: "머신 러닝의 기본 토대가 되는 확률 및 통계 개념들에 대해서 간단히 정리"
categories:
- Machine Learning Study
tags:
- probability
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2022-06-25-machine-learning-01
---

머신 러닝에 대해서 스터디를 진행하였고, 내용을 정리하여 연재를 진행하려고 한다. 주된 참고 자료로는 케빈 머피의 저서인 [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)을 활용하였다. 앞으로 꾸준히 스터디 자료를 업로드할 계획이다.

## 확률이란?
확률(Probability)는 두 가지 관점으로 해석할 수 있다. 먼저 첫 번째는 Frequentist Interpretation이다. `Frequentist Interpretation`은 확률이 장기적으로 사건들의 발생 빈도를 나타낸다는 관점이다. 예를 들면, 만약 우리가 동전을 매우 많이 던진다면 우린 그 동전이 약 절반의 빈도수를 가지며 앞면을 나타낼 것이라고 기대할 수 있다.

그 다음으로는 `Bayesian Interpretation`이다. Bayesian Interpretation에서는 확률이 무언가에 대한 우리의 불확실성을 정량화하는데 활용된다고 여기게 된다. 즉, 확률은 근본적으로 반복되는 시도와 관계있다기 보다는 사건에 정보와 관련되어 있다고 생각하는 관점이다. 예를 들면, 우리가 동전을 던지는 경우 다음 시도에서 앞면과 뒷면이 같은 가능성을 가진다고 믿을 수 있다. 이러한 믿음을 나타내는 것이 확률이라는 관점이 바로 Bayesian Interpretation이다.

확률을 표현하는 방식은 다음과 같이 정리할 수 있다:
- $$p(A)$$: 사건 $$A$$가 참일 확률
    - 예시: "내일 비가 올 것이다."와 같은 형식의 Logical Expression은 사건 $$A$$의 예시가 될 수 있다.
- $$p(\bar{A})$$: 사건 $$A$$가 거짓일 확률
    - $$p(\bar{A}) = 1 - p(A)$$.
- 앞으로는 이진 사건 (Binary Event)의 형식으로 사건을 다룰 예정이며, $$A=1$$은 $$A$$가 참, $$A=0$$는 $$A$$가 거짓을 나타낸다.

## 이산 확률 변수
이진 사건은 이산 확률 변수 $$X$$를 정의함으로 그 표현이 확장될 수 있다. 이산 확률 변수 $$X$$는 유한한, 또는 셀수 있는 무한한 집합 $$\mathcal{X}$$로부터 그 값을 가질 수 있다. 여기서 $$\mathcal{X}$$는 `상태 공간` (State Space)라고 한다.

사건 $$X=x$$의 확률을 $$p(X=x)$$, 또는 간단히 $$p(x)$$라고 나타낸다. 여기서 $$p()$$는 `확률 질량 함수` (Probability Mass Function, PMF)라고 부른다. PMF는 다음과 같은 성질을 갖고 있다:
- $$0 \leq p(x) \leq 1$$.
- $$\sum_{x \in \mathcal{X}} p(x) = 1$$.

## 다양한 확률 연산
사건 $$A$$, $$B$$에 대해서 사건 $$A$$가 참이거나 사건 $$B$$가 참인 사건을 논리식의 형식으로 $$A \lor B$$로 나타낼 수 있다. 이를 사건 $$A$$와 $$B$$의 Union Event라고 한다. 사건 $$A$$와 $$B$$의 Union Event $$A \lor B$$가 참일 확률 $$p(A \lor B)$$는 각 사건이 참일 확률 $$p(A)$$, $$p(B)$$를 이용하여 다음과 같이 연산이 가능하다:

$$
p(A \lor B) = p(A) + p(B) - p(A \land B).
$$

여기서 $$A \land B$$는 사건 $$A$$와 $$B$$의 Joint Event이며 사건 $$A$$와 사건 $$B$$가 둘다 참인 사건을 말한다. 만약 $$A$$와 $$B$$가 둘 다 참인 경우가 존재하지 않는 경우, 즉 두 사건이 `상호 배타적`(Mutually Exclusive)인 경우는 다음과 같이 $$p(A \lor B)$$를 계산할 수 있다:

$$
p(A \lor B) = p(A) + p(B).
$$

위에서 등장한 Joint Event $$A \land B$$의 확률 $$p(A, B) = p(A \land B)$$는 `결합 확률`(Joint Probability)이라고 정의한다. 조건부 확률(Conditional Probability) $$p(A \vert B)$$를 통해서 다음과 같이 쓸 수 있다:

$$
p(A, B) = p(A \land B) = p(A \vert B) p(B).
$$

이러한 계산 방식을 확률의 `곱셈 규칙`(Product Rule)이라고 한다.

방금 언급한 조건부 확률의 경우는 다음과 같이 정의한다. `조건부 확률` $$p(A \vert B)$$는 사건 $$B$$가 참인 것을 이미 관찰한 상황에서의 사건 $$A$$가 참인 상황에 대한 확률로 정의한다. 이는 다음과 같이 계산이 가능하다:

$$
p(A \vert B) = \frac{p(A, B)}{p(B)}.
$$

물론 여기에는 $$p(B)$$는 $$0$$이 아니어야 된다는 조건이 추가된다.

위에서 정의한 결합 확률, 조건부 확률을 바탕으로 유명한 정리인 `Bayes Rule`을 확인할 수 있다:

$$
p(A \vert B) = \frac{p(B \vert A) p(A)}{p(B)}.
$$

추가적으로 `주변 분포`(Marginal Distribution)는 다음과 같이 정의한다:

$$
p(A) = \sum_b p(A, B) = \sum_b p(A \vert B=b)p(B = b).
$$

이 정의는 또한 확률의 `덧셈 법칙`, 또는 `전체 확률의 법칙`(Law of Total Probability)이라고 부른다.

확률의 덧셈 법칙을 확장하여 확률의 `연쇄 법칙`을 확인할 수 있다:

$$
\begin{array}{rl}
p(X_{1:D}) & = p(X_D \vert X_{1:D-1})p(X_{D-1} \vert X_{1:D-2}) \cdots p(X_2 \vert X_1) p(X_1) \\
& = \prod_{i=1}^D p(X_i \vert X_{1:i-1}).
\end{array}
$$

## Generative Classifier
분류 문제는 주어진 Feature Vector $$\mathbf{x}$$가 어떤 분류 $$c$$로 분류될지를 찾는 문제를 말한다. 분류 문제를 논하기 위해서는 주어진 분류 $$y=c$$에 대해서 Feature Vector $$\mathbf{x}$$에 대한 확률 밀도 $$p(\mathbf{x} \vert y=c)$$를 고려하게 된다. 이를 `Class-conditional Density`라고 정의한다.

Class-conditional Density $$p(\mathbf{x} \vert y=c)$$를 살펴보면 이 밀도 함수를 통해 분류 $$c$$가 주어지면 Class-conditional Density를 Sampling Distribusion으로 활용하여 분류 $$c$$에 해당하는 Feature Vector들을 생성해 낼 수 있다는 점을 알 수 있다.

여기서 Bayes Rule 및 Class-conditional Density를 활용하여 `Generative Classifier`를 정의할 수 있다:

$$
p(y=c \vert \mathbf{x} ; \boldsymbol{\theta}) = \frac{p(y=c ; \boldsymbol{\theta}) p(\mathbf{x} \vert y=c ; \boldsymbol{\theta})}{\sum_{c'} p(y=c' ; \boldsymbol{\theta}) p(\mathbf{x} \vert y=c' ; \boldsymbol{\theta})}.
$$

여기서 $$\boldsymbol{\theta}$$는 분류기를 매개변수화하는 매개변수 벡터이다. 이 분류기는 주어진 Feature Vector에 대해서 특정 분류 $$c$$에 속할 확률에 대한 확률 질량 함수를 제시한다.

이 분류기가 Generative Classifier라고 불리는 이유는 Class-conditional Density 및 Class Prior $$p(y=c)$$를 활용하여 데이터를 생성하는 방법을 기술하기 때문이다. 즉, 데이터가 어떻게 생성될지를 고려하여 데이터의 분류를 수행한다.

Generative Classifier를 활용하는 경우에서 가장 키포인트는 각 분류 기준에서 우리가 기대하는 데이터가 어떤 종류일지를 정의해주는 Class-conditional Density의 형태를 잘 찾는 것이 중요하다는 점이다.

## 사건의 독립
만약 어떤 사건 $$X, Y$$의 결합 분포가 다음을 만족한다면:

$$
p(X, Y) = p(X)p(Y),
$$

두 사건은 `독립 사건`이라고 하고 $$X \perp Y$$라고 표현한다.

또한 조건부 독립도 정의할 수 있다. 특정 사건 $$Z$$가 관찰된 경우에는 $$X, Y$$가 서로 독립인 경우를 `조건부 독립`이라고 정의한다. 즉 다음을 만족하는 경우를 조건부 독립이라고 한다:

$$
p(X, Y \vert Z) = p(X \vert Z) p(Y \vert Z)
$$

조건부 독립은 $$X \perp Y \vert Z$$로 표현한다.

조건부 독립에서는 다음과 같은 중요한 정리를 확인할 수 있다. 만약 $$X \perp Y \vert Z$$이면 $$p(z) > 0$$를 만족하는 모든 $$x, y, z$$에 대해서 $$p(x, y \vert z) = g(x, z)h(y, z)$$를 성립하는 함수 $$g, h$$가 존재하며, 이 명제의 역도 성립한다.

이를 증명해보자. 먼저 $$\Longrightarrow$$ 방향을 증명한다. $$X \perp Y \vert Z$$인 경우 $$p(x, y \vert z) = p(x \vert z) p(y \vert z)$$이므로 우리는 $$g(x, z)=p(x \vert z)$$, $$h(y, z) = p(y \vert z)$$로 선택할 수 있다.

그 다음으로 $$\Longleftarrow$$ 방향을 증명해보자.

$$
p(x, y \vert z) = \frac{p(x, y, z)}{p(z)} = g(x, z)h(y, z)
$$

라고 할 수 있다. 이 때 WLOG 모든 $$x, y$$에 대해서 $$g(x, z) \geq 0, h(y, z) \geq 0$$라고 가정하자. 그리고 추가적으로:

$$
\sum_x g(x, z) = \tilde{g}(z), \ \ \ \ \sum_y h(y, z) = \tilde(h)(z)
$$

라고 정의하자. 그러면 우리는 다음과 같이 선택할 수 있다:

$$
p(x \vert z) = \frac{g(x, z)}{\tilde{g}(z)}, \ \ \ \ p(y \vert z) = \frac{h(y, z)}{\tilde{h}(z)}.
$$

이는 다음의 관계들이 만족하기 때문에 가능한 선택이다:

$$
\sum_x \frac{g(x, z)}{\tilde{g}(z)} = 1, \ \ \ \ \sum_y \frac{h(y, z)}{\tilde{h}(z)} = 1, \\ 0 \leq \frac{g(x, z)}{\tilde{g}(z)}, \frac{h(y, z)}{\tilde{h}(z)} \leq 1.
$$

또한 추가로 아래의 관계를 만족한다:

$$
\begin{array}{rl}
\sum_x p(x, y \vert z) & = p(y \vert z) = \frac{h(y, z)}{\tilde{h}(z)} \\
& = \sum_x g(x, z) h(y, z) \\
& = \tilde{g}(z) h(y, z) \\
& \Longrightarrow \tilde{g}(z)\tilde{h}(z) = 1.
\end{array}
$$

따라서:

$$
\begin{array}{rl}
p(x, y \vert z)
& = g(x, z)h(y, z) \\
& = \tilde{g}(z)\tilde{h}(z) p(x \vert z) p(y \vert z) \\
& = p(x \vert z) p(y \vert z). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

## 연속 확률 변수
어떤 변수 $$X$$가 불분명한 연속적인 크기를 가진다고 가정하자. 이 때 $$X$$가 어떤 구간 $$a \leq X \leq b$$에 속할 확률은 다음과 같이 계산할 수 있다:
- 사건 $$A$$를 $$A=(X \leq a)$$, 사건 $$B$$를 $$B=(X \leq b)$$, 사건 $$W$$를 $$W=(a < X \leq b)$$라고 정의하자.
- 그렇다면, $$B = A \lor B$$인 것을 확인할 수 있다.
- 만약 $$A$$와 $$W$$가 상호 배타적이라면:

$$
p(B) = p(A) + p(W) \ \Longrightarrow p(W) = p(B) - p(A).
$$

여기서 함수 $$F(q)$$를 $$F(q) \triangleq p(X \leq q)$$로 정의하자. 이 함수를 $$X$$의 `누적 분포 함수`(Cumulative Distribution Function, CDF)라고 한다. CDF를 활용하면 $$p(a < X \leq b>)$$를 다음과 같이 쓸 수 있다:

$$
p(a < X \leq b) = F(b) - F(a).
$$

추가적으로 CDF $$F$$가 `단조 증가 함수`(Monotonically Increasing Function)인 것에 주의하자. 이에 따라 $$F^{-1}$$이 존재하는 것도 추가적으로 확인할 수 있다.

만약 CDF $$F$$가 존재한다면 다음과 같은 함수를 정의할 수 있다:

$$
f(x) = \frac{d}{dx}F(x).
$$

이 함수를 `확률 밀도 함수`(Probability Density Function, PDF)라고 부른다. PDF를 통해서 $$p(a < X \leq b)$$를 다음과 같이 쓸 수 있다:

$$
p(a < X \leq b) = \int_a^b f(x) dx.
$$

## 평균, 분산, 공분산, 상관 계수
일반적으로 `평균`으로 알려진 물리량은 여러 가지가 있다. 첫 번째로 `Mean`이 있다. Mean은 `표본 평균`(Sample Mean)의 줄임말이며 이에 따라 표본에 종속되는 물리량이다. 확률 변수 $$X$$의 관찰값들을 추출하여 구성한 표본 $$\{x_1, x_2, \cdots, x_N\}$$에 대한 표본 평균은 다음으로 정의된다:

$$
\overline{x} = \frac{1}{N}\sum_{i=1}^Nx_i.
$$

표본 평균은 표본에 대한 `산술 평균`(Arithmetic Mean)이라고도 부른다. 이는 또한 `Average`라고 부르기도 한다.

그 다음으로는 `기대값`(Expectation)이다. 이산 확률 변수 $$X$$의 PMF를 $$p(x)$$라고 하면 $$X$$의 기대값은 다음과 같다:

$$
\mathbb{E}\left[ X \right] = \mu_X \triangleq \sum_{x\in \mathcal{X}}x p(x).
$$

만약 $$X$$가 연속 확률 변수이고 PDF $$p(x)$$라고 한다면 기대값은 다음과 같다:

$$
\mathbb{E}\left[ X \right] = \mu_X \triangleq \int_{x\in \mathcal{X}}x p(x) dx.
$$

기대값은 `모 평균`(Population Mean)이라고 부르기도 하며, 확률 변수에 대한 관찰 기대값이라고 생각할 수 있다. 또한 표본의 크기가 무한히 증가한다면 표본 평균은 점차 기대값으로 수렴한다.

앞으로 분산, 공분산, 상관관계의 정의는 모두 연속 확률 변수인 경우에 대해서만 하도록 한다. 이산 확률 변수의 경우에는 적분 연산 대신 합 연산으로 대체하면 된다.

`분산`(Variance)은 확률 변수의 분포가 기대값으로부터 얼마나 멀리 퍼져 있는지에 대한 물리량이다. 확률 변수 $$X$$의 분산은 $$X$$와 그 기대값 $$\mu_X$$ 사이의 차이의 제곱 $$(X - \mu_X)^2$$의 기대값으로 정의한다:

$$
\text{Var}\left[ X \right] = \sigma_X \triangleq \mathbb{E}\left[ (X - \mu_X)^2 \right].
$$

추가적으로 분산의 계산은 다음과 같은 변형이 가능하다:

$$
\begin{array}{rl}
\sigma_X & = \mathbb{E}\left[ (X - \mu_X)^2 \right] \\
& = \mathbb{E}\left[ X^2 - 2\mu_X X + \mu_X^2 \right] \\
& = \mathbb{E}\left[ X^2 \right] - 2\mu_X \mathbb{E}\left[ X \right] + \mu_X^2 \\
& = \mathbb{E}\left[ X^2 \right] - 2\mu_X^2 + \mu_X^2 \\
& = \mathbb{E}\left[ X^2 \right] - \mu_X^2.
\end{array}
$$

`공분산`(Covariance)이란 2개의 확률 변수 사이의 선형적인 관계를 측정하는 물리량이다. 두 확률 변수 $$X, Y$$ 사이의 공분산은 다음과 같이 정의한다:

$$
\begin{array}{rl}
\text{Cov}\left[ X, Y \right] = \sigma_{X, Y} & \triangleq \mathbb{E}\left[ (X - \mu_X) (Y - \mu_Y) \right] \\
& = \mathbb{E}\left[ XY \right] - \mu_X \mu_Y.
\end{array}
$$

또한 이를 통해 주어진 $$d$$차원 `확률 벡터`(Random Vector) $$\mathbf{x} = \left[ X_1, \cdots, X_d \right]$$의 `공분산 행렬`(Covariance Matrix)는 다음과 같은 Positive Definite인 대칭 행렬(Symmetric Matrix)로 정의된다:

$$
\text{Cov}[\mathbf{x}] \triangleq \mathbb{E}\left[ (\mathbf{x} - \boldsymbol{\mu}_\mathbf{x}) (\mathbf{x} - \boldsymbol{\mu}_\mathbf{x})^\text{T} \right].
$$

공분산은 $$0$$과 무한대 사이의 값을 가지는 것을 우리는 확인할 수 있다. 하지만 몇몇의 경우 두 확률 변수 사이의 선형 관계성의 정도를 측정하기 위한 유한한 상한(Upper Bound)을 가지는 Normalized Measure가 필요한 경우가 있다. 이를 위하여 `상관 계수`(Correlation Coefficient)를 정의한다.

두 확률 변수 $$X, Y$$의 상관 계수는 다음과 같이 정의한다:

$$
\text{Corr}[X, Y] = \rho_{X, Y} \triangleq \frac{\sigma_{X, Y}}{\sigma_X \sigma_Y}.
$$

물론 마찬가지로 확률 벡터 $$\mathbf{x} = \left[ X_1, \cdots, X_d \right]$$의 `상관 계수 행렬`(Correlation Matrix) $$\mathbf{R}$$도 정의할 수 있다:

$$
[\mathbf{R}]_{i,j} = \rho_{X_i, X_j}.
$$

## Change of Variables

## Monte Carlo Approximation

## 참고 자료
- [Machine Learning: A Probabilistic Perspective](https://www.amazon.com/Machine-Learning-Probabilistic-Perspective-Computation/dp/0262018020)
- [https://m.blog.naver.com/sw4r/221010499304](https://m.blog.naver.com/sw4r/221010499304)

## 수정 사항
- 2022.06.25
    - 최초 게제
- 2022.06.27
    - 이산 확률 변수 내용 정리
- 2022.07.01
    - 다양한 확률 연산 내용 정리
- 2022.07.09
    - Generative Classifier 내용 정리
- 2022.07.11
    - 사건의 독립 내용 정리
- 2022.07.17
    - 연속 확률 변수 내용 정리
    - 평균, 분산, 공분산, 상관 계수 내용 정리