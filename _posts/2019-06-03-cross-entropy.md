---
layout: post
use_math: true
title: "[Statistics] Cross-Entropy의 통계적인 의미"
date: 2019-04-25 11:04:33
tagline: "머신 러닝 및 딥러닝에서 Loss Function으로 자주 사용되는 Cross-Entropy의 유도 과정 및 통계적인 의미 정리"
categories:
- Statistics
tags:
- statistics
- machine learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-03-cross-entropy
---

## 문제 세팅
Cross-Entropy를 설명하기 위한 문제는 기본적으로 Binary Classification에서 Optimal Classifier를 어떻게 찾을 지에 대한 문제 세팅을 가지게 된다. Optimal Classifier에 대한 정의는 Logistic Regression에 대한 [포스팅](https://hcnoh.github.io/2019-04-25-logistic-regression-and-naive-bayes)에 정리가 되어있으니 참고하면 될 것 같다. 기본적으로 Binary라는 가정을 가지고 가지만 Multi Class에 대해서도 간단하게 확장이 가능하다.

어쨌든 우리는 여기서 Binary Classification을 위한 Logistic Regression 문제를 풀어나갈 것이다. 이전 [포스팅](https://hcnoh.github.io/2019-04-25-logistic-regression-and-naive-bayes)에서도 확인할 수 있듯이 Binary Classification을 위하여 다음과 같은 Bernoulli 분포를 가정한 모델을 사용하게 될 것이다.

$$
\begin{align*}
P(Y=y \vert X=\mathbf{x};\boldsymbol{\theta})
& = p_{\boldsymbol{\theta}}(\mathbf{x})^y(1−p_{\boldsymbol{\theta}}(\mathbf{x}))^{1−y} \\
p_{\boldsymbol{\theta}}(\mathbf{x})
& = P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})
\end{align*}
$$

여기서 우리는 데이터셋 $$D$$를 가장 잘 설명할 수 있는 파라미터 $$\boldsymbol{\theta}$$를 찾아야 할 것이다.

## Log Likelihood Function
데이터셋 $$D$$를 가장 잘 설명할 수 있는 파라미터 $$\boldsymbol{\theta}$$를 찾기 위한 최적의 방법은 Likelihood Function을 최대로 하는 파라미터 $$\boldsymbol{\theta}$$를 찾는 방법인 Maximum Likelihood Estimation (MLE)라는 것이 잘 알려져 있다. 여기서는 데이터셋 $$D$$ 내부에서의 $$\mathbf{x}$$와 $$y$$가 서로 Dependent하기 때문에 Maximum Conditional Likelihood Estimation (MCLE)를 사용하게 될 것이다. 일단 이 세팅에서 Conditional Likelihood Function을 써보면 다음과 같다.

$$
\begin{align*}
\mathcal{L}(\boldsymbol{\theta} ; D)
& = P(D ; \boldsymbol{\theta}) \\
& = \prod_{(\mathbf{x}, y) \in D} P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

이러한 형식의 함수를 다루는 경우에는 곱하기 연산을 보통 복잡하다고 생각하여 Log를 씌움으로써 식을 간단하게 만들게 된다. 이렇게 만들어진 함수는 Log (Conditional) Likelihood Function이 될 것이다. MLE 또는 MCLE를 사용하는 경우 Log 함수의 증가함수의 특성 덕분에 Optimization 문제를 푸는 것의 결과에는 영향을 주지 않게 된다.

$$
\begin{align*}
\log \mathcal{L}(\boldsymbol{\theta} ; D)
& = \log P(D ; \boldsymbol{\theta}) \\
& = \sum_{(\mathbf{x}, y) \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

## Maximum Likelihood Estimation
위에서 구한 Log (Conditional) Likelihood Function을 이용하여 파라미터를 추정하기 위한 방법인 Maximum (Conditional) Likelihood Estimation의 Optimization 문제를 써보면 아래와 같다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta})
\end{align*}
$$

여기에 우리가 위에서 한 문제 세팅의 가정을 사용하여 식을 다시 쓸 수 있다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \log P(Y=y \vert X=\mathbf{x} ; \boldsymbol{\theta}) \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \log p_{\boldsymbol{\theta}}(\mathbf{x})^y(1−p_{\boldsymbol{\theta}}(\mathbf{x}))^{1−y} \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \left[ y\log (p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \min_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} -\left[ y\log (p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right]
\end{align*}
$$

우리가 잘 알고있는 Cross-Entropy Loss의 모양이 등장했다. 이 유도 결과를 통해서 우리는 Cross-Entropy Loss를 최소화하는 것의 의미는 데이터셋 $$D$$를 설명하는 Log Likelihood를 최대화하는 Optimization 문제의 Objective Function이라는 것을 확인할 수 있다.

## Cross-Entropy Loss로부터 Cross-Entropy 유도
위에서 유도한 Cross-Entropy Loss의 결과를 이용하여 아래와 같이 식을 변형할 수 있다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \min_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} -\left[ y\log (p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{(\mathbf{x}, y) \in D} \left[ y\log (p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y\sim P_D(y\vert \mathbf{x})} \left[ y\log(p_{\boldsymbol{\theta}}(\mathbf{x})) + (1-y)\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ N_D(\mathbf{x})P_D(Y=1 \vert X=\mathbf{x})\log(p_{\boldsymbol{\theta}}(\mathbf{x})) \right. \\
& \left. \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad + \ N_D(\mathbf{x})P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right]
\end{align*}
$$

여기서 $$N_D(\mathbf{x})$$는 주어진 $$\mathbf{x}$$에 대한 데이터셋 내부의 샘플 갯수이며, $$P_D$$는 주어진 확률 변수에 대한 데이터셋 내부의 분포이다. 만약 데이터셋의 분포가 균일하여 $$N_D(\mathbf{x})$$가 모든 샘플 데이터 $$\mathbf{x}$$에 대해서 일정하다면 상수로 취급되어 Optimization 문제에서 생략될 것이다.

$$
\begin{align*}
\boldsymbol{\theta}^*
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ P_D(Y=1 \vert X=\mathbf{x}) \log(p_{\boldsymbol{\theta}}(\mathbf{x})) + P_D(Y=0 \vert X=\mathbf{x})\log(1-p_{\boldsymbol{\theta}}(\mathbf{x})) \right] \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \left[ P_D(Y=1\vert X=\mathbf{x})\log(P(Y=1\vert X=\mathbf{x};\boldsymbol{\theta})) \right. \\
& \left. \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad \quad + \ P_D(Y=0 \vert X=\mathbf{x})\log(P(Y=0\vert X=\mathbf{x};\boldsymbol{\theta})) \right] \\
& \\
& = \arg \max_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} \sum_{y \in \{0,1\}} P_D(Y=y\vert X=\mathbf{x}) \log(P(Y=y \vert X=\mathbf{x}; \boldsymbol{\theta})) \\
& = \arg \min_{\boldsymbol{\theta}} \sum_{\mathbf{x}\sim P_D(\mathbf{x})} H(P_D(Y\vert X=\mathbf{x}), P(Y \vert X=\mathbf{x} ; \boldsymbol{\theta}))
\end{align*}
$$


