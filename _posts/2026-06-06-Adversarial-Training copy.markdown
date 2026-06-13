---
layout: post
title:  "Adversarial Training"
subtitle: "Adversarial Training"
categories: projects
tags: blog github pages jekyll spacy
comments: true
hidden: false
---

> 딥러닝은 연구 단계를 넘어 우리 일상생활의 다양한 분야에 활용되고 있다. 대표적인 예로 자율주행, 문자 인식(OCR), 음성 인식, 스팸 탐지 등이 있으며, 이러한 기술들은 우리의 삶을 더욱 편리하게 만들어 주고 있다.
>하지만 딥러닝 모델이 잘못된 판단을 내릴 경우 그 영향은 단순한 오류를 넘어 심각한 사고로 이어질 수 있다. 예를 들어 도로에 출입금지 표지판이 있을 때 자율주행 시스템이 이를 올바르게 인식한다면 해당 도로로 진입하지 않을 것이다. 그러나 누군가 표지판에 작은 스티커를 붙이거나 특정한 패턴을 추가했을 때, 사람은 여전히 이를 출입금지 표지판으로 인식하지만 딥러닝 모델은 다른 표지판으로 오인식하거나 아예 인식하지 못할 수 있다. 그 결과 차량이 잘못된 경로로 진입하는 등의 문제가 발생할 수 있다.
> 이처럼 입력에 사람이 인지하기 어려운 작은 변화를 가해 모델의 예측을 크게 변화시키는 입력을 **adversarial example(적대적 예제)** 이라고 한다. 이번 포스트에서는 현실 세계에서 발생할 수 있는 adversarial example의 사례를 살펴보고, 이러한 현상이 왜 발생하는지 그리고 이를 방어하기 위한 방법에는 무엇이 있는지 알아본다.


---

## Real-World Examples of Adversarial Attacks

딥러닝 모델은 다양한 실제 시스템에 적용되고 있으며, adversarial attack은 더 이상 이미지 분류기의 장난감 문제가 아니다. 자율주행, 얼굴 인식, 음성 인식, 추천 시스템, 대규모 언어 모델(LLM) 등 실제 서비스에서도 adversarial example이 연구되고 있다.

대표적으로 자율주행 시스템은 도로 표지판 인식, 차선 인식, 보행자 탐지 등 다양한 컴퓨터 비전 기술에 의존한다.

실제로 *Robust Physical-World Attacks on Deep Learning Models*에서는 정지 표지판에 검은색과 흰색 스티커를 부착하여 객체 인식 모델이 이를 다른 표지판으로 오인하도록 만드는 공격을 제안하였다. 연구진은 실제 도로 환경에서 촬영한 영상에서도 공격이 성공함을 보였으며, 이동 중인 차량이 촬영한 프레임의 상당수에서 오인식이 발생함을 보고하였다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_StopSign.jpg" width="400">

또 다른 연구인 *Simple Physical Adversarial Examples against End-to-End Autonomous Driving Models*에서는 도로 위에 단순한 검은 선 몇 개를 추가하는 것만으로도 차량의 조향을 교란할 수 있음을 보였다. 연구 결과, 이러한 패턴은 차선 이탈이나 충돌을 유발할 수 있었으며, 특히 우회전 구간에서 높은 공격 성공률을 보였다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_Lane.jpg" width="400">

자율주행뿐만 아니라 얼굴 인식 시스템에서도 adversarial attack이 가능하다. *Accessorize to a Crime: Real and Stealthy Attacks on State-of-the-Art Face Recognition*에서는 adversarial 패턴이 포함된 안경을 제작하여 얼굴 인식 시스템이 공격자를 다른 사람으로 인식하도록 만드는 공격을 시연하였다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_FaceRecognition.jpg" width="400">

이와 유사하게 *Real-World Adversarial Attack on ArcFace Face ID System (AdvHat)*에서는 모자에 부착한 작은 패턴만으로도 얼굴 인식 시스템이 사용자를 올바르게 인식하지 못하도록 만들 수 있음을 보였다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_AdvHat.jpg" width="400">

