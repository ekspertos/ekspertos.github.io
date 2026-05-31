---
layout: post
title:  "Universal Approximation Theorem"
subtitle: "Deep Learning Intuituion"
categories: projects
tags: UAV DeepLearning MachineLearning
comments: true
hidden: false
---

> 딥러닝 모델은 이미지 분류, 음성 인식, 자연어 처리와 같은 다양한 문제에서 뛰어난 성능을 보여준다. 그렇다면 신경망은 어떻게 이렇게 복잡한 문제들을 해결할 수 있을까?
> 가장 단순한 형태의 신경망은 입력 벡터 에 대해 선형 변환을 수행한다. 하지만 선형 함수만으로는 XOR 문제조차 해결할 수 없으며, 현실 세계의 복잡한 데이터 관계를 표현하기에는 한계가 있다.
> 이를 해결하기 위해 신경망은 Sigmoid, ReLU 와 같은 활성화 함수를 사용하여 비선형성을 추가한다. 놀랍게도 이러한 비선형 활성화 함수와 충분한 수의 뉴런만 있다면, 신경망은 우리가 원하는 거의 모든 함수를 원하는 정확도로 근사할 수 있다.
> 이 사실을 보장하는 이론이 바로 **Universal Approximation Theorem (UAT)** 이다.
> 이번 글에서는 간단한 XOR 예제부터 시작하여, 활성화 함수가 어떻게 Rectangular Function을 만들고, 이를 조합하여 복잡한 함수를 근사할 수 있는지 직관적으로 살펴본다.


딥러닝 모델을 학습하는 것은 우리가 모은 학습 데이터 $(x,y)$ 쌍에 대해 $y \cong f_\theta(x)$ 가 성립하도록 함수 $f_\theta(x)$ 의 가중치 $\theta$ 를 찾는 과정이다.

$$
\min_{\theta} \mathcal{L}(f_\theta(\mathbf{x}),\mathbf{y})
$$

모델 $f_\theta(x)$ 는 여러 레이어의 선형 변환

$$
\hat{\mathbf{x}} = W\mathbf{x}+\mathbf{b}
$$

을 겹겹이 쌓은 형태로 구성된다. 만약 선형 변환만 사용한다면 여러 레이어를 쌓더라도 결국 하나의 선형 변환과 동일하다.

$$
W_2(W_1\mathbf{x}+\mathbf{b}_1)+\mathbf{b}_2
=
(W_2W_1)\mathbf{x}+(W_2\mathbf{b}_1+\mathbf{b}_2)
$$

따라서 선형 함수만으로는 복잡한 비선형 관계를 표현할 수 없다.

이 문제를 해결하기 위해 Sigmoid, ReLU 와 같은 활성화 함수(activation function)를 각 레이어 사이에 삽입한다. 활성화 함수가 추가되면 모델은 비선형 함수를 표현할 수 있게 된다.

예를 들어 XOR 함수를 생각해보자.

| $x_1$ | $x_2$ | XOR |
|---------|---------|---------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

XOR 는 선형 분리가 불가능하므로 선형 모델만으로는 표현할 수 없다. 하지만 ReLU 활성화를 사용하면

$$
\text{XOR}(x_1,x_2)
=
\text{ReLU}(x_1-x_2)
+
\text{ReLU}(x_2-x_1)
$$

로 표현할 수 있다.

이를 2-layer 신경망 형태로 작성하면 다음과 같다.

### 1번째 레이어 (Hidden Layer)

$$
\hat{\mathbf{x}}
=
W_1\mathbf{x}
,\qquad
W_1=
\begin{bmatrix}
1 & -1\\
-1 & 1
\end{bmatrix}
$$

### 활성화 함수

$$
\tilde{\mathbf{x}}
=
\text{ReLU}(\hat{\mathbf{x}})
$$

### 2번째 레이어 (Output Layer)

$$
y
=
W_2\tilde{\mathbf{x}}
,\qquad
W_2=
\begin{bmatrix}
1 & 1
\end{bmatrix}
$$

이처럼 활성화 함수를 사용하면 선형 모델로는 표현할 수 없는 함수를 표현할 수 있다.

---

## Universal Approximation Theorem

XOR 예제는 활성화 함수가 선형 모델의 한계를 극복할 수 있음을 보여준다.

더 일반적으로 Universal Approximation Theorem(UAT)은 적절한 활성화 함수를 사용하는 신경망이 충분히 많은 뉴런을 가질 경우 임의의 연속 함수를 원하는 정확도로 근사할 수 있음을 보장한다.

예를 들어 1-hidden-layer 신경망

$$
f_\theta(x)
=
W_2 \sigma(W_1\mathbf{x}+\mathbf{b}_1)
$$

이 있다고 하자.

충분히 많은 hidden neuron 을 사용하면 $f_\theta(\mathbf{x})$
는 대부분의 연속 함수를 원하는 정확도로 근사할 수 있다.

그렇다면 왜 이런 일이 가능할까?

직관적으로는 활성화 함수를 조합하여 작은 구간 함수(rectangular function)를 만들 수 있고, 이러한 함수들을 많이 쌓으면 복잡한 함수를 근사할 수 있기 때문이다.

---

## Sigmoid로 Rectangular Function 만들기

Sigmoid 함수 $\sigma(x)$ 를 생각해보자.

$$
\sigma(x)
=
\frac{1}{1+e^{-x}}
$$

기울기 $b$ 를 크게 하면

$$
u(x-a)
=
\lim_{b\to\infty}
\sigma(b(x-a))
$$

와 같이 Unit Step Function 을 근사할 수 있다.

따라서

$$
\text{rect}(x;a_1,a_2,c)
=
c\,u(x-a_1)
-
c\,u(x-a_2)
$$

