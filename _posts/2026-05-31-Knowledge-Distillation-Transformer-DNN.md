---
layout: post
title:  Distilling Transformers into DNNs, Is It Possible?
subtitle: "Knowledge Distillation"
categories: projects
tags: UAV Knowledge Distillation DeepLearning MachineLearning
comments: true
hidden: false
---

> Universal Approximation Theorem(UAT)에 따르면, 충분히 큰 신경망(MLP)은 어떤 연속 함수라도 근사할 수 있다. 즉 이론적으로는 Transformer가 구현하는 복잡한 함수도 DNN으로 표현하는 것이 가능하다.
> 하지만 실제 학습에서는 이야기가 다르다. MLP는 구조적인 inductive bias가 부족하기 때문에, 같은 함수를 표현할 수는 있어도 효율적으로 학습하기가 어렵다.
> 반면 Transformer는 self-attention 구조를 통해 입력 토큰 간 관계를 명시적으로 모델링하며, 이러한 inductive bias 덕분에 복잡한 sequence 문제를 훨씬 효율적으로 학습할 수 있다.
> 이 글에서는 이러한 관점에서 Transformer가 학습한 것을 DNN으로 distillation하는 것이 가능한지, 그리고 왜 단순하지 않은 문제인지 살펴본다.

## 1. UAT 관점에서는 DNN으로 모든 것을 표현할 수 있다

Universal Approximation Theorem(UAT)에 따르면, 충분히 큰 hidden layer를 가진 신경망은 임의의 연속 함수를 근사할 수 있다.
즉, 어떤 복잡한 함수 ( f(x) )에 대해서도 다음이 성립한다:

$$
f_{\text{DNN}}(x) \approx f(x)
$$

따라서 Transformer 역시 하나의 함수로 보면,

$$
f_{\text{DNN}}(x) \approx f_{\text{Transformer}}(x)
$$

는 이론적으로 가능하다.
즉 UAT 관점에서는 DNN으로 Transformer의 동작 자체를 표현하는 것에는 근본적인 제약이 없다.

---

## 2. 그런데 DNN은 inductive bias가 부족해 학습이 어렵다

하지만 UAT는 어디까지나 “표현 가능성”에 대한 정리이며, “학습 가능성”을 보장하지는 않는다.
일반적인 MLP(DNN)는 다음과 같은 한계를 가진다:

* 입력 간 구조적 관계를 명시적으로 모델링하지 않는다
* 모든 상호작용을 weight matrix에 암묵적으로 저장해야 한다
* 데이터 효율성이 낮고 일반화 성능이 떨어질 수 있다

즉, 표현은 가능하지만 학습은 비효율적이다

---

## 3. Transformer는 inductive bias 덕분에 더 효율적으로 학습된다

Transformer는 self-attention 구조를 통해 다음과 같은 연산을 수행한다:

$$
y_i = \sum_j \alpha_{ij} V_j
$$

이 구조는 다음과 같은 inductive bias를 제공한다:

* token 간 관계를 직접 계산
* input-dependent interaction 가능
* long-range dependency modeling 가능

즉 Transformer는 단순한 함수가 아니라, “어떻게 계산할 것인가”에 대한 구조적 prior를 함께 가진 모델이다
따라서 동일한 표현력을 가지더라도 DNN보다 훨씬 효율적으로 학습된다.

---

## 4. 그렇다면 Transformer가 학습한 feature를 DNN에 전달할 수 있을까?

여기서 자연스럽게 다음 질문이 등장한다:
***Transformer가 학습한 representation이나 function을 DNN에 distillation하면 DNN도 같은 역할을 수행하면서 더 빠른 inference를 할 수 있지 않을까?**

* 학습 단계: Transformer (효율적인 inductive bias 활용)
* 배포 단계: DNN (빠른 inference)

이러한 구조가 가능하지 않을까 하는 생각이다.

---

## 5. 하지만 distillation만으로는 완전히 해결되지 않는다

문제는 distillation이 전달할 수 있는 정보의 범위이다.
distillation은 기본적으로:

* input → output function
* 또는 일부 intermediate representation

을 전달하는 방법이다.
하지만 Transformer의 핵심 강점은 단순한 함수 자체가 아니라 다음과 같은 구조적 성질에 있다:

* attention 기반 dynamic routing
* token 간 pairwise interaction
* input-dependent computation graph

이러한 요소는 “결과 함수”라기보다 함수를 계산하는 방식 자체 (computation structure)에 해당한다.

---

## 6. 왜 DNN으로는 완전히 옮기기 어려운가?

MLP는 구조적으로:

* 입력이 하나의 fixed vector로 처리되고
* 모든 interaction이 고정된 weight 안에 압축되며
* 입력에 따라 computation graph가 변하지 않는다

반면 Transformer는 입력에 따라:

* 어떤 token이 어떤 token에 영향을 주는지가 달라지고
* interaction 구조 자체가 동적으로 생성된다

즉 MLP는 Transformer가 수행하는 “동적 연산 구조”를 직접 표현하지 못하고,
모든 경우를 weight 내부에 암묵적으로 암기해야 하는 형태가 된다

---

## 7. 결론

정리하면 다음과 같다:

* UAT 관점에서는 DNN으로 Transformer를 표현하는 것은 가능하다
* 그러나 inductive bias가 없기 때문에 직접 학습은 비효율적이다
* Transformer는 attention 구조 덕분에 효율적으로 학습된다
* distillation은 함수는 전달할 수 있지만 구조는 전달하지 못한다
* 따라서 Transformer → DNN 압축은 이론적으로 가능하지만 실용적으로는 제한적이다

결국 이 문제의 핵심은 다음이다:

> 표현력의 문제가 아니라, “어떻게 학습되는가”의 문제이다