이러한 공격은 공항 출입국 심사, 회사 출입 통제, 스마트폰 Face ID와 같은 실제 인증 시스템에 영향을 줄 수 있기 때문에 큰 관심을 받고 있다.

얼굴 인식뿐만 아니라 일반적인 객체 인식 모델 역시 취약하다. *Adversarial Patch*에서는 이미지의 일부에 작은 패치를 부착하는 것만으로도 객체 인식 모델이 특정 클래스로 오인하도록 만들 수 있음을 보였다. 즉, 공격자는 전체 이미지를 수정하지 않고도 모델의 예측을 조작할 수 있다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_Patches.jpg" width="400">

Computer Vision 분야뿐만 아니라 음성 인식 분야에서도 adversarial example이 연구되고 있다. *Practical Hidden Voice Attacks against Speech and Speaker Recognition Systems*에서는 사람이 거의 이해하지 못하는 음성을 생성했음에도 음성 인식 시스템은 이를 정상적인 명령으로 인식할 수 있음을 보였다. 실제 예시는 해당 연구의 [데모 페이지](https://sites.google.com/view/practicalhiddenvoice/home)에서 확인할 수 있다.

이러한 사례들은 adversarial example이 특정 연구 환경에만 존재하는 현상이 아니라 실제 시스템에서도 문제가 될 수 있음을 보여준다. 또한 추천 시스템에서는 특정 콘텐츠가 추천 결과 상위에 노출되도록 유도하는 shilling attack과 poisoning attack이 연구되고 있으며, 최근에는 ChatGPT, Claude, Gemini와 같은 대규모 언어 모델을 대상으로 한 Prompt Injection, Jailbreak, Indirect Prompt Injection 등의 공격도 활발하게 연구되고 있다.

---

## Adversarial Examples related papers

그렇다면 adversarial example은 어떻게 생성되는 것일까? 또한 왜 딥러닝 모델은 이러한 입력에 취약하며, 이를 방어하기 위해 어떤 연구들이 제안되었을까?
Adversarial example 연구는 공격 기법의 발전과 방어 기법의 발전이 반복적으로 이루어지며 성장해 왔다. 이번 포스트에서는 adversarial example 연구의 흐름을 이해하기 위해 해당 분야의 대표 논문들을 시간순으로 살펴본다.

1. **Explaining and Harnessing Adversarial Examples (2014) — 20.6k citations**

   Adversarial example 연구를 대중화한 대표 논문으로, 딥러닝 모델의 선형성이 adversarial example의 원인이라고 주장하였다. 또한 `FGSM(Fast Gradient Sign Method)과 Adversarial Training의 초기 형태`를 제안하였다.

2. **Towards Evaluating the Robustness of Neural Networks (2017) — 13.5k citations**

   강력한 공격 기법인 `C&W Attack을 제안`하고 당시 널리 사용되던 Defensive Distillation을 무력화하였다. 이후 수년간 adversarial defense를 평가하는 대표적인 공격 방법으로 사용되었다.

3. **Towards Deep Learning Models Resistant to Adversarial Attacks (2017) — 19.6k citations**

   Adversarial robustness를 Robust Optimization 문제로 해석하고 `PGD Adversarial Training을 제안`하였다. 현재까지도 가장 대표적인 adversarial defense 기법 중 하나로 평가받고 있다.

4. **Adversarial Patch (2018) — 1.8k citations**

   이미지의 일부에 작은 패치를 부착하는 것만으로 모델을 속일 수 있음을 보였다. Adversarial example 연구를 디지털 환경에서 `현실 세계의 물리적 공격으로 확장`한 대표 논문이다.

5. **Certified Robustness to Adversarial Examples with Differential Privacy (2018) — 1.4k citations**

   Differential Privacy를 활용하여 특정 범위의 입력 변화에 대해 예측이 변하지 않음을 수학적으로 보장하고자 하였다. `Certified Robustness 연구` 분야의 초기 대표 논문 중 하나이다.

6. **Constructing Unrestricted Adversarial Examples with Generative Models (2018) — 0.4k citations**

   `GAN을 이용하여 사람이 보기에도 자연스러운 adversarial example을 생성`하였다. 이를 통해 adversarial attack이 반드시 작은 노이즈일 필요는 없음을 보여주었다.

7.  **Obfuscated Gradients Give a False Sense of Security (2018) — 4.4k citations**
   
    당시 제안된 많은 방어 기법들이 실제 강건성을 제공하는 것이 아니라 `gradient를 숨기고 있을 뿐임을 지적`하였다. 이후 adversarial defense를 평가할 때 반드시 인용되는 대표 논문이 되었다.

8.  **Adversarial Examples Are Not Bugs, They Are Features (2019) — 2.7k citations**

    Adversarial example이 단순한 모델의 버그가 아니라 `모델이 학습한 non-robust feature의 결과라고 주장`하였다. 인간과 모델이 서로 다른 특징을 활용한다는 관점을 제시하며 adversarial example의 원인을 새롭게 해석하였다.


---


## 1. Explaining and Harnessing Adversarial Examples (2014) — 20.6k citations

### 1.1. Introduction

입력에 작은 변화를 가했음에도 출력이 크게 변하는 현상은 기존에 모델의 비선형성(non-linearity)이나 과적합(overfitting)에 의해 발생하는 것으로 설명되곤 하였다. 그러나 본 논문은 이러한 현상이 오히려 딥러닝 모델의 **선형성(linearity)** 에서 비롯된다고 주장한다. 저자들은 고차원 공간에서는 매우 작은 perturbation이라도 각 차원에서 누적되어 큰 변화를 일으킬 수 있으며, 이로 인해 adversarial example이 발생한다고 설명한다.

또한 adversarial perturbation은 특정 모델에만 국한되지 않고 여러 모델 사이에서 공유되는 **transferability** 특성을 가진다. 즉, 하나의 모델을 공격하기 위해 생성한 adversarial example이 다른 모델에 대해서도 높은 확률로 공격에 성공할 수 있음을 보였다. 이러한 특성은 공격자가 대상 모델의 구조나 파라미터를 알지 못하는 black-box 환경에서도 adversarial attack이 가능함을 보인다.

논문의 주요 기여(contributions)는 다음과 같이 정리할 수 있다:

1. 딥러닝 모델의 비선형성(non-linearity)이 아니라, 고차원 공간에서의 **선형성(linearity)** 이 adversarial example의 주요 원인임을 주장.
2. 기존의 L-BFGS 기반 공격보다 빠르고 효율적인 **Fast Gradient Sign Method (FGSM)** 를 제안.
3. 학습 과정에서 adversarial example을 함께 사용하여 모델의 강건성(robustness)을 향상시키는 **Adversarial Training** 소개.


---

### 1.2. The Linear Explaination of Adversarial Examples 

논문에서는 adversarial example이 딥러닝 모델의 비선형성 때문이 아니라, 오히려 고차원 공간에서의 선형성(linearity) 때문에 발생한다고 주장한다. 이를 설명하기 위해 저자들은 선형 모델을 가정하고 다음과 같은 식을 고려한다.

$$
\mathbf{w}^T\tilde{\mathbf{x}}
=
\mathbf{w}^T\mathbf{x}
+
\mathbf{w}^T\boldsymbol{\eta}
$$

여기서 $\boldsymbol{\eta}$는 입력에 추가되는 작은 perturbation이다. 비록 각 원소의 크기는 매우 작더라도, 고차원 공간에서는 이러한 변화가 여러 차원에 걸쳐 누적된다.
예를 들어 입력 차원이 $n$이고 perturbation의 각 원소 크기가 최대 $\epsilon$이라고 하자. 또한 가중치의 평균 절댓값을 $m$이라고 정의하자. 
이 경우 perturbation이 출력에 미치는 영향은 다음과 같이 근사할 수 있다.
$$
\mathbf{w}^T\boldsymbol{\eta} \approx \epsilon mn
$$

즉, 개별 차원에서는 거의 눈에 띄지 않는 작은 변화라도 고차원 공간에서는 누적되어 모델의 출력을 크게 바꿀 수 있다. 저자들은 이것이 adversarial example이 발생하는 주요 원인이라고 설명한다.

그리고 이러한 출력 변화를 최대화하기 위해서는 perturbation을 가중치와 같은 방향으로 설정하면 된다. 

$$
 \boldsymbol{\eta} = \epsilon \cdot \text{sign}(\mathbf{w})
$$

이러한 아이디어를 실제 딥러닝 모델에 적용한 공격 기법이 **FGSM**이다.
FGSM은 가중치 벡터 $\mathbf{w}$ 의 부호를 사용하는 대신, 입력에 대한 손실 함수의 gradient 부호를 perturbation 방향으로 사용한다.

$$
\boldsymbol{\eta}
=
\epsilon
\cdot
\operatorname{sign}
\left(
\nabla_{\mathbf{x}}
J(\boldsymbol{\theta},\mathbf{x},y)
\right)
$$

즉, 입력을 손실 함수가 가장 빠르게 증가하는 방향으로 이동시켜 모델이 오분류하도록 만든다.
그렇다면 출력 변화를 최대화하려면 단순히 가중치의 부호를 사용하면 될 것 같은데, *왜 FGSM은 weight의 sign 대신 gradient의 sign을 사용하는 것일까?*


<img src="https://ekspertos.github.io/assets/img/review/Adversarial/FGSM.jpg" width="400">

이를 이해하기 위해 가장 단순한 선형 분류기인 logistic regression을 살펴보자.
Logistic regression 모델은 다음과 같이 정의된다.

$$
P(y=1)=\sigma(\mathbf{w}^{T}\mathbf{x}+b)
$$

여기서 $\sigma(x)$ 는 시그모이드 함수이다. 또한 라벨을 $y\in\{-1,+1\}$로 표현하면 logistic regression의 기대 손실 함수는 다음과 같이 나타낼 수 있다.

$$
\mathbb{E}_{\mathbb{x},y \sim p_{\text{data}} } \zeta(-y(\mathbb{w}^T \mathbb{x} + b))
$$

여기서 $\zeta(z) = \log (1+\exp(z))$ 는 softplus 함수이다.


위 식이 어떻게 유도되는지 살펴보자. 먼저 $y\in\{-1,+1\}$일 때 조건부 확률은 다음과 같이 표현할 수 있다.

$$
P(y|x) = \sigma(y(\mathbb{w}^T \mathbb{x} + b)) 
$$

이는 시그모이드 함수가 $\sigma(x) + \sigma(-x) = 1$ 를 만족하기 때문이다.
따라서 **negative log-likelihood loss**는 아래와 같이 정의된다.


$$
\mathcal{L}(\mathbf{w},\mathbf{x},y)
=
-\log P(y|\mathbf{x})
=
-\log
\sigma\!\left(
y(\mathbf{w}^{T}\mathbf{x}+b)
\right)
$$

그리고 Sigmoid 함수의 정의를 이용하면 위 식은 아래와 같이 정리된다.

$$
\mathcal{L}(\mathbf{w},\mathbf{x},y)
=
\log\!\left(
1+e^{-y(\mathbf{w}^{T}\mathbf{x}+b)}
\right)
=
\zeta\!\left(
-y(\mathbf{w}^{T}\mathbf{x}+b)
\right)
$$

이제 입력 $\mathbf{x}$에 대한 gradient를 계산해 보자. 로그 함수의 미분 공식 $[\ln f(x)]' = \frac{f'(x)}{f(x)}$ 을 이용하면 다음을 얻을 수 있다.

