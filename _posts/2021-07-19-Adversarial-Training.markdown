---
layout: post
title:  "Adversarial Training"
subtitle: "Adversarial Training"
categories: projects
tags: blog github pages jekyll spacy
comments: true
hidden: true
---

https://chatgpt.com/c/6a0b049b-d58c-8322-992e-0f8bcb9b9723

그니까 boundary 근처에서 취약하다 －＞ 

$$
\Sigma_{i=0}^M x_i
$$


Q: 근데 loss를 가장 빠르게 증가시키는게, decision boundary를 넘어서려는거 아니야?

A: “loss를 최대화하는 방향”이 항상 “decision boundary를 넘는 방향”은 아니다.

gradient 방향은 “정답 클래스 확률을 가장 빨리 떨어뜨리는 방향”이지 “decision boundary로 가는 방향”이 아니다

그런데 boundary 근처에서는 loss landscape가 매우 가파름


Q: 왜 “근처”에서만 취약하냐?
high-dimensional space에서는 boundary가 매우 복잡하고 촘촘함
데이터 manifold 주변에 decision boundary가 얇게 감싸고 있음

neural network는 locally linear한 성질이 있음
그래서 작은 이동에도 쉽게 crossing 가능


Q: 왜 high-dimensional에서 decision boundary가 “촘촘하게” 존재하나?
고차원에서는 “공간 대비 데이터가 너무 희소해서”, boundary가 데이터 주변에 얇게 
깔릴 수밖에 없다

실제 데이터는 그 공간 전체에 퍼져있지 않음
전체 공간이 아니라 “아주 얇은 manifold 위”에 있음


Q: 고차원에서는 “한 방향으로만 밀어도” 다른 class가 너무 쉽게 존재 
그런데 고차원에서는 “빈 공간이 너무 많아서” 둘은 모순 아니야?



Q: adversarial example의 본질?

A: “decision boundary 근처에 있는 점이 gradient 방향으로 살짝 밀리면서 넘어가는 것”




gradient 방향 = 실제 변화 방향
curvature (비선형 효과)가 약함 의미이다.

작은 perturbation에서도 output 변화가 “예측 가능하게 직선적으로 커짐”

그래서:

조금만 밀어도 logit이 크게 변함
decision boundary 쉽게 crossing


Q: locally linear 하기 때문에 adversarial가 어떤 데이터일지 근사할 수 있는거지 adversarial이 존재하기 위한 필수 조건이 아니네

1) 왜 local linearity는 “필수 조건이 아닌가?”

이걸 이해하려면 반례 생각하면 됨:

만약 모델이 완전히 비선형이어도
decision boundary가 가까우면
작은 perturbation으로도 flip 가능

👉 즉 adversarial은 “geometry 문제”라서
선형성 여부와 독립적으로 발생함

5) 그럼 local linearity의 진짜 의미

local linearity는 이거야:

“이 근처에서는 gradient 하나로 충분히 방향을 예측할 수 있다”

즉:

attack 설계 가능
optimization 가능
worst-case direction 계산 가능


Q: 그러면 논문에 적힌 이 말은 틀린거야? "The linear view of adversarial examples suggests a fast way of generating them. We hypothesize that neural networks are too linear to resist linear adversarial perturbation. "

3) 그런데 네가 짚은 게 정확히 뭐냐면

너는 지금:

“local linearity는 attack을 쉽게 찾게 하는 거지, adversarial 존재 자체의 필요조건은 아니지 않나?”

를 본 거야.

👉 맞아. 이후 연구들은 실제로 그렇게 봄.

4) 현대적 관점에서는 이렇게 정리함
Goodfellow 관점
local linearity가 핵심 원인
이후 연구들
더 근본 원인은:
decision boundary geometry
high-dimensional concentration
small margin
data manifold structure

그리고:

local linearity는
공격 가능성을 강화하고
gradient-based attack을 쉽게 만든다




## Explaining and Harnessing Adversarial Examples [ICLR 2015]


3가지 메인 컨트리뷰션
- 모델이 상당히 Linear한 특징들을 가지고 있다
- Linear 특징을 활용해서 FGSM이라는 강력한 공격 기법을 설계
- 그 데이터를 학습에 사용하는 Adversarial Training을 조명한다 

상당수의 최신 논문들에서 인정하고 따르고 있다.

Adversarial Training이 아직도가장 강력한 Adverarial 공격에 대한 방어기법이다

아직도 FGSM이 Baseline 공격기법이다.

우리모델이 Linear한거도 현재 정설로 받아들여지고 있다.

### Introduction
adversarial example은 Transferable 하다는 특징이 있다.
하나의 네트워크 (ResNet)에서 학습된 모델에서의 adversarial 이 다른 모델 (VGG) 에서도 똑같이 adversarial로서 동작한다. -> Transferability 

L-BFGS :
 - 뉴턴 메서드 기반
 - time-comsuming

