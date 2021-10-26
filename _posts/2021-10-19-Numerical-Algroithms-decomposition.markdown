## 서론

> `Ax = b` 식의 `x` 값을 구하는 방법에 대해 알아본다
>
> Gaussian Elimination, LU decomposition,  Cholesky Factorization, QR Factorization, Gram-shmit factorization
>

row substitution, row permutation, row scailing은 결과에 영향을 주지 않는다. b 값 또한 동일하게 substitution되고 scailing되기 때문이다.

그러나 column operation은 x 값을 바꿔주는 것이기 때문에 x의 위치를 어떻게 바꾸었는지 기록해 둬야 한다. (column swap은 book keeping 필요.)

## Gaussian Elimination
  0. pivoting
    - __partial pivoting__
      열에서 가장 큰 값이 pivot으로 사용될 수 있도록 row swap을 한다
    - __full pivoting__
      전체 행렬에서 가장 큰 값이 pivot에 위치하도록 한다
      coloumn swap을 해야 할 경우 `book keeping 필요`하다

  1. __row scailing__
    pivot 값이 1이 되도록 row scailing 진행한다

  2. __forward substitution__
    __scailing__ 과 __elimination__ 을 통해 Upper Matrix를 만들어 준다

  3. __backward substitution__
    __scailing__ 과 __elimination__ 을 통해 Identity Matrix를 만들어 준다

`Ax = b` 값을 구하기 위해 Gaussian Elimination을 따로 해주어야 한다.



## LU decomposition
`Ax = b` 값을 구할 때마다 Gaussian Elimination을 해준다고 하면 $O(n^3)$이 필요하다.

LU로 행렬을 $O(n^3)$을 통해 분해 한 후 $O(n^2)$ 만으로 `Ax = b`를 구할 수 있다.

$$
Ax = b \ , \ LUx = b \\
Ly = b \ , \ Ux = y  \\
O(n^2) + O(n^2) = O(n^2)
$$

$$
M_k M_{k-1} ... M_2 M_1 A \overrightarrow{x} = M_k M_{k-1} ... M_2 M_1 \overrightarrow{b}
$$

$$
U = M_k M_{k-1} ... M_2 M_1 A
$$
$$
L = (M_k M_{k-1} ... M_2 M_1)^{-1} = M_1^{-1} M_{2}^{-1}..M_{k-1}^{-1} M_k^{-1}
$$

```
A의 역함수를 정확히 구할 수 없다
그러나 L 같은 경우 쉬운 방법으로 U의 역함수를 구할 수 있다

즉, scailing 과 elimination 은 쉽게 역함수를 구해줄 수 있다.
```

## Over-Determined Case
> 행렬 A가 non invertable 한 경우 값을 구하는 방법을 알아본다
>

식이 미지수보다 많은 Over-Determined Case에 대해 알아본다

[n x m] [m x 1] = [n x 1]

n 이 식의 개수, m 이 미지수의 개수이다.

- tall system은 식에 모순을 가지고 올 확률이 높다

- exact solution을 허용하지 않을 수 있다