$$
\nabla_{\mathbf{x}}
\mathcal{L}(\mathbf{w},\mathbf{x},y)
=
\frac{
e^{-y(\mathbf{w}^{T}\mathbf{x}+b)}
}{
1+e^{-y(\mathbf{w}^{T}\mathbf{x}+b)}
}
(-y\mathbf{w})
$$

이 식에 $\operatorname{sign}$을 취하면 아래와 같다.

$$
\begin{aligned}
\operatorname{sign}
\!\left(
\nabla_{\mathbf{x}}\mathcal{L}
\right)
&=
\operatorname{sign}
\!\left(
\frac{
e^{-y(\mathbf{w}^{T}\mathbf{x}+b)}
}{
1+e^{-y(\mathbf{w}^{T}\mathbf{x}+b)}
}
(-y\mathbf{w})
\right) \\
&=
\operatorname{sign}(-y\mathbf{w}) \\
&=
-y\,\operatorname{sign}(\mathbf{w})
\end{aligned}
$$

이는 지수 함수의 출력과 분모가 항상 양수이므로 gradient의 부호가 $-y\mathbf{w}$에 의해서만 결정되기 때문이다.

결과적으로 logistic regression에서는 gradient의 부호를 구하는 것이 가중치의 부호를 구하는 것과 본질적으로 동일하다는 것을 보였다. 
즉, 클래스 라벨 $y$에 따라 전체 방향만 반전될 뿐, **FGSM이 사용하는 gradient의 sign은 weight의 sign과 같은 방향 정보를 담게된다.**