를 사용하면 $a_1$ 과 $a_2$ 사이에서만 값이 $c$ 인 Rectangular Function 을 만들 수 있다.
$b$ 가 커질수록 더욱 직사각형에 가까워진다.

---

## ReLU로 Rectangular Function 만들기

ReLU 함수 를 사용해도 비슷한 구조를 만들 수 있다.

$$
\sigma_{\text{ReLU}}(x)
=
\max(0,x)
$$

먼저

$$
u(x-a)
=
\lim_{\epsilon\to0}
\frac{
\sigma_{\text{ReLU}}(x-a+\epsilon)
-
\sigma_{\text{ReLU}}(x-a)
}
{\epsilon}
$$

를 통해 Unit Step Function 을 만들 수 있다.

따라서

$$
\text{rect}(x;a_1,a_2,c)
=
c\,u(x-a_1)
-
c\,u(x-a_2)
$$

를 구성할 수 있다.

Sigmoid 의 경우 Rectangular Function 하나를 만드는 데 2개의 뉴런이 필요하지만, ReLU 의 경우 Step Function 을 먼저 구성해야 하므로 더 많은 뉴런이 필요하다.

---

## 함수를 근사하는 방법

Rectangular Function 을 여러 개 배치하면 Piecewise Constant Function 을 만들 수 있다.

<img src="https://ekspertos.github.io/assets/img/review/Regularization/UniversalApproximation1.png" width="700">

Rectangular Function 의 개수를 늘릴수록 원하는 함수에 더욱 가까워진다.

<img src="https://ekspertos.github.io/assets/img/review/Regularization/UniversalApproximation2.png" width="700">

아래는 이러한 방식으로 $sin(x)$ 와 $sin(x)+cos(3x)$ 를 근사한 예시이다.

<img src="https://ekspertos.github.io/assets/img/review/Regularization/UniversalApproximation3.png" width="700">

이것이 Universal Approximation Theorem 의 핵심 직관이다. 활성화 함수로 구성된 뉴런들을 충분히 많이 사용하면 복잡한 함수도 원하는 정확도로 근사할 수 있다.

UAT 관점에서 unit function을 만들기 위해 큰 b를 사용하면 saturation 문제가 더욱 심해진다. 이러한 saturation 특성은 sigmoid 계열 활성화 함수의 근본적인 단점이며, 깊은 신경망에서는 gradient vanishing을 유발할 수 있다. ReLU는 양수 영역에서 gradient가 유지되기 때문에 이러한 문제를 완화할 수 있어 현대 딥러닝에서 더 널리 사용된다.


검증을 해보자면, 시그모이드 활성화 함수를 사용한 모델로 $sin(x)$ 함수를 학습한 결과이다.

<img src="https://ekspertos.github.io/assets/img/review/Regularization/UniversalApproximationSinfunction.png" width="700">

$(a)$는 각각 $\text{I. } (-\pi,\pi)$, $\text{II. } (-2\pi,2\pi)$, $\text{III. } (-3\pi,3\pi)$ 범위의 데이터로 학습된 모델의 첫 번째 레이어 bias $b$ 분포를 나타낸다. 입력 범위가 넓어질수록 bias 분포 역시 더 넓은 영역으로 확장되는 경향을 확인할 수 있다.

$(b)$는 $(-\pi,\pi)$ 범위의 데이터로 학습된 모델을 더 넓은 입력 구간에서 평가한 결과이다. 학습에 사용된 구간에서는 원래 함수를 잘 근사하지만, 학습 범위를 벗어나면 오차가 증가하는 것을 확인할 수 있다. 이는 sigmoid 뉴런의 transition point가 주로 학습 데이터가 존재하는 영역에 형성되기 때문이며, 그 결과 학습 범위 밖에서는 충분한 표현력을 제공하지 못하기 때문이다.

---

## 실습 코드

```python
import numpy as np
import matplotlib.pyplot as plt

def sigmoid(x):
    return 1 / (1 + np.exp(-x))

# -------------------------
# USER FUNCTION HERE
# -------------------------
def f_target(x):
    return np.sin(x) + 0.3 * np.cos(3*x)  # <- 바꿔도 됨

# domain
x = np.linspace(0, 2*np.pi, 2000).reshape(1, -1)

# resolution (basis size)
K = 20
sharp = 80

# interval boundaries
a = np.linspace(0, 2*np.pi, K, endpoint=False)
b = np.linspace(2*np.pi/K, 2*np.pi, K, endpoint=True)

mid = (a + b) / 2

# sample target function
w = f_target(mid)

# -------------------------
# Hidden layer: step detectors
# -------------------------
W1 = np.zeros((2*K, 1))
b1 = np.zeros((2*K, 1))

for i in range(K):
    # left step
    W1[2*i, 0] = sharp
    b1[2*i, 0] = -sharp * a[i]

    # right step
    W1[2*i+1, 0] = sharp
    b1[2*i+1, 0] = -sharp * b[i]

H = sigmoid(W1 @ x + b1)  # (2K, N)

# -------------------------
# Output layer: linear combination
# -------------------------
W2 = np.zeros((1, 2*K))

for i in range(K):
    W2[0, 2*i] = w[i]
    W2[0, 2*i+1] = -w[i]

y = W2 @ H
y = y.flatten()

# true function
y_true = f_target(x.flatten())

# plot
plt.plot(x.flatten(), y_true, label="target f(x)", linewidth=2, color='blue')
plt.plot(x.flatten(), y, label="step NN approx", linewidth=2, color='orange')
plt.legend()
plt.title("Universal Approximation via Step Basis NN")
plt.show()
```

---
## 참고
1. <https://dlaiml.tistory.com/entry/Universal-Approximation-Theorem>