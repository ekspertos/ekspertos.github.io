

## 서론

>
> Rethinking the Inception Architecture for Computer Vision에서 처음 제시되고
> 현재 여러 SOTA 모델들의 훈련에 활용된 `Label Smoothing` 기법에 대해 정리한다.
> Label Smoothing 기법이 모델 학습에 미치는 영향들이 제시된
> `When Does Label Smoothing Help?`를 바탕으로 포스트가 작성되었다.
> https://arxiv.org/pdf/1906.02629.pdf

과적합되는 현상은 모델 성능을 떨어뜨리는 주요 원인으로 과적합된 모델은 __학습용 데이터__ 에 대해
높은 성능을 보이는 반면, 학습에 사용되지 않은 __검증용 데이터__ 에 대해서는 제대로된 추론을 하지 못한다.
대표적으로 Dropout과 Label Smoothing이 과적합을 방지하기 위해 모델에 사용되며, 높은 성능 향상 결과를
보인다. 본문에서는 일반화 기법들 중 여러 SOTA 모델에 적용된 기법인 `Label Smoothing`에 대해 다룬다.

Label Smoothing은 [...0 1 0...]의 hard target 레이블 대신 [...0.01 0.92 0.01...]의 평활화된 soft target
레이블을 모델 학습에 사용해 성능을 개선한다.
![이미지](https://miro.medium.com/max/1400/1*BDXCF-C9azGzr-OFzBFlEw.png)

Label Smoothing은 모델 일반화를 포함해 다음과 같은 특징들을 갖는다.
  - 모델 일반화 및 학습속도 개선 ( improve generalization & learning speed )
  - 모델 보정 효과 개선 ( improve model calibration )
  - 지식 증유에 보이는 역효과 ( less effective to knowledge distillation )

위 특징들은 아래에서 자세히 설명할 예정이다.

---

* 목차
  - [Label Smoothing 기법 소개](#label-smoothing-기법-소개)
  - [Penultimate Layer Representations 시각화](#penultimate-layer-representations-시각화)


## Label Smoothing 기법 소개
기법을 이해하기 위해 그 핵심이 되는 Cross-Entropy Loss에 대한 사전지식이 필요하다.
<details>
<summary> <b>Cross Entorpy Details </b> </summary>

---
* `Cross Entropy`
  Entropy는 정보이론에서 정보를 나타내는 단위로 정보의 불확실성이라고도 한다.  정보에 대한
  확률분포를 기반으로 계산되며 정보량에 대한 식은 다음 같다.
  $$
  H(p) = -\sum_i p_i log p_i \\
  $$

  정보이론에서 Relative Entropy 혹은 Kullback-Leibler distance를 사용해 두 가지 정보(Entropy)를
  비교한다. 두 정보가 비슷하면 비슷할수록 0에 가까운 값을 갖고, 다르면 다를 수록 큰 값을 갖는다.
  `Kullback-Leibler distance로 두 확률분포 사이의 유사도에 대한 측정이 가능하다.`
  $$
  D_{KL}(p||q) = - p_i \cdot log \frac{p_i}{q_i}
  $$

  `Cross Entorpy`는 Kullback-Lebler distance와 동일한 식으로 모델 출력과 hard 레이블의 유사도를
  계산하는 식이다. 다음은 categorical cross entropy의 식이다.
  $$
  CE = -\sum p_i log q_i
  $$
  모델의 출력 $q_i$와 hard label $p_i$ 사이의 relative entropy를 계산한 식으로 하드 레이블은 $p_i$가 0 또는 1의
  값만 갖기 때문은 Kullback-Leibler distance는 위와 같이 Cross Entorpy 식으로 간략히 표현된다.

  즉, Cross-Entropy loss를 사용한 모델은 모델 출력과 하드 라벨의 분포가 같아지도록 학습된다.

  Kullback-Leibler distance는 줄여서 `KL Divergence`라고도 한다.

  ---

</div>
</details>

다중 클래스 분류(multi-class classification) 모델은 신경망 마지막에 위치하는 __소프트맥스 함수의 출력__ 과
__하드 라벨__ 사이의 유사도를 높이는 방식으로 학습되며, 크로스 엔트로피 손실을 최소화하며 목적을 달성한다.
```python
criterion = nn.KLDivLoss(reduction="none")
# Cross-Entropy Loss
ce_loss = criterion(torch.log_softmax(x, dim=1), hard_dist)
```
라벨 스무딩은 모델의 출력을 하드 라벨과 비교하는 대신 소프트 라벨과 비교하며 소프트 라벨은
균등 분포 함수(uniform distribution)와 하드 라벨의 weighted mixture를 사용한다.
반면, 라벨 스무딩이 적용된 모델은 __소프트맥스 함수의 출력__ 과 __소프트 라벨__ 사이의 유사도를 높이는 방식으로
학습된다. 소프트 라벨은 균등 분포 함수(uniform distribution)와 하드 라벨의 weighted mixture로 구해진다.

$$
y_k^{LS} = y_k (1-\alpha) + \alpha/K
$$

```python
criterion = nn.KLDivLoss(reduction="none")
soft_dist = hard_dist * (1-alpha) + norm_dist / K
# Label Smoothing Loss
lsm_loss = criterion(torch.log_softmax(x, dim=1), soft_dist)
```

라벨 스무딩은 아래에 코드에서 구체적으로 구현되어있다.


## Penultimate Layer Representations 시각화

__라벨 스무딩이 적용된 모델__ 은 올바른 클래스와 잘못된 클래스들 사이의 값들이 균등한 거리로 분포되도록
학습된다. 예로 1번이 올바른 클래스이라면, (1,2) (1,3), (1,4) ... 의 클래스들이 비슷한 거리를 두고 분포한다.
거리 정보는 모두 $ \alpha $와 관련된 값을 갖는다.

반면, __하드 라벨로 학습된 모델__ 은 올바른 클래스가 다른 클래스들에 비해 굉장히 큰 예측 값으로 예측되고,
잘못된 클래스들 사이의 예측 값 또한 거리가 불균등하도록 학습된다.

라벨 스무딩을 시각화하면, 정답 클래스를 중심으로 다른 클래스들이 ``아치 모양``으로 분포된 형태를 보인다.


Penultimate Layer의 출력 값을 사용해 직관적으로 설명할 수 있다.
Fully-connected, 소프트맥스 계층 바로 직전 계층을 Penultimate 계층이라고 하며, 아래 그림에서
두번 째 은닉층을 Penultimate 계층이라고 한다.

![이미지](https://developers.google.com/machine-learning/crash-course/images/SoftmaxLayer.svg)

penultimate 계층의 출력을 x,

$$
||x-w_k||^2 = x^T x - 2x^T w_k + w_k^T w_k
$$

![이미지](https://ekspertos.github.io/assets/img/review/2022-05-22-penultimate_layer.PNG)

w1과는 가깝지만 w2, w3, ... 와 같은 거리를 갖게 만든


```

















먼저, 라벨 스무딩이 이미지 분류에서 어떤 효과를 낳는지 확인하기 위해 최종 은닉층의 활성화 값,
penultimate layer activation을 시각화하는 실험을 진행한다.

직관적으로 k 번째 클래스에 대한 맞는 클래스

Label Smoothing은 모델 학습 과정에 활용되는 기법으로  과 비교하지 않고 soft target 과의 비교를 통해 이루어진

target에 대한 기여도만 1로 두지 않고 다른 target 들도 normal distribution으로 설정하여
학습을 진행하는 것이 모델 학습 일반화와 학습 속도 개선에 도움이 된다고 알려져 왔다.


Label Smoothing은 Model Calibration을 개선해 Beam-Search를 사용하는 모델에 특히 효과적이다.
> Beam-Search로 Sequence를 예측하는 음성과 자연어 처리에서는 Label Smoothing을 적용한 모델이 대부분이다

음성인식 모델에서 model calibration을 증진시켜 Beam-Search의 정확도를 높이기 위해 Label Smoothing을 사용한다.
Label Smoothing 기법들이 적용된 모델들이 여러 분야에서 SOTA를 달성했다.

균일하게 차이나도록 장려한다

## Label Smoothing 구현

```python
import torch
from torch import nn
torch.random.manual_seed(0)

smoothing = 0.1
confidence = 1.0 - smoothing

padding_idx = 0
batch_size = 2
seq_len = 4
size = dim = 10

# model output
x = torch.rand((batch_size, seq_len, dim))
x = torch.log_softmax(x, dim=1)
'''
>>> x.shape
torch.Size([2, 4, 10])
'''
x = x.view(-1, size)    # (8,)
# hard target
hard_target = torch.randint(1, size-1, (batch_size, seq_len))        # 0~9
hard_target[0][seq_len - 1:] = padding_idx
'''
>>> hard_target
tensor([[3, 8, 1, 0],
        [2, 8, 2, 2]])
'''
hard_target = hard_target.view(-1)  # shape: (8)
# normal distrubytion
norm_dist = x.clone()
norm_dist.fill_(smoothing / size)
'''
>>> norm_dist
tensor([[0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100],
        [0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100],
        [0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100],
        ...
'''
# soft distribution
soft_dist = norm_dist.scatter_(1, hard_target.unsqueeze(1), confidence)
'''
>>> soft_dist
tensor([[0.0100, 0.0100, 0.0100, 0.9000, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100],
        [0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.9000, 0.0100],
        [0.0100, 0.9000, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100, 0.0100],
        ...
'''
# KL Divergence Between Distributions
criterion = nn.KLDivLoss(reduction="none")
kl = criterion(x, soft_dist)
# padding operation
ignore = hard_target == padding_idx
lsm_loss = kl.masked_fill(ignore.unsqueeze(1), 0).sum() / batch_size
"""
>>> lsm_loss
tensor(6.6152)
"""
```


리뷰 포스트
https://blog.si-analytics.ai/21
https://hongl.tistory.com/230

Calibration
https://github.com/hollance/reliability-diagrams