### 1.3. Adversarial Training of Linear Models Versus Weight Decay

**그렇다면 adversarial training은 어떻게 이루어질까?** 가장 간단한 방법은 원본 입력 $\mathbf{x}$ 대신 adversarial perturbation이 추가된 입력 $\tilde{\mathbf{x}}=\mathbf{x}+\boldsymbol{\eta}$ 를 사용하여 학습하는 것이다. 
그러면 loss 함수는 다음과 같이 정의된다.

$$
\mathcal{L}(\mathbf{w},\tilde{\mathbf{x}},y)
=
\zeta\!\left(
-y(\mathbf{w}^{T}\tilde{\mathbf{x}}+b)
\right)
$$

앞서 logistic regression의 경우 FGSM perturbation이 $\boldsymbol{\eta} = -\epsilon y\,\operatorname{sign}(\mathbf{w})$ 로 표현됨을 보였다. 
또한 $\|\mathbf{w}\|_1=\mathbf{w}^{T}\operatorname{sign}(\mathbf{w})$ 이므로 다음 관계를 얻을 수 있다.

$$
\begin{aligned}
y(\mathbf{w}^{T}\tilde{\mathbf{x}}+b)
&=
y\!\left(
\mathbf{w}^{T}
(\mathbf{x}-\epsilon y\,\operatorname{sign}(\mathbf{w}))
+b
\right)
\\
&=
y(\mathbf{w}^{T}\mathbf{x}+b)
-\epsilon
\mathbf{w}^{T}
\operatorname{sign}(\mathbf{w})
\\
&=
y(\mathbf{w}^{T}\mathbf{x}+b)
-\epsilon\|\mathbf{w}\|_1 .
\end{aligned}
$$

