---
layout: post
title:  "Batch Normalization"
subtitle: "Normalization"
categories: projects
tags: blog github pages jekyll spacy Regularization Normalization
comments: true
hidden: false
---

> 딥러닝 모델은 일반적으로 Gradient Descent를 사용하여 학습되며, 효율적인 최적화를 위해 입력 데이터의 분포를 적절히 조정하는 것이 중요하다. 입력 feature들의 스케일이 크게 다르거나 강한 상관관계를 가지면 최적화가 어려워질 수 있기 때문에, Standardization, Decorrelation, Whitening과 같은 전처리 기법이 사용된다. 이러한 아이디어를 네트워크 내부의 각 레이어에 적용한 것이 Batch Normalization이며, 이는 학습을 안정화하고 학습 속도를 향상시키는 데 도움을 준다. 이 포스트에서는 Batch Normalization의 동작 원리를 살펴보고, 왜 이 기법이 딥러닝 학습에 효과적인지 분석한다.


## 1. Why Batch Normalization? 

딥러닝은 복잡한 문제를 해결하기 위해 여러 개의 레이어로 구성된 심층 신경망을 학습한다. 이러한 모델은 일반적으로 Gradient Descent 계열의 최적화 알고리즘을 사용하여 파라미터를 업데이트한다.

Gradient Descent가 효율적으로 동작하기 위해서는 입력 데이터의 스케일과 분포가 적절하게 조정되는 것이 중요하다. 입력 feature들의 스케일이 크게 다르거나 서로 강한 상관관계를 가지는 경우, 최적화 문제가 ill-conditioned해질 수 있으며 이는 학습 속도를 저하시킨다.

이러한 문제를 완화하기 위해 입력 데이터에 다양한 전처리 기법이 사용된다. 대표적으로 Normalization, Decorrelation, Whitening 등이 있다.
실제로는 계산 비용과 구현의 단순성 때문에 Whitening보다는 Standardization이 훨씬 널리 사용되며, 대부분의 딥러닝 모델에서 기본적인 전처리 방법으로 자리 잡았다.

그렇다면 **입력 데이터뿐 아니라 네트워크 내부의 각 레이어로 전달되는 활성값(activation) 역시 적절한 분포를 유지하도록 만들면 학습을 더욱 쉽게 만들 수 있지 않을까?**
이러한 아이디어에서 출발한 기법이 Batch Normalization(BN)이다. Batch Normalization은 각 레이어의 입력을 정규화하여 학습을 안정화하고, 더 큰 학습률을 사용할 수 있도록 하며, 결과적으로 딥러닝 모델의 학습을 크게 가속화한다.

---

## 2. Two Perspectives on Why Batch Normalization Works

이번 글에서는 Batch Normalization(BN)이 어떻게 동작하는지, 그리고 왜 딥러닝 학습에 효과적인지 살펴본다. 이를 위해 Batch Normalization을 처음 제안한 논문인 Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift와, 이후 BN의 성공 원인을 재분석한 *How Does Batch Normalization Help Optimization?*을 중심으로 리뷰한다.

먼저 Batch Normalization 논문은 BN의 핵심 효과를 Internal Covariate Shift(ICS) 의 완화로 설명한다. 저자들은 학습 과정에서 이전 레이어의 파라미터가 변화함에 따라 각 레이어의 입력 분포가 지속적으로 변하는 현상이 최적화를 어렵게 만든다고 주장한다. BN은 각 레이어의 입력을 정규화하여 이러한 분포 변화를 줄이고, 활성화 함수가 saturation 영역에 머무르는 비율을 감소시켜 학습을 안정화한다. 또한 BN은 가중치의 크기(scale)에 대해 출력이 불변인 특성을 가지므로, 모델이 단순히 weight norm을 키우는 방향이 아니라 실제로 유의미한 방향의 변화를 학습하도록 유도한다고 설명한다.

