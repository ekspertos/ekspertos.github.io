## 확률 및 불규칙 신호론
### Lecture 18
결합 특성 함수  (Joint Characteristic Function)
- 두 개의 랜덤변수 X와 Y의 결합 특성 함수

$$
\Phi _{X,Y} (w_1, w_2) = E[e^{jw_1X + jw_2Y}]
$$



결합 가우시안 랜덤 함수

###
 결합 가우시안 랜덤변수



## Lecture 17 & 18


## Asymptotic equpartition property

n이 무한대로 갈 때 가지는 근사적인 성질들에 대해 살펴본다

channel coding theory에서 achievability를 증명할 때 WLLN을 기반한 Typical Set을 정의한 다음에 이를 이용해 증명했다

Typical Set 의 정의가 continuous random variable 에서도 적용된다.


대부분 discrete random variable과 비슷하다.

$X_1, X_2, ... , X_n$ 이 f(x)의 확률분포를 따르는 i.i.d 한 random variable 이라고 한다.

이때 differential entropy는

$$
\frac{1}{n} log f(X_1,X_2,...,X_n) \rightarrow E[-log f(x)] = h(X)
$$

WLLN을 사용하면 log 함수의 평균임을 알 수 있다.


$$
\frac{1}{n} log f(x_1)f(x_2)...f(x_n) = \frac{1}{n} log f(x_1) + logf(x_2) +...+ logf(x_n) \rightarrow E[-log f(x)] = h(X)
$$

Discrete에서 Typical Size의 정의

$$
|A_{\epsilon}^{(n)}| = 2^{nH(X)}
$$

cardinarity를 통해서 size를 정해주었다

그러나 continuous 한 경우에는 무한하기 때문에 cardinarity를 구하기 불가능 하다


typical set의 크기를 Vol으로 정의한다.

$$
Vol(A) = \int_A dx_1 dx_2 ... dx_n
$$

- $ Pr(A_{\epsilon}^{(n)}) > 1- \epsilon$ for n sufficiently large

- $Vol(|A_{\epsilon}^{(n)}|) \leq 2^{n(h(X) + \epsilon)}$ for all n

- $Vol(|A_{\epsilon}^{(n)}|) \geq 2^{n(h(X) - \epsilon)}$ for all n


## Differential Entropy의 Differential Entropy

다중 가우시안 함수에 대한 확률 밀도 함수
$$
f(x) = \frac{1}{(\sqrt{2\pi)^n}|K|^{\frac{1}{2}}} e^{-\frac{1}{2} (x-\mu)^T K^{-1} (x-\mu)}
$$

$$

$$

## Relative Entropy

확률분포의 차이를 계산하는데 사용하는 함수

$$
D(f||g) = \int f log\frac{f}{g}
$$

f의 support set과 g의 support set

relative entropy는

$$
s_f \subset s_g
$$

인 경우에만 유한한 값을 갖게된다

f(x) = g(x) 일 경우 Relative entropy가 0 값을 갖는다

$$
-D(f||g) = \int_S \frac{g}{f}
$$

$$
\leq log \int_S f\frac{g}{f}
$$

$$
= log \int_S g \leq log 1 = 0
$$

relative entropy는 0보다 크거나 값다.
-> mutual entropy도 0보다 크거나 같음을 알 수 있다.

$$
I(X;Y) = D(f(x,y) || f(x)g(y))
$$

$$
I \geq 0, h(X|Y) \geq h(X)
$$
conditional reduced entropy도 성립한다

## Hadamard's Inequality

$$
h(X_1, ..., X_N) \leq \sum{h(X_i)}
$$

를 통해서

$$
|K| \leq \Pi_{i=1}^n K_{ii}
$$

## Theorem 8.6.3

h(X+c) = h(X)

## Theorem 8.6.4

h(aX) = h(X) + log|a|

h(AX) = h(X) + log |det(A)|

## Theorem 8.6.5

entropy를 최대화하는 분포는 X가 uniform 분포인 상황이다

entorpy를 최대화하는 분포는 ``X ~ N(0,K)`` 인 상황이다

가우시안 분포가 분산이 같은 상황에서 엔트로피를 최대화 해준다.


## Lecture 24

### Gaussian Channel

Gaussian Channel & Capacity of Gaussian channel

$$
Y_i = X_i + Z_i , \ \ Z_i \sim  N(0, N)
$$

i.i.d Gaussian noise

시간에 따라 독립인 random variable

독립인 랜덤 변수들이 더해지면 Central limit 이론에 의해 Gaussian 이 된다

노이즈 variance 가 0 이라면 엔트로피가 무한대로 발산할 수 있다. 즉, 수신단은 송신단에서 보낸 것을 정확히 볼 수 있다(알파벳이 무한대인 경우).


입력 신호 제한이 없다면 각 codeword 들 간의 거리를 늘려주면서 커패시티를 무한대로 만들 수 있다.

그래서 우리는 Average Power Constraint를 두고 이론을 살펴본다

$$
\frac{1}{n} \sum_{i=1}^{n}x^2 \leq P
$$

결론적으로 커패시티는 다음 식으로 표현된다.

$$
C = \frac{1}{2} log_2 (1 + \frac{P}{N})
$$