따라서 adversarial example에 대한 loss는 아래와 같이 쓸 수 있다.

$$
\mathcal{L}(\mathbf{w},\tilde{\mathbf{x}},y)
=
\zeta\!\left(
\epsilon\|\mathbf{w}\|_1
-y(\mathbf{w}^{T}\mathbf{x}+b)
\right).
$$

이 식은 $L^1$ regularization과 매우 유사한 형태를 가진다. 실제로 $\epsilon\|\mathbf{w}\|_1$ 항이 추가적인 penalty 역할을 수행하기 때문이다. 하지만 중요한 차이점이 존재한다. $L^1$ regularization에서는 penalty가 loss에 직접 더해지는 반면, adversarial training에서는 penalty가 activation 내부에서 차감된다.

Weight decay의 경우 모델이 최적의 해를 찾더라도 regularization 항은 항상 loss에 남아 있다. 반면 adversarial training에서는 모델이 충분히 좋은 예측을 하게 되면 softplus 함수의 입력이 큰 음수가 되어 loss 값이 0에 가까워진다. 따라서 학습이 진행됨에 따라 penalty의 효과가 자연스럽게 감소할 수 있다.

이러한 차이는 multiclass 분류 문제에서 더욱 직관적으로 이해할 수 있다. 예를 들어 고양이 이미지를 분류하는 상황을 생각해 보자. 현재 모델이 고양이를 주로 개와 혼동한다고 하자. Adversarial training은 이러한 실제 취약성을 반영하여 고양이와 개를 구분하는 방향에 집중적으로 penalty를 부여한다. 즉, 모델이 실제로 실수하기 쉬운 방향에 대해서만 robust하도록 학습된다.

