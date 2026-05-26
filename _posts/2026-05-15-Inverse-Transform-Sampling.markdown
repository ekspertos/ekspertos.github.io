---
layout: post
title:  "Inverse Transform Sampling"
subtitle: "Inverse Transform Sampling"
categories: projects
tags: Sampling probability distribution generation 
comments: true
hidden: false
---

> 
> 컴퓨터는 어떻게 특정 확률분포에서 샘플을 간단한 함수 호출만으로 생성할 수 있을까?
> 확률분포란 특수한 환경에서 관측된 데이터들이 어떤 통계적 패턴을 가지는지 수학적으로 기술한 모델이다.
> 컴퓨터에서 이런 물리적 상황을 그대로 시뮬레이션하는 방식으로 확률변수 샘플을 얻을 수도 있지만, 이러한 접근은 일반적으로 비효율적이거나 경우에 따라서는 불가능한 경우도 많다.
> 그렇다면 컴퓨터에서는 어떻게 Gaussian이나 chi-square와 같은 다양한 확률분포의 샘플을 효율적으로 생성할 수 있을까? 
> 그 해답이 바로 `Inverse Transform Sampling` 기법이다.



- [How Computers Generate Distribution Samples](#how-computers-generate-distribution-samples)
- [Uniform Distribution Sampling](#uniform-distribution-sampling)
- [Inverse Transform Sampling](#inverse-transform-sampling)
- [참고](#참고)

---
## How Computers Generate Distribution Samples

신호처리를 공부하다 보면 확률 분포에서 랜덤 변수를 샘플링하는 경우가 많다.
예를 들어 VAE(variational autoencoder) 모델은 입력 데이터에 대해 encoder가 추정한 확률분포(보통 Gaussian)로부터 latent variable을 샘플링하고, 이를 decoder에 입력하여 데이터를 생성한다.
정규분포는 다음과 같이 정의된다.

$$
p_X(x) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{1}{2}(\frac{x-\mu}{\sigma})^2}
$$

흔히 확률분포를 알고 있으면 샘플링이 가능할 것이라고 생각하지만, 확률분포 만으로는 샘플링이 가능하지 않다. 
따라서 샘플링이 가능하도록 하기 위해서는, 출력이 샘플이 되는 함수를 정의해주거나 별도의 방법론이 필요하다.

대표적으로 출력이 샘플이 되는 함수를 정의해주는 방법은 `Inverse Transform Sampling`를 활용하는 방법이다. 
Inverse Transform Sampling은 컴퓨터가 쉽게 생성할 수 있는 균등분포 난수와 확률분포의 누적분포함수를 이용하여, 원하는 확률분포의 샘플을 생성하는 방법이다.

## Uniform Distribution Sampling

컴퓨터는 다른 분포들과 달리 균등분포(uniform distribution)에 해당하는 난수를 비교적 쉽게 생성할 수 있다. 이는 균등분포가 특정한 복잡한 변환 없이도, 단순한 이산 상태 업데이트 과정과 선형적인 규칙만으로 근사적으로 구현될 수 있기 때문이다.

구체적으로 컴퓨터는 의사난수 발생기(pseudo random number generator, PRNG)를 사용하여 균등분포에 가까운 난수열을 생성한다. 이러한 PRNG는 내부에 일정 길이의 비트로 이루어진 상태(state)를 가지고 있으며, 매 단계마다 이 상태를 일정한 규칙에 따라 갱신하면서 새로운 값을 출력한다.

![이미지](https://ekspertos.github.io/assets/img/RandomProcess/2026-05-15-LFSR.png)

대표적인 예로, 위에 그림처럼 시프트 레지스터 구조와 XOR 기반의 피드백을 사용하는 Linear Feedback Shift Register(LFSR)가 있다.
그렇다면 이 균등 분포 샘플들로 어떻게 정규분포와 같은 복잡한 분포의 샘플들을 만들지 알아보자.


## Inverse Transform Sampling

Inverse Transform Sampling은 확률분포의 누적분포함수(CDF)를 통해 생성된 값이 $[0,1]$ 구간의 균등분포 변수로 대응된다는 성질을 이용한 기법이다.

$$
F_X(x) = \int_{-\infty}^{\tau} f_X(\tau) \ d\tau, \ \ F_X(x) \equiv u \sim U(0,1)
$$

확률변수 $x$의 CDF 값을 $u$라고 하면, $u$는 $[0,1]$ 구간의 균등분포 안에 위치한다. 또한 CDF가 단조 증가 함수이므로, $x$와 $u$ 사이에는 일대일 대응이 성립한다.
따라서 우리는 inverse CDF $F_X^{-1}$를 구할 수 있다면, $[0,1]$ 구간에서 샘플링한 균등분포 변수 $u \sim U(0,1)$를 이용해서
$x = F_X^{-1}(u)$
로 목표 확률분포의 샘플을 생성할 수 있다.

$$
F_X^{-1}(u) = x, \ \ u \sim U(0,1) \text{ and } x \sim f_X(x) 
$$

아래 예시를 보면 이해가 더 쉬울 것이다. 원래는 균등분포에서 랜덤하게 값을 샘플링하지만, 직관적으로 보기 위해 동일한 간격으로 샘플링을 사용한다.
먼저, inverse transform sampling으로 균등분포의 샘플을 구하는 예시를 본다.

<p align="center">
  <img src="https://ekspertos.github.io/assets/img/RandomProcess/2026-05-15-ICDF.png" width="500">
</p>

이 예시에서 볼 수 있듯이, 균등분포에서 샘플된 값으로 균등분포의 샘플을 만들기 때문에 입력의 균일한 간격이 출력에서도 그대로 보존되는 것을 볼 있다.
이제 이것을 라플라스 분포에 적용하면 아래와 같다.

<p align="center">
  <img src="https://ekspertos.github.io/assets/img/RandomProcess/2026-05-15-ICDF-2.png" width="500">
</p>


이 예시에서는 균등분포에서 샘플된 값으로 라플라스 분포를 만들기 때문에 입력의 균일한 간격이 출력에서 보존되지 않고 확률 밀도가 높은 중앙 영역에 출력이 집중되는 것을 볼 수 있다.

아래에 라플라스 분포의 예시를 재현할 수 있는 파이썬 코드를 제공한다.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import uniforｍ, laplace

# parameters
mu = 0
b = 1

# x for Laplace pdf/cdf
x = np.linspace(-6, 6, 1000)
# x for uniform pdf
uniform_x = np.linspace(-0.01, 1.01, 1000)

# uniform distribution U(0,1)
uniform_pdf = uniform.pdf(uniform_x, loc=0, scale=1)

# Laplace distribution
pdf = laplace.pdf(x, loc=mu, scale=b)
cdf = laplace.cdf(x, loc=mu, scale=b)

# u for inverse CDF
u = np.linspace(0.001, 0.999, 1000)
inv_cdf = laplace.ppf(u, loc=mu, scale=b)

plt.figure(figsize=(10, 8))

# (1,1) PDF
plt.subplot(2, 2, 1)
plt.plot(pdf, x, color='blue')
plt.title("Laplace PDF")
plt.xlabel("f(x)")
plt.ylabel("x")
plt.grid(True)

# (2,2) inverse CDF
plt.subplot(2, 2, 2)
plt.plot(u, inv_cdf, color='green')
plt.title("Inverse CDF")
plt.xlabel("u")
plt.ylabel(r"$F^{-1}(u)$")
plt.grid(True)

# (1,2) CDF
plt.subplot(2, 2, 4)
plt.plot(uniform_x, uniform_pdf, color='black')
plt.title("Uniform PDF")
plt.xlabel("u")
plt.ylabel("f(u)")
plt.grid(True)


plt.tight_layout()
plt.show()

```

---

다음 포스트에서는 다양한 분포에 대해 inverse transform sampling을 적용하는 예시를 알아볼 것이다.


## 참고
1. 위키피디아, <https://en.wikipedia.org/wiki/Inverse_transform_sampling>
2. Sampling 방법 / Inverse CDF Method, <https://m.blog.naver.com/jinis_stat/221648391742>
3. 지수분포의 난수 생성기 (Inverse CDF method) 예제, <https://blog.naver.com/jinis_stat/221641097139>