반면 *How Does Batch Normalization Help Optimization?*은 BN의 성능 향상이 정말 ICS 감소 때문인지 면밀히 분석한다. 흥미롭게도 실험 결과 BN을 사용하더라도 ICS가 반드시 감소하지 않으며, 경우에 따라서는 오히려 증가할 수도 있음을 보인다. 대신 저자들은 BN의 핵심 효과가 손실 함수의 지형(loss surface)을 더 부드럽고 예측 가능하게 만드는 데 있다고 주장한다. 이러한 성질은 gradient의 변화를 안정화하여 더 큰 learning rate를 사용할 수 있게 만들고, 결과적으로 최적화를 더욱 빠르고 안정적으로 수행할 수 있도록 한다.

즉, 첫 번째 논문은 BN의 효과를 "입력 분포 변화의 감소" 관점에서 설명하는 반면, 두 번째 논문은 BN이 최적화 문제 자체를 더 잘-conditioned하게 만든다는 관점에서 그 성공 원인을 해석한다. 이번 글에서는 두 논문의 주장과 실험 결과를 비교하며 Batch Normalization의 동작 원리를 자세히 살펴본다.

---

## 3. Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift

Internal covariance shift 때문에 생기는 문제를 Batch Norm 으로 완화 하고자 한다.
보통 whitening, decorrelate 또는 Normalize 기법을 사용해서 gradient descent를 사용할 때 모델의 학습 속도를 향상 시킬 수 있다.

- Normalize: hessian 의 대각 성분이 1이 되게 한다. 
- Decorrelate: hessian 의 비대각 성분이 0이 되게 한다.
- Whitening: Normalize 그리고 Decorrelate 를 모두 한다.

이 때 우리는 보통 $H = \text{Cov}(X^TX)$ 이라고 쓴다.
왜냐하면 선형 모델에서 hessian 이 이렇게 표현되기 때문이다.

$$
\mathcal{L}(w) = \| Xw-y\|_2 \\
\nabla^2_w \mathcal{L}(w) = X^TX
$$

그런데 covariance 값의 대각 성분을 지워야 하기 때문에 covariance 값을 구할 필요가 있고 Inverse 를 구해야 해서 매우 복잡하기 때문에 Normalization을 사용한다.

### 3.1. BN Algorithm