반면 weight decay는 어떤 방향이 실제로 취약한지 알지 못한다. 모든 가중치의 크기를 일괄적으로 감소시키기 때문에 결과적으로는 고양이와 개를 구분하는 방향뿐 아니라 고양이와 자동차, 고양이와 비행기처럼 현재 분류에 큰 영향을 주지 않는 방향까지 함께 억제하게 된다.

물론 두 방법을 완전히 동일한 것으로 볼 수는 없다. 하지만 adversarial 관점에서 해석하면 weight decay는 실제 취약한 방향만 고려하는 것이 아니라 모든 방향이 잠재적으로 adversarial direction이라고 가정하는 것으로 볼 수 있다. 다시 말해 **weight decay는 adversarial perturbation의 영향을 과대평가하는 보다 보수적인 regularization이라고 해석**할 수 있다.

그 결과 **weight decay의 계수를 지나치게 크게 설정하면 실제로 중요하지 않은 방향까지 함께 억제하게 되어 underfitting이 발생**할 수 있다. 반면 adversarial training은 실제로 취약한 방향에 대해서만 penalty를 부여하며, 모델의 예측이 충분히 좋아지면 그 효과가 자연스럽게 감소한다. 따라서 **adversarial training은 상대적으로 더 큰 $\epsilon$ 값을 사용하더라도 학습이 가능**하다.


### 1.4. Why Do Adversarial Examples Generalize?

논문에서는 FGSM 방향으로 입력을 이동시키면서 $\epsilon$ 값을 점차 증가시켰을 때 모델의 출력이 어떻게 변하는지 분석한다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/FGSM_FinePocket2.jpg" width="400">

실험 결과, $\epsilon$ 값이 증가함에 따라 각 클래스의 softmax score는 거의 선형적으로 증가하거나 감소하는 모습을 보였다. 이는 FGSM이 찾은 방향을 따라 이동할 때 모델이 국소적으로 선형에 가까운 동작을 한다는 것을 의미한다. 다시 말해, adversarial example은 매우 복잡한 비선형 구조에 의해 발생하는 것이 아니라 모델의 선형적인 특성에 의해 설명될 수 있다는 것이다.

이러한 결과는 adversarial example이 왜 서로 다른 모델 사이의 전이성(transferability)을 설명하는데 중요한 단서를 제공한다.

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/FGSM_FinePocket.jpg" width="400">

만약 adversarial example이 고차원 공간에서의 복잡한 비선형성에 의해 생성된 `fine pocket` 때문이라면, $\epsilon$을 증가시키면서 이동할 때 공격 성공 영역과 실패 영역이 반복적으로 나타나야 한다. 즉, softmax score가 감소했다가 다시 증가하는 등 매우 불규칙한 변화를 보여야 한다.

반면 실제 실험에서는 이러한 현상이 관찰되지 않았다. FGSM 방향을 따라 이동할수록 softmax score는 비교적 일정한 기울기로 증가하거나 감소하였다. 이는 **adversarial example이 매우 좁고 복잡한 pocket에 존재하는 것이 아니라, 비교적 넓은 영역에 걸쳐 분포하고 있음**을 말한다.

따라서 adversarial example은 특정한 점에서만 발생하는 현상이 아니라 넓은 subspace에 걸쳐 존재한다고 볼 수 있다. **이러한 구조는 fine pocket과 같은 국소적인 구조보다 서로 다른 모델에 의해 일관되게 학습될 가능성이 높다**. 특히 서로 다른 모델이 동일한 데이터 분포로부터 **유사한 decision boundary를 학습한다면, adversarial region 역시 비슷한 형태로 형성될 수 있다**. 그 결과 한 모델에서 생성한 adversarial example이 다른 모델에서도 동작하는 transferability가 발생한다고 해석할 수 있다.