$$
min || Ax-b ||_2 = (Ax-b)^T (Ax-b) \\
= (x^TA^T-b^T)(Ax - b)  = x^T(A^TA)x - x^TA^Tb - b^TAx + b^Tb \\
= x^T(A^TA)x - (b^TAx)^T - b^TAx + b^Tb \\
= x^T(A^TA)x - 2(A^Tb)^Tx + b^Tb \\
$$
2-norm을 해줌으로써 구해지 4개의 값들 모두 상수이여야 한다. 상수는 $a = a^T$ 라는 특징을 갖는다
$$
(a^Tx)' = (a*x)' = a
$$
$$
\triangledown (||Ax-b||) = ((A^TA+(A^TA)^T)x - A^Tbx = 0
$$
$$
A^TAx = A^Tb
$$

위의 식을 normal equation 이라고 하며 구하기 힘들다.

non-square 문제를 square 형태로 해결할 수 있다.

__Invertable matrix ATA를 사용한다.__


## Under-Determined Case
식이 미지수보다 적은 Under-Determined Case에 대해 알아본다

[n x m] [m x 1] = [n x 1]

n 이 식의 개수, m 이 미지수의 개수이다.


- 유일한 solution을 찾을 수 없다
- 해에 대한 추가적인 가정이 필요하다

식에 대한 추가적인 가정을 하는 방법을 `Regularization`이라고 한다.

- __Regularization__
  - __Ridge Regularization__
    $
    min ||A\overrightarrow{x} - \overrightarrow{b}|| + \alpha ||x||_2^2
    $
    L2 Regularization이라도고 하며, 상대적으로 큰 값들에 대한 정규화를 해준다.
  - __Lasso Regularization__
    $
    min ||A\overrightarrow{x} - \overrightarrow{b}|| + \alpha ||x||_1^2
    $
    L2 Regularization이라도고 하며, 모든 값들을 동일하게 줄여주어 여러개의 zero들을 갖는다.


### __Tikhonov Regularization__
Rigde Regularization을 다른 말로 Tikhonov Regularization 이라고 부른다.

$$
(A^TA + \alpha I)x = A^Tb
$$
다음 식을 통해 우리가 원하는 값을 구할 수 있다.

$$
Rank(A) = m \\
Rank(A^TA) = m
$$
nxn 행렬이지만 rank 값이 그보다 작은 m 이기 때문에 `non-inverable`하다. 그렇기 때문에 `alpha 값을 증가 시킬 수록 invertable 특성이 높아져` 유일한 해를 결정 짓기 쉬워진다.


## Choleskey Factorization
`Ax = b`에서 LU factorization 한 것과 같이 ATAx = ATb 에 대해서도 `Choleskey factorization`을 한다.

### ATA 특성
ATA이 `SPD`(Symmetric Positive Definite)의 특성을 갖는다는 것을 알고 넘어가야 한다
우리는 `SPD matrix Q`를 factorize하는 방법을 배운다

- __Symmetric__
  $A^TA$ 행렬은 symmetric 하다
  $\begin{bmatrix}
  q_1q_1 & q_1q_2 & q_1q_3 \\
  q_2q_1 & q_2q_2 & q_2q_3 \\
  q_3q_1 & q_3q_2 & q_3q_3  
  \end{bmatrix} $

- __Positive Definite__
  - __positive semi-definite__ : $x^T A^TA x \geq 0$

  - __positive definite__ : $x^T A^TA x > 0$

  $x^TA^TAx = ||Ax||_2^2 \geq 0 $
  특성 상 positive semi-definite의 조건을 만족하며, 행렬의 모든 column들이 linearly dependent한 경우 `positive definite` 이다.


### factorization
행렬 C가 SPD 특성을 갖는다고 하면 다음과 같이 표현할 수 있다.
$$
E =\begin{bmatrix}
\frac{1}{\sqrt{c_{11}}} & \overrightarrow{0} \\
\overrightarrow{r} & I_{n-1\times n-1} \\
\end{bmatrix}
\\
C =\begin{bmatrix}
c_{11} & \overrightarrow{v}^T \\
\overrightarrow{v} & \tilde{C} \\
\end{bmatrix}
$$


$$
EC = \begin{bmatrix}
\sqrt{c_{11}} & \frac{\overrightarrow{v}^T}{\sqrt{c_{11}}} \\
c_{11}\overrightarrow{r} + \overrightarrow{v} & \overrightarrow{r}\overrightarrow{v}^T + \tilde{C} \\
\end{bmatrix}
$$

$$
c_{11}\overrightarrow{r} + \overrightarrow{v} = 0 \\
r_i = -\frac{c_{i-1}}{c_{11}} \\
$$

$$
ECE^T = \begin{bmatrix}
1 & 0 \\
0 & D \\
\end{bmatrix}
$$
결과로 나온 D 또한 SPD 행렬이다.
$$
...E_2E_1CE_1^TE_2^T... = I
$$

$$
L = E_1^{-1} E_{2}^{-1} ... E_{k-1}^{-1} E_k^{1}
C = LL^T
$$
이 과정들에서 구한 E 값을 이용해 L을 구할 수 있다

이 과정을 `Choleskey factorization`이라고 하며 다음과 같은 성질을 갖는다

- __성질__
  - $\frac{n(n-1)}{2}$ 의 메모리 만을 사용하여 행렬을 저장할 수 있다
  - $O(n^2)$의 forward & backward substitution 을 갖는다
  - LU 사용 시 rounding error에 의해 `SPD 특성을
잃을 수 있다`

- __가능한 이유__
  - `positive definite` : $\sqrt{c_{11}}$ 을 구할 수 있다
  - `Symmetry` E와 ET를 곱해 upper과 lower matrix 을 0 으로 만들 수 있다


### Choleskey Implementation
`C = LLT`에서 L 값을 쉽게 구하는 방법에 대해 설명한다.

$$
L = \begin{bmatrix}
L_{11} & 0 & 0\\
l_k^T & l_{kk} & 0\\
\times & \times & \times \\
\end{bmatrix}
$$
$$
LL^T = \begin{bmatrix}
\times & \times & \times \\
l_k^T L_{11}^T & ||l_{kk}||_2^2 + l_{kk}^2 & \times\\
\times & \times & \times \\
\end{bmatrix}
$$

다음 과 같이 표현 할 수 있으며 이를 행렬 C 와 비교하면 다음과 같은 조건을 가질 수 있다.

$$
l_{k}^TL_{11} = c_{k}^T \\
l_{kk} = \sqrt{c_{kk} - ||l_k||_2^2}
$$

Gaussian elimination을 통해 $l_k$ 를 구해준 후 위의 식을 이용해 $l_{kk}$ 를 구한다

첫 행부터 차례대로 이를 진행해 L 행렬을 구한다

### 시간복잡도
$$
l_{k}^TL_{11} = c_{k}^T
$$
Lower matrix gaussian elimination은 O(n^2)의 시간 복잡도를 갖는다
각 행에 대해 elimination을 진행하므로 factorize 하기 위해 결과적으로 $O(n^3)$의 복잡도를 갖는다
