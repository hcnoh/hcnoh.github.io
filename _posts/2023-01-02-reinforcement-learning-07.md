---
layout: post
use_math: true
title: "[Reinforcement Learning] 7. Trust Region Policy Optimization (3)"
date: 2023-01-02 00:00:00
tagline: "Trust Region 방법을 이용하여 Policy Gradient의 성능을 향상시킨 알고리즘인 Trust Region Policy Optimization에 대해서 정리"
categories:
- Reinforcement Learning Study
tags:
- probability
- statistics
- machine learning
- reinforcement learning
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2023-01-02-reinforcement-learning-07
---

이번 포스트에서는 이전 포스트와 마찬가지로 `Trust Region Policy Optimization`(TRPO)를 설명하기 위한 이론적 배경들을 설명하도록 한다. 먼저 이전 포스트에서 다루었던 `연관된 정책 짝`과 `TV Distance` 사이의 관계를 먼저 정리하고 이어서 TV Distance의 중요한 성질인 `Pinsker's Inequality`를 정리하도록 한다.

## 연관된 정책 짝과 TV Distance 사이의 관계
먼저 다음의 Lemma를 살펴보도록 하자.

**Lemma 1)**
다음을 만족하는 분포 $$p, q$$를 가정하자:

$$
D_{\text{TV}}(p \Vert q) = \alpha.
$$

이때 두 분포 $$p, q$$는 다음을 만족한다:

$$
\alpha \leq p(X \neq Y), \ X \sim p, Y \sim q.
$$

증명은 다음과 같다. 먼저 모든 부분집합 $$A \subseteq \mathcal{X}$$에 대하여 다음을 확인할 수 있다:

$$
\begin{array}{rl}
p(A) - q(A)
& = \sum_{x \in A}p(x) - \sum_{x \in A}q(x) \\
& = p(X \in A) - p(Y \in A).
\end{array}
$$

또한 모든 확률 값은 $$1$$보다 작거나 같으므로:

$$
p(X \in A \land Y \notin A) \leq 1.
$$

이는 다음과 같이 정리될 수 있다:

$$
\begin{array}{ll}
p(X \in A \land Y \notin A) \leq 1 \\
\ \ \ \ \Longrightarrow p(X \in A) + p(Y \notin A) - p(X \in A \land Y \notin A) \leq 1 \\
\ \ \ \ \Longrightarrow p(X \in A) + p(Y \notin A) - 1 \leq p(X \in A \land Y \notin A) \\
\ \ \ \ \Longrightarrow p(X \in A) - p(Y \in A) \leq p(X \in A \land Y \notin A).
\end{array}
$$

여기서 또한 다음을 확인할 수 있다:

$$
X \in A \land Y \notin A \longrightarrow X \neq Y.
$$

따라서:

$$
p(X \in A \land Y \notin A) \leq p(X \neq Y).
$$

즉, 최종적으로 다음을 확인할 수 있다:

$$
\begin{array}{rl}
\alpha = \sum_{x:p(x) \geq q(x)} \left[ p(x) - q(x) \right] & \leq \max_A \left[ p(A) - q(A) \right] \\
& \leq p(X \neq Y). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

Lemma 1을 통해서 우리는 두 분포에 대한 TV Distance는 두 분포로 생성되는 샘플이 일치하지 않을 확률값에 대한 Lower Bound를 의미한다는 것을 확인할 수 있다. 다음으로 Lemma 2를 살펴보자.

**Lemma 2)**
다음을 만족하는 분포 $$p, q$$를 가정하자:

$$
D_{\text{TV}}(p \Vert q) = \alpha.
$$

이 때, 두 분포 $$p, q$$로 생성되는 랜덤 변수 $$X, Y$$에 대해서 $$X = Y$$일 확률이 $$1 - \alpha$$인 $$(X, Y)$$에 대한 결합 분포가 존재한다.

증명은 다음과 같다. 먼저 다음을 살펴보자:

