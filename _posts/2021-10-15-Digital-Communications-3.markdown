---
layout: post
title:  "채널 코딩, Trellis Coding"
subtitle: "Trellis Coding"
categories: university Jekyll
tags: communications blog github pages jekyll spacy
use_math: true
comments: true
---

## Trellis Code
메모리를 사용한 채널코딩 방식이다
Covolutional Code와 Turbo Code로 나뉜다.

`K` : Constraint Length
고려해야하는 값의 개수로 사용하는 메모리의 개수는 `k-1`개 이다.

![Trellis](https://ekspertos.github.io/assets/img/university/2021-10-15-Trellis-Code.PNG)

1 bit 입력으로 2bit 출력을 하므로 `C = 1/2` 이다.

Trellis Code는 메모리를 사용하기 때문에 근접한 `Code bit들 간에 correlation` 되어 있다. 이러한 연관성을 이용해서 decoding을 해주는 방식이다.

다음 방식들을 통해 correlation 을 더 증가시킬 수 있다
  1. Memory 증가
  2. Feedback 추가
    Turbo Code는 Feedback을 사용한다



## State Diagram
![Trellis](https://ekspertos.github.io/assets/img/university/2021-10-15-Trellis-State-Diagram.PNG)

K-1 메모리의 상태를 이용해서 만든 diagram이다.
입력이 0인 경우를 실선으로, 입력이 1인 경우를 점선으로 표현한다.
$$
state = 2^{k-1}
$$
그림은 k=3으로 state 3개를 사용하며, 흔히 알고있는 `LTE` 같은 경우에 k=7 을 사용하며 `state 64개`를 이용한다


## Trellis Diagram
![Trellis](https://ekspertos.github.io/assets/img/university/2021-10-15-Trellis-Diagram.PNG)

decoding을 위해서 encoding의 ``시작 state를 알고 있어야`` 한다

![Trellis](https://ekspertos.github.io/assets/img/university/2021-10-15-Trellis-State-Diagram-Decode.PNG)

00 state 으로 시작한 Decoder 측 trellis diagram이다.

__00 state :__
 - 다음이 0일 경우 : `00 수신` , `distance 0`
 - 다음이 1일 경우 : `11 수신` , `distance 2`
실제로 수신된 값이 11 이므로 1 을 송 됬을 확률이 높다고 생각할 수 있다.

수신된 모든 값들을 이용해 banch를 구성한 후 모든 state까지의 거리 중 ``최소인 경로를 수신값 사용``한다

이러한 디코딩 방법을 제안한 사람이 Viterbi 이다

Trellis Code decoding 할 때 state 당 하나 씩의 경로들을 기억하고 있어야 한다.

그러나 모든 survivor path 를 메모리에 저장하기는 현실적으로 힘들다. 그러므로 현실에서는 예로 100-bit 마다 Trellis decoding을 한다. (constraint length에 따라 사용할 값이 정해져 있다)


### Soft Decision
수신받은 값이 0에 가까운 정도와 1에 가까운 정도를 판단한다.

예로 0 과 1 을 8개의 기준으로 나누어 판단한다.

수신 받은 값의 정도에 따라 `00` `01` `21` ... `56` ... `77` 중 64개의 값 중 하나로 판단한다.

Soft Decision은 Hard Decision 보다 압도적으로 좋은 성능을 갖는다.

- Linear Block 코드에도 Soft Decision 을 사용할 수는 없을까?
   8개의 기준을 따라 나눈다는 의미는 3bit를 이용한고 볼 수 있다. 그러나 Linear block code는 binary로 처리하지 않을 경우에 연산이 매우 복잡해진다. 아직은 Soft Decision을 사용하지 않는 것이 더 낫다.



## Binary Symmetric Code

BSC (Binary Symmetric Code) 는 BER를 확률을 사용해 구해줄 수 있다.

$$
Pr(1 | 0) Pr(0) + P(0 | 1) Pr(1)
$$

(0을 송신 했을 때 1을 수신할 확률) x (0을 송신할 확률) + (1을 송신 했을 때 0을 수신할 확률) x (1을 송신할 확률)




## Maximum Likelyhood
$$
ML = max(Pr(\overrightarrow{r}|0) , Pr(\overrightarrow{r}|1))
$$
0을 전송했을 때 r이 나타날 확률이 높은지 1을 전송했을 때 r이 나타날 확률이 높은지 확인한다


## MAP (Maximum A Posteriori)
$$
ML = max(Pr(0|\overrightarrow{r}) , Pr(1|\overrightarrow{r}))
$$
r을 수신했을 때 그 값이 1일 확률과 0일 확률 중 최대값을 확인한다. ML 보다 정확한 값을 얻을 수 있다.

- __Example__
  15cm의 머리카락을 주웠다, 여자의 머리카락인지 남자의 머리카락인지 확인하고 싶다
  - ML
    남자가 15cm 머리카락을 갖고 있을 확률과 여자가 15cm 머리카락을 갖고 있을 확률 비교
  - MAP
    15cm 머리카락을 가진 사람 중 남자의 비율이 많은지 여자의 비율이 많은지 비교