$$
\begin{aligned}
\mu_B &\leftarrow \frac{1}{m}\sum_{i=1}^{m} x_i
&& \text{// mini-batch mean} \\

\sigma_B^2 &\leftarrow \frac{1}{m}\sum_{i=1}^{m}(x_i-\mu_B)^2
&& \text{// mini-batch variance} \\

\hat{x}_i &\leftarrow \frac{x_i-\mu_B}{\sqrt{\sigma_B^2+\epsilon}}
&& \text{// normalize} \\

y_i &\leftarrow \gamma \hat{x}_i + \beta
\equiv \mathrm{BN}_{\gamma,\beta}(x_i)
&& \text{// scale and shift}
\end{aligned}
$$


이를 통해서 각 layer의 output이 mean 0 var 1 이 되도록 할 수 있다.


### 3.2. Why Batch Normalization works well

#### 1) 다음 activation과의 관계

sigmoid 와 같은 활성화 함수는 saturation regime 이 존재하며 값들이 saturation regime 에 많이 존재하는 것은 gradient propagation이 안되기 때문에 모델 학습에 안 좋다. 그렇다고 아예 없애기에는 non linearity를 추가할 필요가 있다. batch norm 을 사용하면 이 saturation regime 에 존재하는 값의 비율을 조절할 수 있다.

Batch Norm은 활성화 함수 전에 주로 사용하고 그 다음에 사용하는 경우는 많이 없다. 왜냐하면 ReLU와 같은 활성화 함수 같은 경우 0이하의 값들은 모두 0으로 만들지만, norm을 거치면 음수 성분들이 다시 생기기 때문에 이런 유의미한 정보들이 없어져버린다.

#### 2) Weight scailing 해도 결과가 같다.

$$
\text{BN}(Wn) = \text{BN}((aW)n)
$$

layer jacobian 이 weight scaling을 해도 달라지지 않는다. weight 이 크면 weight의 gradient가 작아진다.
즉,  weight scaling 을 바꾸는건 의미가 없기 때문에 유의미한 direction을 바꾸도록 유도한다.

#### 3) mean, variance도 변수 처럼 여겨서 normalize가 더 의미 있게 한다.

mean 이랑 variance 가 constant 처럼 여겨 질 경우 논리적인 문제가 발생한다.

$$
x_i = u_i - b, \ \  \hat{x_i} = x_i - \mu_B \\
\mu = \frac{1}{N}\sum_{i=1}^{N}(x_i)
$$

- 변수로 여겨질 경우

아래 그림과 같이 편미분이 계산된다.
![이미지](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT9aeHFWXHfDhJ_xzPneJlEsKVdu-tZ5vGoow&s)

여기서 주목할 것은 $\frac{\partial l}{\partial b} = 0$ 이라는 점이다
생각해보면 당연한게 $b+\Delta b$ 만큼 움직였다고 하자.
그렇다면,

$$
x_i = u_i+b+\Delta b \\
\hat{x}_i = u_i + b + \Delta b - \frac{1}{N} \sum_{i=0}^N (u_i + b + \Delta b) \\
=u_i + b - \frac{1}{N} \sum_{i=0}^N (u_i + b)
$$


- 상수로 여겨질 경우







---

## 4. How Does Batch Normalization Help Optimization?


Batch normalization 이 가장 먼저 소개된 논문이다. 
이 논문에서는 



1. How Does Batch Normalization Help Optimization?






- whitening이 효과적인것을 알지만 그것을 수행하기는 어렵다.
  - 들어가기에 whitening 은 입력값들의 상관관계를 모두 없앤 것이다.
    - feature 간의 상관관계, batch sample 간의 상관관계 모두 없앤것이다. 
  - whitening 을 수행하기 어려운 이유는 feature간의 covariance 행렬을 구하기 어렵기 때문이다.
    - feature 간의 covariance 행렬은 $dxd$ 이고 batch sample은 N개 라고 하자
      - 그러면 dxd 에 대한 sample을 N 개 밖에 못 만든다는 말이고
      - 그 covariance의 rank 가 N 보다 클 수가 없다
      - dxd 행렬의 inverse를 구할 수 없으므로 whitening이 불가능하다.


BatchNorm의 목적은 통계적 추정이 아니라 activation을 스케일링하는 것이라서, 1/(m−1) 대신 계산이 단순한 1/m을 사용해도 큰 문제가 없기 때문이다

### 왜 $N$이 아니라 $N-1$일까?

핵심은 평균 $\bar{X}$를 데이터로부터 추정했기 때문이다.

예를 들어 $N=2$라고 하자.

$X_1, X_2$가 있다.

평균을 빼면

$$
(X_1-\bar{X}) + (X_2-\bar{X}) = 0
$$

이 항상 성립한다.

즉, 두 개의 편차가 독립적으로 움직일 수 없다.

하나의 편차가 정해지면 다른 하나의 편차는 자동으로 결정된다.

따라서 자유도(degrees of freedom)는

$$
N
$$

이 아니라

$$
N-1
$$

이 된다.

일반적으로도

$$
\sum_{i=1}^{N}(X_i-\bar{X}) = 0
$$

이 항상 성립하므로, $N$개의 편차 중 $N-1$개만 자유롭게 선택할 수 있다.

따라서 분산을 추정할 때는 자유도 $N-1$을 사용하여

$$
S^2
=
\frac{1}{N-1}
\sum_{i=1}^{N}
(X_i-\bar{X})^2
$$

로 정의하며, 이렇게 해야 모분산 $\sigma^2$의 불편추정량(unbiased estimator)이 된다.

### 왜 분산이 작아지는가?

표본평균 $\bar{X}$는 데이터에 가장 잘 맞도록 선택된 값이다.

즉,

$$
\sum_{i=1}^{N}(X_i-c)^2
$$

를 최소로 만드는 $c$가 바로 $\bar{X}$이다.

따라서

$$
\sum_{i=1}^{N}(X_i-\bar{X})^2
$$

는 평균 $\mu$를 알고 있다고 가정했을 때의

$$
\sum_{i=1}^{N}(X_i-\mu)^2
$$

보다 체계적으로 작아진다.

그 이유는 평균을 데이터에 맞게 추정하면서 이미 오차를 최소화하도록 최적화했기 때문이다.

즉, 표본평균 $\bar{X}$를 사용하면 데이터의 변동성이 일부 흡수되어 편차 제곱합이 작아지며,

$$
\frac{1}{N}
\sum_{i=1}^{N}(X_i-\bar{X})^2
$$

는 모분산 $\sigma^2$를 과소추정하는 경향을 갖는다.

이를 보정하기 위해 분모를 $N$이 아닌 $N-1$로 나누어

$$
S^2
=
\frac{1}{N-1}
\sum_{i=1}^{N}(X_i-\bar{X})^2
$$

를 사용한다.


HowDoes Batch Normalization Help Optimization? (2018)


- 목차
  - 정규화는 왜 필요할까?
  - Batch Normalization

---

## 정규화는 왜 필요할까?
  딥러닝에서 발생하는 상황들 중 하나로 `vanishing/exploding gradient`가 있습니다.
  Layer 수가 적은 경우 문제가 심각하지 않지만, Layer 수가 많아질 수록 누적되어 나타나기 때문에  심각한 문제가 됩니다.

  * __vanshing/exploding gradient__
    이 현상이 발생하는 이유가 무엇일까요? 활성화 함수 중 하나인 Sigmoid 함수를 갖고 왔습니다.
    ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-20-sigmoid.PNG, "sigmoid")

    시그모이드 함수의 출력은 0과 1 사이로 제한되어, 입력의 절대값이 커질수록 출력은 0 또는 1로 수렴하게 됩니다.

    ``출력값이 수렴``한다 뜻은 ``미분값이 '0'`이라는 말과 같습니다.
    미분 값을 통해 학습되는 파라미터들은 학습 속도가 굉장히 느려지겠지요. 이 현상을 `vanishing gradient`이라고 합니다.

  네트워크 각 계층의 입력값들을 정규화 시켜 `saturation regeme`에 갇히지 않도록 하는 방법이 발표됩니다.
  이 벙법이 ``정규화 기법``으로 최근 거의 모든 딥러닝 모델에 사용되고 있습니다.


## Covariate Shift
  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-20-Covariate-Shift.PNG)
  입력의 분포에 따라 학습되는 결과가 다른데 이를 Covariate Shift라고 합니다.
  딥러닝에서는 하나의 계층의 출력이 다음 계층의 입력이 됩니다.
  이 값에 적용되는 Covatiate Shift를 `Internal Covariate Shift`라고 합니다.
  우리는 이 현상을 줄이기 위해 Batch Normalization을 이용합니다.

## Batch Normalization
  2015년에 vanishing gradient를 줄이기 위해 획기적인 방법 두개가 발표되는데 `BN(Batch Normalization)`과 `Residual Network`입니다. 우리는 이 중 BN에 대해 알이볼 것 입니다.

  전체 traing set을 분석할 수 없으며 미분 가능한 방법으로 normailzation을 선택했습니다.
  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-16-normailzation-function.PNG)
  각각의 feature 마다 따로 정규화 해주는 함수입니다.

  feature 마다 `decorrelate 시켜줄 수는 없지만`` 충분히 학습 속도를 향상 시킬 수 있습니다.

  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-16-batch-normalization.PNG)
  위 이미지에서 볼 수 있듯이 feature 마다 같은 평균 값을 이용해 정규화를 해줍니다.



  정규화를 해주므로써 `saturation regime`에 갇히지 않도록 해주었습니다.
  그러나 이 것만을 해주면 시그모이드 함수를 사용하는 의미가 없어집니다.
  평균값을 0, 분산을 1로 정규화된 데이터는 ``-1~1(66%), -2~2(99%)``사이에 데이터가 분포합니다.

  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-20-sigmoid.PNG, "sigmoid")
  비선형의 특성을 위해 활성화 함수를 사용하는데 -1과 1 사이에 선형적인 특성을 갖기 때문에
  활성화 함수를 사용하는 의미가 없어집니다.

  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-16-normailzation-function(2).PNG)
  이를 해결해주기 위해 BN에는 ``scaling parameter \gamma, shift parameter \Beta``를 사용해줍니다.