식 자체가 잘못됐다
-sign(w) 가 아니라 -y*sign(w) 이어야한다
-> Adversarial Training Versus Weight Decay 를 읽어보도록


패널티가 빼진다


아니... 왜 Logistic Sigmoid Function 으로 classify 하는데, softplus function으로 로스를 계산하는거지??


Binary classification에서 label을

$$
y \in \{-1,+1\}
$$

라고 하자.

모델의 output score를

$$
z = wx+b
$$

라고 하면, logistic sigmoid를 이용한 class probability는 다음과 같다.

$$
P(y=1|x)=\sigma(z)
$$

$$
\sigma(z)=\frac{1}{1+e^{-z}}
$$

이를 label $y\in\{-1,+1\}$ 형태로 하나로 합치면:

$$
P(y|x)=\sigma(yz)
$$

왜냐하면:

- \(y=1\)

$$
P(y=1|x)=\sigma(z)
$$

- \(y=-1\)

$$
P(y=-1|x)=\sigma(-z)
$$

이기 때문이다.

---

학습에서는 negative log likelihood를 최소화하므로 loss는:

$$
L(x,y)
=
-\log P(y|x)
$$

$$
=
-\log \sigma(yz)
$$

이다.

이제 sigmoid 정의를 대입하면:

$$
=
-\log\left(
\frac{1}{1+e^{-yz}}
\right)
$$

로그를 정리하면:

$$
=
\log(1+e^{-yz})
$$

여기서 \(z=wx+b\) 이므로:

$$
L(x,y)
=
\log(1+e^{-y(wx+b)})
$$

이다.

softplus function을

$$
\zeta(t)=\log(1+e^t)
$$

라고 정의하면:

$$
L(x,y)
=
\zeta(-y(wx+b))
$$

가 된다.

더 많은 캐패시티가 필요하다? -> 그런가? 

Defensive Distillation -> T를 test time에 그러면 숨긴다는거랑 같은 의미 아닌가?



## 논문 추천

1) 시작점 — FGSM / linearity hypothesis
Explaining and Harnessing Adversarial Examples

가장 먼저 읽어야 하는 논문.

핵심:

FGSM 제안
“too linear” hypothesis
high-dimensional linear accumulation

특히 중요한 부분:

w
T
δ

설명.

2) “gradient masking” 붕괴시킨 논문
Obfuscated Gradients Give a False Sense of Security

엄청 중요함.

이 논문이 보여준 것:

단순히 gradient를 이상하게 만들면 robust해 보일 뿐
실제 robustness는 아님

즉:

adversarial 문제는 gradient artifact가 아니라 geometry 문제

라는 걸 강하게 보여줌.

3) robust optimization 관점 정립
Towards Deep Learning Models Resistant to Adversarial Attacks

현대 adversarial training의 기반.

핵심:

θ
min
	

∥δ∥≤ϵ
max
	

L(x+δ,y)

여기서 adversarial robustness를:

optimization problem

으로 정식화함.

PGD attack도 여기.

4) “decision boundary geometry” 이해에 중요한 논문
Adversarial Examples Are Not Bugs, They Are Features

이건 진짜 추천.

엄청 중요한 관점 변화:

adversarial perturbation은 “noise”가 아니라
모델이 실제로 사용하는 predictive feature

라는 주장.

이 논문의 핵심 통찰
인간이 못 보는 feature도
모델 입장에서는 predictive
adversarial perturbation은 그 feature를 강화

즉:

adversarial vulnerability는 버그가 아니라 statistical structure의 결과

5) geometry/manifold 관점
The Boundary Tilting Perspective on the Phenomenon of Adversarial Examples

네 질문과 굉장히 잘 맞음.

핵심:

boundary가 manifold 근처에서 기울어져 있음
그래서 작은 perturbation으로 crossing 가능

이 논문은:

“boundary geometry” 관점을 명확히 제시

6) robustness vs accuracy tradeoff
Adversarial Examples Improve Image Recognition

이 논문도 매우 중요.

핵심 주장:

robust feature와 standard feature가 다를 수 있음

즉:

robustness를 높이면
일반 accuracy와 tradeoff 생길 수 있음
7) representation/manifold 관점
A Geometric Perspective on the Transferability of Adversarial Directions

핵심:

왜 adversarial perturbation이 모델 간 transfer되는가
shared geometry 설명
추천 읽는 순서

내 추천은:

Goodfellow 2014
Madry 2017
Athalye 2018
Ilyas 2019
Tsipras 2018
Tanay & Griffin 2016

이 순서면:

linearity
optimization
geometry
feature learning
robustness tradeoff

까지 다 이어짐.


[중요 참고]


https://songsite123.tistory.com/78


Towards the Science of Security and Privacy in Machine Learning
https://jonathan-hui.medium.com/adversarial-attacks-b58318bb497b