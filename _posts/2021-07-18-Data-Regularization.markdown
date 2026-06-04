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
|**[10][&nbsp;Layerwise&nbsp;Pretraining&nbsp;](#layerwise-pretraining-and-initialization)**|~~현재는 optimization 기법의 발전으로 인해 layer-wise pretraining 없이도 end-to-end 학습이 가능~~|
|**[11] [Architectural Regularization](#adversarial-training)**|Inductive bias를 잘 설계해서 모델의 hypothesis space와 optimization trajectory를 제한해 implicit regularization 효과 제공|
|**[12] [Label Smoothing](#label-smoothing)**| 모델을 soft label로 학습하여 출력 분포의 sharpness를 줄이고, 훈련 데이터에 대한 과도한 확신 완화   |
|**[13] [Batch Normalization](#batch-normalization)**|Batch Normalization은 layer 출력을 mini-batch 단위로 정규화하는 기법으로, 같은 입력이라도 batch 구성에 따라 출력이 달라지는 stochasticity가 발생하며 noise injection과 유사한 효과 제공 |

---

## Inductive Bias 란?
Regularization 기법들을 제대로 살펴보기 전에 그와 밀접하게 관련된 **inductive bias**라는 개념을 먼저 살펴본다.

**Inductive bias**란, 문제의 정답이 어떤 성질을 가진다고 가정하고, 그 가정을 반영해 모델을 설계하거나 더 적합한 loss function, regularization, optimizer 등을 사용하는 것이다. 이런 가정은 모델이 단순히 데이터에 맞는 해가 아니라, 우리가 가정한 구조에 부합하면서 더 잘 일반화되는 해를 찾도록 유도한다.
자세한 내용은 [Inductive Bias](https://ekspertos.github.io/projects/2021/07/20/Inductive-Bias/) 포스트를 참고하길 바란다.

---

## Weight Decay


---

## Noise Injection X

---

## DropOut X

---

## DropConnect X

---

## Ensemble X

---

## Data Augmentation

---

## Early Stopping X

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



