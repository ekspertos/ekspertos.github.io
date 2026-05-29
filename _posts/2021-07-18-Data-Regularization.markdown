---
layout: post
title:  "Data Regularization"
subtitle: "Regularization"
categories: projects
tags: regularization DeepLearning MachineLearning
comments: true
hidden: false
---

 
> 딥러닝 모델은 입력과 출력 사이의 패턴을 학습하고 학습된 패턴을 기반으로 새로운 입력에 대해 적절한 출력을 예측한다.
> 그리고 다양한 입력에 대해 잘 일반화(generalization)하기 위해 다양한 상황를 포함한 충분한 데이터가 필요하다.
> 하지만 실제로 모든 상황을 고려한 입력을 수집하는 것은 불가능하기 때문에 제한된 입력으로만 학습이 되는 경우가 많다.
> 데이터가 부족 하면 모델은 사전에 접한 입력에만 출력을 예측하고 새로운 입력에 대해 제대로 응답을 예측 못할 수 있다. 
> 이러한 `과적합` (overfitting) 현상을 완화하기 위해 실제로는 모델 복잡도를 조절하거나 학습을 안정화하는 다양한 `정규화` (regularization) 기법들이 사용된다.
> 이번 포스트에서는 최근 모델에서 일반적으로 사용되는 다양한 정규화 기법들을 자세히 살펴본다.


이번 포스트에서는 `A survey of regularization strategies for deep models(2019)` 논문에서 다루는 정규화 기법들에 대해 자세히 알아본다.


 

