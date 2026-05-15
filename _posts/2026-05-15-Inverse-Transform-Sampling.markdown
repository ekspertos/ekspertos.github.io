---
layout: post
title:  "Inverse Transform Sampling"
subtitle: "Inverse Transform Sampling"
categories: projects
tags: Sampling probability distribution generation 
comments: true
---

> 
> 컴퓨터는 어떻게 특정 확률분포에서 샘플을 간단한 함수 호출만으로 생성할 수 있을까?
> 확률분포란 특수한 환경에서 관측된 데이터들이 어떤 통계적 패턴을 가지는지 수학적으로 기술한 모델이다.
> 컴퓨터에서 이런 물리적 상황을 그대로 시뮬레이션하는 방식으로 확률변수 샘플을 얻을 수도 있지만, 이러한 접근은 일반적으로 비효율적이거나 경우에 따라서는 불가능한 경우도 많다.
> 그렇다면 컴퓨터에서는 어떻게 Gaussian이나 chi-square와 같은 다양한 확률분포의 샘플을 효율적으로 생성할 수 있을까? 
> 그 해답이 바로 `Inverse Transform Sampling` 기법이다.


신호처리를 공부하다 보면, 확률 분포에서 랜덤 변수를 샘플링할 때가 많다.
예를들어 VAE (variational autoencoder) 모델은 정규분포에서 샘플한 정보를 토대로 latent variable을 생성하고 이를 학습하여 이미지를 생성한다. 
정규분포는 아래와 같다.

$$
p_X(x) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{1}{2}(\frac{x-\mu}{\sigma})^2)}
$$

간단하게 생각해보면 정규분포에서 샘플을 만들기 위해서는 평균 값에 가까운 수들을 많이 생성하고 지수함수적으로 생성 빈도를 줄이면 된다.
이렇게 인위적으로 샘플을 만들어줄 수는 있겠지만 이는 랜덤성이 없기 때문에 실제 샘플로는 적합하지 않다

그렇다면 컴퓨터는 어떻게 정규분포에서 이러한 샘플을 생성할 수 있을까? 그 핵심은 상대적으로 쉽게 생성할 수 있는 균등분포(uniform distribution) 난수를 이용하는 것이다.

컴퓨터는 의사난수 발생기(pseudo random number generator, PRNG)를 통해 균등분포에 가까운 난수열을 생성한다. 이러한 PRNG는 내부에 일정 길이의 비트로 이루어진 상태(state)를 가지고 있으며, 매 단계마다 이 상태를 일정한 규칙에 따라 갱신하면서 새로운 값을 생성한다.

대표적인 예로, 시프트 레지스터 구조와 XOR 기반의 피드백을 사용하는 Linear Feedback Shift Register(LFSR)가 있다.

![이미지](https://ekspertos.github.io/assets/img/RandomProcess/2026-05-15-LFSR.png)

그렇다면 이 균등 분포 샘플들로 어떻게 정규분포와 같은 복잡한 분포의 샘플들을 만들지 알아보자.


## 