## Layer Normalization
  Batch Norm은 mini-batch의 모든 입력을 평균시켜 정규화를 진행합니다.
  즉, Batch의 ``모든 입력 값이 동일한 평균값을 공유``한다는 것입니다.

  예를 들어, 딥러닝을 통해 이미지를 처리하는 상황이라고 하겠습니다.
  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-21-image-RGB.PNG)
  `mini-batch의 사이즈는 100`, ``이미지 크기는 10x10``이라고 가정하겠습니다.
  `feature의 크기는 RGB 3`의 값을 갖겠지요.

  RGB 각각의 feature마다 정규화가 진행되며, 정규화 시 이용되는 평균값은 ``100x10x10 입력``을 평균해서 구해지게 됩니다. (mini-batch:100, image-size:10x10)

  이러한 방법은 순환구조가 있는 RNN에는 사용될 수 없습니다.
  RNN의 mini-batch를 생각해보겠습니다.
  vector_size:8 , mini-batch:100, step-size: 10 의 RNN 구조가 있습니다.
  이 RNN에 batch norm을 적용하기 위해서는 batch의 입력들을 이용해서 평균값을 구해주어야 합니다.
  그러나 생각해보면 RNN의 각각의 입력 값들은 모두 ``시간에 따라 달라지는 값``을 갖고 있습니다. 즉, 시간적인 특성인 포함된 값들입니다.
  모두 동일하게 평균화를 시켜버리면 이러한 특성을 모두 잃어버리게 됩겠지요.

  ```
  [참고] [https://stackoverflow.com/questions/45493384/is-it-normal-to-use-batch-ormalization-in-rnn-lstm]
  For RNNs, this means computing the relevant statistics over the mini-batch and the time/step dimension, so the normalization is applied only over the vector depths. This also means that you only batch normalize the transformed input (so in the vertical directions, e.g. BN(W_x * x)) since the horizontal (across time) connections are time-dependent and shouldn't just be plainly averaged.
    ```
  ```
  이 예에서 feature은 vector_size이다?
  각각의 토큰은 vector_size의 길이로 변환된다,
  이 토큰의 vector_size별로 normalize 시킨다는 개념이 RNN에서의 Batch Norm이다.
 
  그래서 RNN은 Layer normalization이라는 BN과는 조금 다른 방법으로 정규화를 시킵니다.

  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-16-batch_layer_norm.PNG)

  바로 모든 각각의 ``입력 값 마다 정규화`` 시켜주는 것입니다.

  각각의 입력값들을 vector_size 마다 정규화를 시켜주는 정규화를 `Layer Normalization`이라고 합니다.
```







[참고]

<https://yxxshin.github.io/2022/09/13/2022-09-13-Batch-Normalization-Backprop/>

[논문]
Batch Normalization: Accelerating Deep Network Training by, Reducing Internal Covariate Shift