[[Adversarial example using FGSM](https://colab.research.google.com/github/tensorflow/docs/blob/master/site/en/tutorials/generative/adversarial_fgsm.ipynb)] 에서 FGSM 기법으로 adversarial example을 생성하는 코드를 실습할 수 있다. 

<img src="https://ekspertos.github.io/assets/img/review/Adversarial/FGSM_ColabImage.jpg" width="500">

---
---

### 2. Towards Evaluating the Robustness of Neural Networks (2015) — 13.5k citations


Defencive distillation은 softmax 함수에 temperture을 첨가해서 학습을 하는 기법이다.


$$
\operatorname{softmax}(x, T)_i = \frac{e^{x_i/T}}{e^{\sum_j x_j/T}}
$$

1. Temperature을 T로 설정해서 teacher network 훈련.
2. 훈련된 teacher network에서 soft label을 생성.
3. Temperature을 T로 하여 soft label로 distilled network 훈련 
4. T를 1로 해서 Test 진행

근데 왜 


<img src="https://ekspertos.github.io/assets/img/review/Adversarial/Softmax_Backpropagation.jpg" width="500">








---
---

### 3. Towards Deep Learning Models Resistant to Adversarial Attacks (2017) — 19.6k citations

Adversarial example을 생성하는 가장 대표적인 방법으로 FGSM이 있다. 그러나 FGSM은 gradient 방향으로 단 한 번만 이동하기 때문에 주어진 perturbation budget 내에서 가장 강력한 adversarial example을 찾는다고 보장할 수 없다. 즉, 동일한 $\epsilon$-ball 내부에 더 강한 adversarial example이 존재하더라도 이를 발견하지 못할 수 있다.

이를 해결하기 위해 기존에는 FGSM을 여러 번 반복 적용하는 Iterative FGSM(BIM)이 제안되었다. Madry 등의 연구에서는 이를 더욱 일반화하여, 매 step마다 gradient 방향으로 이동한 뒤 허용된 perturbation 범위를 벗어나면 다시 $\epsilon$-ball 내부로 projection하는 Projected Gradient Descent (PGD) 공격을 사용하였다.

또한 본 논문은 adversarial robustness를 다음과 같은 min-max optimization 문제로 정식화하였다.

$$
\min_{\theta}
\mathbb{E}_{(x,y)\sim\mathcal{D}}
\left[
\max_{\|\delta\|\le\epsilon}
L(x+\delta,y;\theta)
\right]
$$

내부의 maximization은 현재 모델을 가장 잘 속이는 adversarial perturbation $\delta$를 찾는 과정이고, 외부의 minimization은 이러한 최악의 공격에 대해서도 loss를 최소화하도록 모델을 학습하는 과정이다.

즉, 모델이 단순히 일반 이미지에 대해서만 잘 동작하도록 학습하는 것이 아니라, 주어진 $\epsilon$ 범위 내에서 발생할 수 있는 최악의 adversarial example에 대해서도 올바르게 분류하도록 학습하는 것이다.

이러한 PGD adversarial training은 이후 adversarial robustness 연구의 대표적인 baseline으로 자리잡았으며, 현대 adversarial training 연구의 출발점이 되었다.




---
---

### 4. Adversarial Patch (2018) — 1.8k citations

예시 논문

---
---


### 5. Certified Robustness to Adversarial Examples with Differential Privacy (2018) - 1.4k citations

Certified Robustness 논문


---
---

### 6. Constructing Unrestricted Adversarial Examples with Generative Models (2018) — 0.4k citations

GAN으로 adversarial example을 생성

그렇기 때문에 restricted bound를 사용 안함


---
---

### 7. Obfuscated Gradients Give a False Sense of Security (2018) — 4.4k citations

gradient를 숨기는 obfuscated gradent를 사용하는 것은 제대로된 robust model을 만드는 것이 아니라 꼼수를 사용하는거다 

---
---


### 8. Adversarial Examples Are Not Bugs, They Are Features (2019) — 2.7k citations

Adversarial example이 발생하는 것은 이미지에 non-robust feature 때문이다.

---
---

### 9. Reliable Evaluation of Adversarial Robustness with an Ensemble of Diverse Parameter-free Attacks (2020) — 3.0k citations

---
---

### 10. Square Attack: A Query-Efficient Black-Box Adversarial Attack via Random Search — 1.7k citations





## 참고

1. <https://songsite123.tistory.com/78>

2. <https://jonathan-hui.medium.com/adversarial-attacks-b58318bb497b>

3. <https://chatgpt.com/c/6a0b049b-d58c-8322-992e-0f8bcb9b9723>