|**기법**|**설명**|
|:-|--------------|
|**[1]  [Weight Decay](#weight-decay)**|로스 함수에 패널티 항을 추가해 모델 가중치들이 작아지도록 유도|
|**[2]  [Noise Injection](#noise-injection)**|입력에 무작위 노이즈를 추가해 작은 입력 변화나 잡음에 민감해지는 것을 방지 |
|**[3]  [DropOut](#dropout)**|학습때 뉴런을 랜덤하게 비활성해 뉴런 간 상호 의존도 감소시키는 방법으로 bagging과 유사한 ensemble 효과로 일반화 성능 향상|
|**[4]  [DropConnect](#dropconnect)**|뉴런을 비활성화하는 대신 뉴런 간에 연결하는 가중치를 랜덤하게 제거|
|**[5]  [Ensemble](#ensemble)**|다양한 모델의 출력을 평균내어 예측하는 방법으로 모델들 오차 간 공분산이 작을 수록 예측 성능 향상|
|**[6]  [Data Augmentation](#data-augmentation)**|데이터 변형으로 학습 데이터의 다양성 증가|
|**[7]  [Early Stopping](#early-stopping)**|Validation loss가 감소하다 증가하면 과적합의 신호로 보고 과적합이 발생하기 전에 학습 종료 |
|**[8]  [Adversarial Training](#adversarial-training)**| 입력의 제한된 변형에도 모델이 일관된 출력을 유지하도록 학습시켜 decision boundary를 더 매끄럽게 만들고 non-robust feature에 대한 의존 감소. |
|**[9]  [Multi-task Learning](#multi-task-learning)**|두 가지 이상의 task를 하나의 모델로 학습시켜 공통으로 유용한 representation을 학습하도록 유도하고, task-specific feature에 대한 과도한 의존 감소. |
|**[10][&nbsp;Layerwise&nbsp;Pretraining&nbsp;and&nbsp;Initialization&nbsp;](#layerwise-pretraining-and-initialization)**|~~현재는 optimization 기법의 발전으로 인해 layer-wise pretraining 없이도 end-to-end 학습이 가능~~|
|**[11] [Architectural Regularization](#adversarial-training)**|Inductive bias를 잘 설계해서 모델의 hypothesis space와 optimization trajectory를 제한해 implicit regularization 효과 제공|
|**[12] [Label Smoothing](#label-smoothing)**| 모델을 soft label로 학습하여 출력 분포의 sharpness를 줄이고, 훈련 데이터에 대한 과도한 확신 완화   |
|**[13] [Batch Normalization](#batch-normalization)**|Batch Normalization은 layer 출력을 mini-batch 단위로 정규화하는 기법으로, 같은 입력이라도 batch 구성에 따라 출력이 달라지는 stochasticity가 발생하며 noise injection과 유사한 효과 제공 |

---

## Inductive Bias
Regularization 기법들을 제대로 살펴보기 전에 그와 밀접하게 관련된 **inductive bias**라는 개념을 먼저 살펴본다.

**Inductive bias**란, 문제의 정답이 어떤 성질을 가진다고 가정하고, 그 가정을 반영해 모델을 설계하거나 더 적합한 loss function, regularization, optimizer 등을 사용하는 것이다. 이런 가정은 모델이 단순히 데이터에 맞는 해가 아니라, 우리가 가정한 구조에 부합하면서 더 잘 일반화되는 해를 찾도록 유도한다.
자세한 내용은 [Inductive Bias](https://ekspertos.github.io/projects/2021/07/20/Inductive-Bias/) 포스트를 참고하길 바란다.

---


## Weight Decay

딥러닝 모델을 학습하는 것은 우리가 모은 학습 데이터 $(x,y)$ 쌍에 대해 $y \cong f_\theta(x)$ 가 성립하는 함수 $f_\theta(x)$ 를 찾는 과정이다.

$$
\min_{\theta} \mathcal{L}(f_\theta(\bold{x}),\bold{y})
$$

모델 $f_\theta(x)$는 여러 레이어의 선형 변환 $\hat{\bold{x}} = W \bold{x} + \bold{b}$ 을 겹겹이 쌓은 형태로, 적절한 가중치 $\theta$를 찾는 것이 목적이다. 이 때 모델이 Sigmoid, ReLU 와 같은 활성화 함수를 레이어 중간중간 마다 둔다면, 모델은 비선형 함수를 표현할 수 있다.

예를 들어 XOR 함수를 모델로 표현하고 싶은 경우, $\hat{\bold{x}} = W \bold{x} + \bold{b}$ 의 선형 변환만으로 구할 수 있는 방법이 없다. 반면 ReLU 활성화 사용하면 $\hat{\bold{x}} = \text{ReLU}{(x_1-x_2)} + \text{ReLU}(x_2-x_1)$의 간단한 함수로 표현 가능하다.
이것을 2-레이어 모델로 표현할 수 있다.

- 1번째 레이어 (hidden layer):
$$
\hat{\bold{x}} = W_1 \bold{x} \ , \ \text{where} \ W_1 = \begin{bmatrix} +1 & -1 \\ -1 & +1\end{bmatrix}
$$
- ReLu 활성화 함수:
$$
\tilde{\bold{x}} = \text{ReLU}({\hat{\bold{x}}})
$$
- 2번째 레이어 (output layer):
$$
\bold{y} = W_2 \tilde{\bold{x}} \ , \ \text{where} \ W_2 = \begin{bmatrix} +1 & +1\end{bmatrix}
$$

이러하듯 2-레이어 모델을 사용하면 모든 함수를 근사할 수 있다. 이 개념을 **universal approximation theorem** 이라고 한다. 즉, 
$$
f_\theta(x) = W_\theta \sigma(W_{1} x)
$$ 

1. 비선형 함수를 학습하기 위해서 필요
2. 노이즈 있는 데이터를 학습하기 위해 필요
3. 변수 보다 더 적은 샘플이 있기 때문에 여러가지 해가 존재할 수 있는데 그중 하나를 찾아줌




그럼 선형일 때는 무조건 solution을 찾을 수 있는걸까?

이때 $f_\theta(x)$는 비선형 함수이고 non-convex 하기 때문에 trivial 하게 최적 값을 찾는 방법이 없다.
그렇기 때문에 딥러닝은 gradient descent를 사용해서 목적함수를 최소화하는 방식으로 가중치 $\theta$를 찾는다.
그러나 gradient descent는 global minimum을 찾는다는 확신이 없기 때문에 local minimum을 찾기도 한다.
이 때 여러개의 local minimum 이 있는데 이때 weight decay 는 local mumimum 중에서 계수 값을 작은 것을 사용하도록 유도하는 기법이다. 


내가 다음과 같은 constraint를 만족하는 $\theta$를 찾고 싶다고 하자.

$$
(\theta - \mu)^T A (\theta - \mu) = 1
$$

여기서 $\theta$는 우리가 선택하는 변수이고, $\mu$와 $A$는 시스템 또는 데이터로부터 주어지는 파라미터이다.  
이 constraint를 만족하는 $\theta$들의 집합은 ellipse 형태의 manifold를 형성한다.

즉, $\theta$ 자체가 ellipse인 것이 아니라, $\theta$가 속하는 feasible set이 ellipse 구조를 갖는 것이다:

$$
\{\theta \mid (\theta - \mu)^T A (\theta - \mu) = 1\}
$$

---

이때 $\mu$와 $A$에 작은 perturbation (noise)이 존재한다고 하자:

$$
\mu \rightarrow \mu + \epsilon_1, \quad A \rightarrow A + \epsilon_2
$$

그러면 constraint surface 자체가 다음과 같이 변형된다:

$$
(\theta - (\mu + \epsilon_1))^T (A + \epsilon_2) (\theta - (\mu + \epsilon_1)) = 1
$$

---

이러한 상황에서 가능한 해 $\theta$들 중에서 $\|\theta\|$가 작은 해를 선택하면,  
perturbation $\epsilon_1, \epsilon_2$에 대해 constraint value의 변화가 상대적으로 작아질 가능성이 있다.

즉, 다음 값의 민감도를 줄이는 방향으로 해를 선택하는 것이다:

$$
(\theta - (\mu + \epsilon_1))^T (A + \epsilon_2) (\theta - (\mu + \epsilon_1)) - 1
$$

---

따라서 small-norm solution을 선호하는 것은 noise에 의해 변형된 constraint surface에 대해 더 안정적인 해를 얻기 위한 inductive bias로 해석할 수 있다.








**Weight decay** 란 모델 가중치들이 작아지도록 유도하는 기법이다. 


란 모델 loss 함수에 패널티 항을 추가해 모델의 가중치들이 작아지도록 유도하는 기법이다. 
Inductive bias 관점에서 설명하면 **weight decay** 는 모델의 가중치들이 작도록 하는 inductive bias 를 유도하기 위해 사용하는 regularization 기법이다. 
원래의 gradient descent 식이 다음과 같다.
$$
\theta_{t+1} = \theta_t - \alpha \Delta f_t(\theta_t)
$$
이때 $\theta_t$ 는 $t$ 시점에서 가중치의 값, $\alpha$ 는 learning rate, 그리고 $f_t(\theta)$ 는 loss 함수이다. 한편 weight decay를 포함하면 다음과 같은 업데이트 식을 사용한다.
$$ 
\theta_{t+1} = (1-\lambda)\theta_t - \alpha \Delta f_t(\theta_t)
$$
여기서 $\lambda$ 는 decay rate 이라고 부르며 0과 1사이의 값을 갖는다. weight을 업데이트 할 때 weight 의 크기를 일정 비율만큼 감소시키기 때문에 weight가 비약적으로 커지는 것을 방지할 수 있다.


#### 1. Weight decay 기법이 어떻게 정규화에 도움이 될까?

모델의 가중치가 크다면 모델이 입력 변화에 매우 민감하게 반응해 약간의 노이즈가 포함되더라도 출력이 변할 수 있다. 이는 훈련데이터에 대한 모델이 오버피팅됨을 의미한다.

#### 2. SGD에서 weight decay와 L2 정규화의 관계는?

SGD의 업데이트 식은 앞서 살펴본대로 다음과 같다.
$$
\theta_{t+1} = \theta_t - \alpha \Delta f_t(\theta_t)
$$
그리고 L2 정규화를 포함한 로스 함수는 다음과 같다.
$$
f_t^{\text{L2}}(\theta_t) = f_t(\theta_t) + \frac{\lambda^{\text{L2}}}{2}||\theta_t||_2^{2}
$$
L2 정규화를 포함한 로스 함수에 SGD를 하면 다음과 같다.
$$
\begin{aligned}
\theta_{t+1} &= \theta_t - \alpha ( \Delta f_t(\theta_t) + \lambda^{\text{L2}} \theta_t ) \\ &= (1-\alpha \lambda^{\text{L2}} )\theta_t - \alpha \Delta f_t(\theta_t)
\end{aligned}
$$
즉, SGD 기법을 쓸 때 L2 정규화 기법이 weight decay 기법과 동일한 효과를 낸다는 것을 알 수 있다. 
이 때 $\lambda = \alpha \lambda^{\text{L2}}$ 이기 때문에 weight decay 기법이 L2 정규화 기법보다 더 flexible 하게 변수 값들을 조정할 수 있다. 이것은 weight decay에서 $\alpha$ 값이 gradient의 크기에만 영향을 미치기 때문이다.

그래서 어떤 논문들에서는 weight decay에 L1 정규화 기법을 포함하기도 한다. 

#### 3. 그렇다면 weight decay와 L1 정규화의 관계는?

L2 정규화는 수식적으로 weight decay와 관련이 크게 없다. 그러나 가중치를 줄인다는 관점에서 weight decay로 포함시키는 논문들도 있다.
$$
f_t^{\text{L1}} (\theta_t) = f_t(\theta) + \lambda^{\text{L2}} ||\theta_t||_1^{2}
$$

L1 정규화와 L2 정규화를 함께 사용하는 ElasticNet도 이와 같다.
$$
f_t^{\text{EN}} (\theta_t) = f_t(\theta) + (1-\lambda^{\text{EN}}) ||\theta_t||_1^{2} + \lambda^{\text{EN}}||\theta_t||_2^{2} 
$$

#### 4. L1 & L2 정규화 효과는?

L1 정규화와 L2 정규화를 적용했을 때 gradient 는 아래와 같다. 

$$
\Delta f_t^{\text{L2}}(\theta_t) = \Delta f_t(\theta_t) + \lambda^{\text{L2}}\theta_t
$$

$$
\Delta f_t^{\text{L1}}(\theta_t) = \Delta f_t(\theta_t) + \lambda^{\text{L1}}\Sigma_n \ \text{sign}(\theta_t^{(n)})
$$

수식에서 볼 수 있듯이 L2 정규화의 경우 가중치 내에 높은 값을 더 크게 감소하고 작은 값들은 작게 감소하도록 유도한다. 반면 L1 정규화는 높은 값이든 작은 값이든 같은 크기만큼 감소하게 유도한다.
그래서 L2 정규화는 가중치들을 평평하게 만드는 반면 L1 정규화는 작은 값들이 없어지면서 가중치들이 sparse 하게 분포하게 된다. 

<img src="https://ekspertos.github.io/assets/img/review/Regularization/L1L2WeightDecay.png" width="700">

그림에서 볼 수 있듯이 L1 정규화와 L2 정규화 모두 그 영역 내에서 가장 작은 값을 찾는 것을 볼 수 있다.
그리고 L2 정규화에 비해 L1 정규화에서 더 한 값이 더 크게 나오도록 유도되어 sparse 하게 나타나는 것을 볼 수 있다.

#### 4. Bayesian estimation 관점에서 L1 & L2 정규화 기법

Bayesian estimation 에서 사용하는 대표적인 방법이 ML estimation과 MAP estimation이다.
$$
\hat{x}_{\text{ML}} = \argmax_x p(y | x)
$$
$$
\hat{x}_{\text{MAP}} = \argmax_x  p(x | y)
$$
,
ML estimation은 관측된 데이터 $x$ 가 주어졌을 때,  
어떤 클래스 또는 parameter $\theta$ 가 그 데이터를 가장 잘 생성했는지를 찾는 방법이다.
즉 다음 값을 최대화한다.
$$
\arg\max_\theta P(x \mid \theta)
$$
반면 MAP estimation은  
관측된 데이터 $x$ 가 주어졌을 때 실제 parameter 또는 클래스가 $\theta$ 일 확률을 찾는 방법이다.
즉 다음 값을 최대화한다.
$$
\arg\max_\theta P(\theta \mid x)
$$
Bayes rule에 의해
$$
P(\theta \mid x)
=
\frac{P(x \mid \theta)P(\theta)}{P(x)}
$$
이므로 MAP estimation은 ML estimation에 prior probability $P(\theta)$ 를 추가로 고려한 방법이라고 볼 수 있다.
예를 들어 길이가 30cm 인 머리카락을 주웠을 때  
이 머리카락이 남성의 것인지 여성의 것인지 예측한다고 하자.

클래스를 다음과 같이 정의하자.
- 여성: $F$
- 남성: $M$
---

### ML Estimation
ML estimation은 다음 두 확률을 비교한다.
$$
P(x=30 \mid F)
$$
$$
P(x=30 \mid M)
$$
즉,
- 여성이 머리카락을 떨어뜨렸을 때 30cm 머리카락일 확률
- 남성이 머리카락을 떨어뜨렸을 때 30cm 머리카락일 확률
중 더 큰 값을 선택한다.
예를 들어
$$
P(30 \mid F)=0.4
$$
$$
P(30 \mid M)=0.1
$$
이라면 ML estimation은 여성이라고 판단한다.

---

### MAP Estimation

MAP estimation은 prior probability까지 함께 고려한다.
즉 다음 값을 비교한다.
$$
P(F \mid 30)
\propto
P(30 \mid F)P(F)
$$
$$
P(M \mid 30)
\propto
P(30 \mid M)P(M)
$$
예를 들어 어떤 환경에서
$$
P(F)=0.01
$$
$$
P(M)=0.99
$$
라면,
$$
0.4 \times 0.01 = 0.004
$$
$$
0.1 \times 0.99 = 0.099
$$
가 되어 MAP estimation은 남성이라고 판단할 수도 있다.


우리는 여기서 MAP estimation을 자세히 알아본다.

$$
\log (p(\theta|x))
$$

$$
p(\theta|x) \propto p(x|\theta)p(\theta)
$$

$$
\log p(\theta|x) = \log p(x|\theta) + \log p(\theta)
$$

즉 $\theta$ 가중치 값의 prior 확률이 gaussian 이라고 가정하고 MAP estimation을 한 것을 L2 정규화,
 $\theta$ 가중치 값의 prior 확률이 Laplacian 이라고 가정하고 MAP estimation을 한 것을 L1 정규화와 같은 의미를 가지는 것을 알 수 있다.



#### 5. 그렇다면 Adam Optimizer에서는 L2 Regularizer을 사용할까?

SGD에서의 weight decay는 모든 parameter에 동일한 decay coefficient를 적용하여  
각 weight를 현재 값에 비례해 감소시킨다.

반면 Adam에서는 adaptive learning rate 때문에  
regularization term 역시 parameter마다 서로 다른 scaling을 받게 된다.

따라서 Adam에서의 L2 regularization은  
SGD의 weight decay처럼 동일한 decay coefficient를 적용하는 방식과는 다르게 동작한다.

왜냐하면 Euclidean Space에서 최적화 되는게 아니고
Geometry를 바꾸고 최적화를 진행되기 때문에 
Isotrophic 하게 파라미터들이 감소하는 것이 아니라 
파라미터들이 서로 다른 크기로 줄어들기 때문에
보면 안된다.


그래서 원래와 같은 Loss에 penalty term을 추가하는 방식은 못쓰고
weight decay를 사용한다. 이게 adamW이다


#### 3. 그렇다면 모델 가중치들이 작아진다면 learning rate 을 높여주어야 할까?

Backpropagation 과정을 다시 생각해보면 모델의 가중치 값이 작을수록 backprop 되는 미분 전파 값이 작아진다. 그렇다면 lr을 높여주어야하지 않을까? 





---

## Noise Injection

---

## DropOut

---

## DropConnect

---

## Ensemble

---

## Data Augmentation

---

## Early Stopping

---

## Adversarial Training 

딥러닝 모델은 입력으로부터 다양한 특징(feature)을 추출하고, 이를 이용해 문제를 해결한다.
예를 들어 동물 분류 문제에서는 코, 귀, 입 모양과 같은 특징들이 유용하게 사용될 수 있다.
물론 딥러닝 모델이 사용하는 특징은 사람이 쉽게 인식할 수 있는 것에만 국한되지 않는다.
이미지의 질감(texture)처럼 사람이 크게 의식하지 않는 특징이나, 사람이 거의 인지하지 못하는 미세한 패턴까지도 분류에 도움이 된다면 학습에 활용할 수 있다.

이러한 특징들 중에는 입력이 조금만 변해도 다른 클래스로 인식될 정도로 민감한 것들이 있는 반면, 작은 변화에도 같은 클래스로 안정적으로 인식되도록 만드는 특징들도 있다.
예를 들어 같은 강아지라도 유전적 차이나 개체 차이로 인해 코 모양이 조금 작거나 입의 형태가 다를 수 있지만, 모델은 이러한 변동이 있어도 여전히 같은 강아지로 분류해야 한다.
이처럼 입력의 작은 변화에도 예측이 안정적으로 유지되도록 하는 특징들을 robust feature라고 한다.
반대로 입력이 아주 조금만 변해도 모델의 예측이 크게 달라지게 만드는 특징들은 non-robust feature라고 한다.

사람이 인식했을 때 동일한 라벨로 판단되는 입력이면서, 제한된 perturbation 집합 내에서 변형되었음에도 불구하고 모델의 오분류를 유도하는 입력을 adversarial example이라고 한다.

다시 말해 adversarial example은 입력 공간에서 작은(또는 제한된) 변형을 통해 `non-robust feature에 의존하는 모델의 취약성을 드러내는 것`이다.

그리고 Adversarial training은 adversarial example을 학습 과정에 포함시켜, 모델이 non-robust feature에 대한 의존도를 줄이고 보다 robust feature에 의존하도록 유도하는 방법이다.



---

## Multi-task Learning

- multi-task learning
- multi-objective learning
- multi-task optimization
- multi-expert (MoE)
- instruction tuning (LLM)
- multi-modal learning

으로 바뀜

두 가지 이상의 task를 하나의 모델로 학습시켜 
모델이 여러 task 에 공통으로 유용한 representation을 학습하도록 유도

여러 task에 대해 만족해야한다는 제약을 통해 task-specific feature에 대한 과도한 의존을 줄이고 일반화 성능 향상





Q: "정확도 감소 = 일반화 감소"?
A: 하나의 task 정확도가 약간 낮아질 수 있어도, 전체적으로 “더 일반화된 모델”이라고 말할 수 있다.

Multi-task learning에서는 특정 task의 성능이 다소 감소할 수 있지만, 여러 task를 동시에 만족하도록 제약함으로써 task-specific shortcut을 줄이고 더 일반화된 representation을 학습하기 때문에 전체적인 generalization은 향상될 수 있다.

✔️ 1. shared representation
여러 task 공통 구조 학습
✔️ 2. inductive bias 증가
“이 feature는 여러 task에 유용해야 한다”
✔️ 3. shortcut / spurious feature 억제
task-specific noise 감소

즉 “가능한 좋은 해”의 집합이 줄어듦. 이게 hypothesis space를 바꾼다는 뜻이다.


CNN:
“local feature를 쓰도록 구조적으로 제한”
MTL:
“shared feature를 쓰도록 학습 문제를 제한”



---

## Layerwise Pretraining and Initialization

---

## Architectural Regularization

---

## Label Smoothing

“확률 1까지 갈 필요는 없다” 라는 inductive bias를 준다.


1. Distillation 계열
   
Knowledge distilation을 특정 분류의 label smoothing이라고 볼 수 있다.
cat 이미지에서 dog probability는 조금 높고 차 확률은 낮게 하면서 sementic simillarity를 반영하도록 한다. -> generalization에 도움을 준다

Distilling the Knowledge in a Neural Network

1. Confidence Penalty
직접 entropy를 regularization 하자.

1. Mixup / Manifold Mixup
mixup: Beyond Empirical Risk Minimization


4. Calibration 연구
WhenDoes Label Smoothing Help? (2019)







---

## Batch Normalization

Batch Normalization은 각 layer의 activation을 mini-batch 단위로 정규화하여, 학습 과정에서 activation distribution의 변화를 줄이고 최적화를 안정화한다고 설명된다. 또한 weight scaling에 대해 일정한 불변성을 가지므로, 더 큰 learning rate을 사용할 수 있게 해준다.

초기 논문에서는 이러한 효과를 internal covariate shift 감소로 설명하였으며, 이는 이전 layer의 parameter 변화로 인해 다음 layer 입력 분포가 계속 변하는 현상을 의미한다. BatchNorm은 이를 줄임으로써 다음 layer가 보다 안정적인 분포에서 학습할 수 있게 만든다고 해석되었다.

그러나 이후 연구들은 BatchNorm의 효과가 internal covariate shift 감소보다는 optimization 관점에서 설명되어야 한다고 주장한다. 즉, BatchNorm은 loss landscape를 smoother하게 만들고 gradient의 Lipschitz constant를 감소시켜 gradient의 변동성을 줄이며, 결과적으로 더 안정적인 optimization을 가능하게 한다.

layer 의 activation을 mini-batch 단위로 정규화하여, 학습

batch 1 에서보면 A 로 보이고 batch 2 에서 보면 A'로 보인다 모델 입장에서는 입력에 noise가 섞인거 처럼 보인다

모델이 특정 activation에 의존하기 어렵다는 말이다.

같은 sample 이어도 다른 표현을 갖는다는 관점에서 augmentation 효과를 볼 수도 있다.


Batch Normalization은 layer 출력을 mini-batch 단위로 정규화하여 activation의 mean과 variance를 batch statistics에 의존하도록 만든다. 이로 인해 같은 input이라도 batch 구성에 따라 출력이 달라지는 stochasticity가 발생하며, 이는 모델 입장에서 noise injection 효과를 유도한다. 이러한 noise는 모델이 특정 activation 값에 과도하게 의존하지 않도록 만들어 implicit regularization 효과를 제공한다.


Batch Normalization은 layer 출력을 mini-batch 단위로 정규화하여 같은 input이라도 batch 구성에 따라 출력이 달라지는 stochasticity가 발생하며 이는 모델 입장에서 noise injection 효과를 유도

---



