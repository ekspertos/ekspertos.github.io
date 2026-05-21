---
layout: post
title:  "Inductive Bias"
subtitle: "Inductive Bias"
categories: projects
tags: regularization DeepLearning MachineLearning Inductive Bias
comments: true
hidden: true
---

 
 > 딥러닝 모델은 입력과 출력 사이의 복잡한 관계를 학습할 수 있을 만큼 강력한 표현력을 가진다. 그러나 표현력이 충분히 크다는 것은 동시에 매우 많은 가능한 해답(solution)이 존재한다는 뜻이기도 하다. 만약 아무런 제약 없이 모델을 학습한다면, 모델은 문제를 푸는 데 필요한 일반적인 패턴이 아니라 학습 데이터에만 존재하는 우연한 특징(shortcut)에 의존할 수도 있다.
> 이때 중요한 개념이 바로 **inductive bias**이다. Inductive bias란 문제의 해답이 어떤 성질을 가질 것이라는 가정을 모델 구조나 학습 과정에 반영하여, 모델이 탐색할 수 있는 해 공간을 제한하는 것을 의미한다. 쉽게 말해, 모델이 “어떤 방식으로 문제를 풀 가능성이 높은가”에 대한 가정을 미리 심어두는 것이다.
> 이번 글에서는 inductive bias가 무엇인지, 그리고 다양한 딥러닝 모델들이 어떤 가정을 바탕으로 설계되었는지 살펴본다.




딥러닝 모델은 매우 복잡한 함수를 학습할 수 있으며, 충분한 표현력을 가진 모델은 동일한 학습 데이터를 설명하는 수많은 해답을 만들어낼 수 있다.  
그렇다면 모델은 이 많은 가능성 중 왜 특정한 해답을 선택하게 될까?

이 질문에 대한 중요한 개념이 바로 **inductive bias**이다.

**Inductive bias**는 문제의 해답이 어떤 성질을 가질 것이라는 가정을 모델 구조나 학습 과정에 반영하여, 가능한 해 공간을 제한하는 것을 의미한다. 다시 말해, 모델은 완전히 중립적인 상태에서 학습하는 것이 아니라, 특정한 가정을 바탕으로 더 선호하는 해답의 방향을 가지고 학습을 진행한다.

흥미로운 점은 우리가 익숙하게 사용하는 많은 딥러닝 모델들이 사실 이러한 inductive bias를 기반으로 설계되었다는 것이다. CNN은 이미지의 지역적 패턴(locality)을, RNN은 순차적 의존성(sequential dependency)을, Transformer는 입력 간 전역적인 관계(global interaction)의 중요성을 가정한다.

이번 글에서는 inductive bias가 무엇인지 살펴보고, 다양한 딥러닝 모델과 학습 기법들이 어떤 가정을 바탕으로 설계되었는지 알아보자.

## Inductive Bias
Regularization을 자세히 알아보기에 앞서, 기법들을 설명할 때 자주 등장하는 개념인 `inductive bias`를 먼저 알아보자.

**Inductive bias**란 문제의 해답이 어떤 성질을 가질 것이라는 가정을 모델 구조나 학습 과정에 반영하여, 가능한 해 공간을 제한하는 것을 의미한다.

예를 들어 이미지 분류 문제를 살펴보자.

### 1. CNN Inductive Bias

우리는 이미지 속에 어떤 객체가 존재하는지 알고 싶다.  
이때 객체가 이미지의 어디에 위치하는지는 크게 중요하지 않으며, 객체를 분류하기 위해서는 인접한 픽셀들의 패턴을 중요하게 살펴볼 필요가 있다.

![이미지](https://ekspertos.github.io/assets/img/review/2021-07-20-Translation-Invariance.png)

즉, 이미지 분류 문제의 해답은 인접한 픽셀들의 정보를 잘 파악하는 **locality** 성질과, 객체의 위치가 조금 변해도 같은 결과를 내야 하는 **translation invariance** 성질을 가질 것이라고 가정할 수 있다.

Convolutional Neural Network(CNN)는 이러한 inductive bias를 반영하여 설계된 모델이다.


### 2. RNN Inductive Bias

이번에는 음성 인식 문제를 생각해보자.

현재 시점의 정보는 이전 시점의 정보와 강한 연관성을 가지며, 시간적으로 가까운 정보일수록 더 중요한 경우가 많다. 또한 멀리 떨어진 정보일수록 그 영향력이 점차 감소할 것이라고 가정할 수 있다.

Recurrent Neural Network(RNN)는 이러한 순차적 관계(sequential dependency)에 대한 inductive bias를 반영하여 설계된 모델이다.


### 3. Transformer Inductive Bias

앞서 살펴본 CNN은 locality를 강하게 가정하는 모델이었다.

반면 Transformer는 가까운 정보뿐만 아니라 멀리 떨어진 정보 사이의 관계도 직접적으로 중요할 수 있다는 가정을 반영하여 설계된 모델이다. Self-attention 메커니즘을 통해 입력 간의 전역적인 관계(global interaction)를 학습할 수 있으며, 이를 통해 장거리 의존성(long-range dependency)을 효과적으로 다룰 수 있다.


지금까지 살펴본 예제들은 모델 구조에 inductive bias가 반영된 경우였다.

반면 inductive bias는 모델의 학습 과정에도 반영될 수 있으며, 대표적인 예시 중 하나가 바로 Weight Decay이다.

사실 CNN이나 RNN과 같은 모델들도 특정 inductive bias를 반영하여 설계된 구조였지만, 과거에는 이를 명시적으로 inductive bias 관점에서 설명하는 경우가 많지 않았다. 

그러나 `Relational Inductive Biases, Deep Learning, and Graph Networks` 논문이 등장하면서, 모델 구조 자체가 어떤 inductive bias를 반영하고 있는지에 대한 관점이 더욱 중요하게 다뤄지기 시작했다.

해당 논문에서는 다양한 딥러닝 모델들이 어떤 relational inductive bias를 기반으로 설계되었는지 설명하며, 특히 graph network가 가지는 inductive bias의 중요성을 다룬다. 관심이 있다면 한 번 읽어보는 것을 추천한다.