$$
\begin{array}{rl}
\alpha = D_{\text{TV}}(p \Vert q)
& = \sum_{x:p(x) \geq q(x)} \left[ p(x) - q(x) \right] \\
& = \sum_{x:p(x) < q(x)} \left[ q(x) - p(x) \right].
\end{array}
$$

또한 다음의 분포들을 고려해보자:

$$
\begin{array}{rl}
f_1(x)
& = \left\{
\begin{array}{ll}
\frac{p(x) - q(x)}{\alpha} & \text{if} \ p(x) \geq q(x) \\
0 & \text{otherwise}
\end{array},
\right. \\
f_2(x)
& = \left\{
\begin{array}{ll}
0 & \text{if} \ p(x) \geq q(x) \\
\frac{q(x) - p(x)}{\alpha} & \text{otherwise}
\end{array},
\right. \\
f_3(x)
& = \left\{
\begin{array}{ll}
\frac{q(x)}{1 - \alpha} & \text{if} \ p(x) \geq q(x) \\
\frac{p(x)}{1 - \alpha} & \text{otherwise}
\end{array}.
\right.
\end{array}
$$

그러면 다음을 확인할 수 있다:

$$
\begin{array}{rl}
\sum_x f_1(x)
& = \sum_{x: p(x) \geq q(x)} f_1(x) = 1, \\
\sum_x f_2(x)
& = \sum_{x: p(x) < q(x)} f_2(x) = 1, \\
\sum_x f_3(x)
& = \frac{1}{1 - \alpha} \left[ \sum_{x: p(x) \geq q(x)} q(x) + \sum_{x: p(x) < q(x)} p(x) \right] \\
& = \frac{1}{1 - \alpha} \left[ \sum_{x: p(x) \geq q(x)} q(x) + 1 - \sum_{x: p(x) \geq q(x)} p(x) \right] \\
& = \frac{1}{1 - \alpha} \left( 1 - \sum_{x: p(x) \geq q(x)} \left[ p(x) - q(x) \right] \right) \\
& = \frac{1 - \alpha}{1 - \alpha} = 1.
\end{array}
$$

즉, $$f_1, f_2, f_3$$는 확률 분포라는 것을 확인할 수 있다. 또한 다음의 확률 분포를 가지는 랜덤 변수 $$Z$$를 고려하자:

$$
\begin{array}{rl}
p(z)
& = \left\{
    \begin{array}{ll}
        1 - \alpha & \text{if} \ z = 1, \\
        \alpha & \text{if} \ z = 0, \\
        0 & \text{otherwise}.
    \end{array}
\right.
\end{array}
$$

이를 바탕으로 다음과 같은 샘플링 과정(Sampling Process)을 고려해보자. 만약 $$Z = 1$$이라면 $$X$$를 $$f_3$$로 부터 생성하고 $$Y$$는 $$Y = X$$로 선택한다. 그리고 만약 $$Z = 0$$이라면 $$X$$는 $$f_1$$으로부터 생성하고 $$Y$$는 $$f_2$$로부터 생성한다.

이 과정으로부터 다음을 확인할 수 있다:

$$
\begin{array}{rl}
p(X = Y)
& = 1 - \alpha, \\
p(x)
& = (1 - \alpha) f_3(x) + \alpha f_1(x), \\
q(x)
& = (1 - \alpha) f_3(x) + \alpha f_2(x). \ \ \ \ \text{Q.E.D.}
\end{array}
$$

Lemma 1과 Lemma 2를 통해서 우리는 두 분포 $$p, q$$로 생성되는 샘플 $$X \sim p, Y \sim q$$가 일치하지 않을 확률값 $$p(X \neq Y)$$가 TV Distance $$D_{\text{TV}}(p \Vert q)$$와 일치하는 결합 분포가 항상 존재하며, 따라서 TV Distance는 두 분포로 생성되는 샘플이 일치하지 않을 확률값이 가질 수 있는 최소값이라는 것을 알 수 있다.

위의 Lemma 1과 Lemma 2를 통해서 이제 `연관된 정책 짝`과 `TV Distance` 사이의 관계를 생각해보자. 먼저 주어진 정책 $$\pi, \tilde{\pi}$$에 대해서 다음의 정의를 살펴보자:

$$
D_\text{TV}^\text{max}(\pi, \tilde{\pi}) := \max_s D_\text{TV}\left( \pi(\cdot \vert s) \Vert \tilde{\pi}(\cdot \vert s) \right) = \alpha.
$$

이 정의와 Lemma 2를 통해서 적절한 결합 분포 $$p(a, \tilde{a} \vert \pi, \tilde{\pi})$$를 선택하여 항상 다음을 만족하도록 할 수 있다는 것을 확인할 수 있다:

$$
\alpha = \max_s p(a \neq \tilde{a} \vert \pi, \tilde{\pi}, s).
$$

즉, 적절한 결합 분포만 잘 설정하여 준다면 임의로 주어진 정책 $$\pi, \tilde{\pi}$$는 항상 $$D_\text{TV}^\text{max}(\pi, \tilde{\pi}) = \alpha$$에 대해서 $$\alpha$$로 연관된 정책 짝이 된다는 것을 최종적으로 확인할 수 있다.

### 개인적인 의견
위에서 설명한 `연관된 정책 짝`과 `TV Distance` 사이의 관계는 향후 `TRPO`를 설명하는 중요한 정리를 증명하는데 활용되게 된다. 하지만 위의 설명 자체는 상당한 억지가 존재한다.

먼저 주어진 두 정책 $$\pi, \tilde{\pi}$$에 대한 결합 분포는 기본적으로 두 정책의 곱으로 결정되게 될 것이다. 만약 그렇지 않다면 두 정책의 행동이 서로 독립적이지 않다는 이야기인데 $$\pi$$에서 새로운 정책 $$\tilde{\pi}$$로 갱신을 시도한다는 관점에서 두 정책의 행동이 서로 종속적이라는 이야기는 어폐가 있다. 즉 두 정책에 대한 결합 분포는 다음과 같이 계산한다:

$$
p(a, \tilde{a} \vert \pi, \tilde{\pi}, s) = \pi(a \vert s) \cdot \tilde{\pi}(\tilde{a} \vert s).
$$

즉, Lemma 2가 적용이 되기 위한 결합 분포의 선택의 자유가 이 상황에서는 존재하지 않는다. 여기서 결합 분포를 결정하는 요소는 결국 현재 상태 $$s$$만이 존재할 뿐일 것이다.

따라서 적절한 결합 분포를 잘 선택하면 임의로 주어진 정책 $$\pi, \tilde{\pi}$$는 항상 $$D_\text{TV}^\text{max}(\pi, \tilde{\pi}) = \alpha$$에 대해서 $$\alpha$$로 연관된 정책 짝이 된다는 명제는 상태 $$s$$를 잘 선택한다면 $$D_\text{TV}^\text{max}(\pi, \tilde{\pi})$$와 $$\max_s p(a \neq \tilde{a} \vert \pi, \tilde{\pi}, s)$$가 동일하다는 명제로 바꿔서 말할 수 있을 것이지만 당연하게도 이는 상당한 억지이다.

좌우지간 향후 포스트에서는 이 개인적인 의견은 잠시 뒤로 미루고 논문에서 주장하는 바에 대해서만 작성을 진행하도록 하겠다.

## Pinsker's Inequality
`Pinsker's Inequality`는 `Kullback-Leibler Divergence`(KL Divergence)와 `TV Distance` 사이의 관계에 대한 부등식이다. Pinsker's Inequality를 기술하면 다음과 같다:

**Theorem 1)**
공간 $$\mathcal{X}$$에서 정의된 분포 $$p, q$$에 대하여 다음의 부등식이 성립한다:

$$
D_{\text{TV}} (p \Vert q) \leq \sqrt{\frac{1}{2} D_{\text{KL}}(p \Vert q)}.
$$

이를 증명해보자. 먼저 다음과 같은 특수한 상황을 고려해보자:

$$
\begin{array}{rl}
p(x)
& = \left\{
    \begin{array}{ll}
    p & \text{if} \ x = 1, \\
    1 - p & \text{if} \ x = 0, \\
    0 & \text{otherwise}.
    \end{array}
\right. \\
q(x)
& = \left\{
    \begin{array}{ll}
    q & \text{if} \ x = 1, \\
    1 - q & \text{if} \ x = 0, \\
    0 & \text{otherwise}.
    \end{array}
\right.
\end{array}
$$

추가적으로 다음의 함수를 고려하자:

$$
f(p, q) = p \log \frac{p}{q} + (1 - p) \log \frac{1 - p}{1 - q} - 2(p - q)^2.
$$

만약 $$p = q$$라면 $$f = 0$$일 것이다. 그리고:

$$
\begin{array}{rl}
\frac{\partial f}{\partial q}
& = -\frac{p}{q} - \frac{1 - p}{1 - q} \cdot (-1) - 2 \cdot 2 \cdot (p - q) \cdot (-1) \\
& = \frac{-p(1 - q) + q(1 - q)}{q(1 - q)} + 4(p - q) \\
& (p - q) \left[ 4 - \frac{1}{q(1 - q)} \right].
\end{array}
$$

우리는 다음을 확인할 수 있다:

$$
q + (1 - q) = 1 \geq 2 \sqrt{q(1 - q)} \ (\because \ 0 \leq q \leq 1).
$$

$$
\Longrightarrow q(1 - q) \leq \frac{1}{4}.
$$

따라서 $$p \geq q$$일 때는 $$\frac{\partial f}{\partial q} \leq 0$$이고, $$p \leq q$$일 때는 $$\frac{\partial f}{\partial q} \geq 0$$이다.

이를 통해서 $$f(p, q)$$는 $$p = q$$인 지점에서 최소인 포물선 형태의 함수일 것이고 항상 다음과 같은 결과를 만족할 것이다:

$$
f(p, q) \geq 0 \Longrightarrow D_\text{TV}(p \Vert q) \leq \sqrt{\frac{1}{2} D_\text{KL}(p \Vert q)}.
$$

이를 통해 우리는 특수한 상황인 $$f(p, q)$$에 대해서 Pinsker's Theorem을 증명하였다. 이제 일반적인 상황을 고려해보자.

공간 $$\mathcal{X}$$에서 정의된 분포 $$p, q$$에 대하여 부분집합 $$A \subseteq X$$를 다음과 같이 정의하자:

$$
A = \left\{ x: p(x) \geq q(x) \right\}.
$$

또한 분포 $$p_A$$와 $$q_A$$를 다음과 같이 정의하자:

$$
\begin{array}{rl}
p_A(y)
& = \left\{
    \begin{array}{ll}
        p(A) & \text{if} \ y = 1, \\
        p(\bar{A}) & \text{otherwise}.
    \end{array}
\right. \\
q_A(y)
& = \left\{
    \begin{array}{ll}
        q(A) & \text{if} \ y = 1, \\
        q(\bar{A}) & \text{otherwise}.
    \end{array}
\right. \\
\end{array}
$$

여기서 $$y$$는 다음과 같다:

$$
\begin{array}{rl}
y
& = \left\{
    \begin{array}{ll}
        1 & \text{if} \ x \in A, \\
        0 & \text{otherwise}.
    \end{array}
\right.
\end{array}
$$

이를 통해 다음을 유도할 수 있다:

$$
\begin{array}{rl}
D_\text{TV}(p \Vert q)
& = \sum_{x \in A} \left[ p(x) - q(x) \right] = p(A) - q(A) \\
& = \sum_{y \in \left\{ 1 \right\}} \left[ p_A(y) - q_A(y) \right] = D_\text{TV} \left( p_A \Vert q_A \right).
\end{array}
$$

이제 KL Divergence를 살펴보자:

$$
\begin{array}{rl}
D_\text{KL}(p_A \Vert q_A)
& = -H(p_A) + p_A(1) \log \frac{1}{q_A(1)} + p_A(0) \log \frac{1}{q_A(0)} \\
& = -H(p_A) + \sum_{x \in A} p(x) \log \frac{1}{\sum_{x \in A} q(x)} \\
& \ \ \ \ + \sum_{x \in \bar{A}} p(x) \log \frac{1}{\sum_{x \in \bar{A}} q(x)}.
\end{array}
$$

우리는 여기서 다음을 확인할 수 있다:

$$
q(x') \leq \sum_{x \in B} q(x) \ \text{for some} \ x' \in B.
$$

$$
\Longrightarrow \log \frac{1}{q(x')} \geq \log \frac{1}{\sum_{x \in B} q(x)} \ \text{for some} \ x' \in B.
$$

따라서:

$$
\begin{array}{rl}
D_\text{KL} (p_A \Vert q_A)
& \leq -H(p_A) + \sum_{x \in A} p(x) \log \frac{1}{q(x)} + \sum_{x \in \bar{A}} p(x) \log \frac{1}{q(x)} \\
& = -H(p_A) + \sum_{x \in \mathcal{X}} p(x) \log \frac{1}{q(x)}.
\end{array}
$$

또한:

$$
\begin{array}{rl}
-H(p_A)
& = p_A(1) \log p_A(1) + p_A(0) \log p_A(0) \\
& = \left( \sum_{x \in A} p(x) \right) \log \left(  \sum_{x \in A} p(x) \right) + \left( \sum_{x \in \bar{A}} p(x) \right) \log \left( \sum_{x \in \bar{A}} p(x) \right).
\end{array}
$$

`Jansen's Inequality`를 이용하여 다음을 확인할 수 있다:

$$
g(z) = z \log z \Longrightarrow g''(z) = \frac{1}{z} > 0 \ (z > 0).
$$

$$
\Longrightarrow \left( \sum_{x \in B} p(x) \right) \log \left( \sum_{x \in B} p(x) \right) \leq \sum_{x \in B} p(x) \log p(x).
$$

그러므로:

$$
\begin{array}{rl}
-H(p_A)
& \leq \sum_{x \in A} p(x)\log p(x) + \sum_{x \in \bar{A}} p(x) \log p(x) \\
& = \sum_{x \in \mathcal{X}} p(x)\log p(x) \\
& = -H(p).
\end{array}
$$

$$
\begin{array}{lrl}
\Longrightarrow
& D_\text{KL}(p_A \Vert q_A)
& \leq -H(p) + \sum_{x \in \mathcal{X}} p(x) \log \frac{1}{q(x)} \\
& & = D_\text{KL}(p \Vert q) \\
\Longrightarrow
& D_\text{KL}(p \Vert q)
& \geq D_\text{KL}(p_A \Vert q_A) \\
& & \geq 2 D_\text{TV}^2 (p_A \Vert q_A) \ \text{(Special Case)} \\
& & = 2 D_\text{TV}^2 (p \Vert q). \\
\Longrightarrow
& D_\text{TV}(p \Vert q) & \leq \sqrt{\frac{1}{2}D_\text{KL}(p \Vert q)}. \ \ \ \ \text{Q.E.D.}
\end{array}
$$

## 참고 자료
- [Wikipedia: Reinforcementa Learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
- [Trust Region Policy Optimization](https://arxiv.org/pdf/1502.05477.pdf)
- [Markov Chains and Mixing Times](http://103.47.12.35/bitstream/handle/1/2397/PS51%20mcmt2e.pdf?sequence=1&isAllowed=y)
- [Wikipedia: Pinsker's Inequality](https://en.wikipedia.org/wiki/Pinsker%27s_inequality)

## 수정 사항
- 2023.01.02
    - 최초 게제
- 2023.01.14
    - 증명 정리
- 2023.01.15
    - Pinsker's Inequality 증명 정리 완료