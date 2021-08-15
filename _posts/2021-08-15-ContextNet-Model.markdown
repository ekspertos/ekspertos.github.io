---
layout: post
title:  "ContextNet Review"
subtitle: "ContextNet"
categories: projects
tags: blog github pages jekyll spacy
comments: true
---


## 서론
> 현재 시점의 [Librispeech 벤치마크](https://paperswithcode.com/sota/speech-recognition-on-librispeech-test-clean)에서 3번째와 7번째를 차지하고 있는 ContextNet 모델에 대해 알아보겠습니다.
> ContextNet에 대한 논문을 참고해 작성했습니다. https://arxiv.org/abs/2005.03191
>
---

## ContextNet 요약
ContextNet은 Alignment 방식 중 하나인 `RNN Transducer`을 기반으로 합니다.
![이미지](https://3.bp.blogspot.com/-Z_W8MILyUsk/XIauDigQ93I/AAAAAAAAD5E/0LsZWC3mWaIDrzSN0I4QCGGBWBzg5XNYgCEwYBhgL/s1600/image2.png)

기존의 RNN-T와의 차이점으로 __RNN-T__ 의 경우 오디오 입력 간의 dependency를 인코딩하는데 `LSTM` 계층을 사용하지만
__ContextNet__ 의 경우에는 이 과정에  `Squeeze-and-Excitation Network이 추가된 CNN` 계층을 사용합니다.

![이미지](https://user-images.githubusercontent.com/54731898/122670336-864ba400-d1fc-11eb-985e-e40e20339a68.png)
`1D Convolution`이  오디오 ``시간`` 사이의 dependency를 인코딩하고
`Squeeze-and-Excitation Network`는 오디오 ``주파수`` 사이의 dependency를 인코딩하며
단순한 LSTM을 이용한 방식보다 더욱 구체화된 신호 간 dependency를 얻을 수 있지요.

---


## 들어가며
CNN을 end-to-end(E2E) 음성인식 모델들이 많은 관심을 받고 있습니다.
그 중 하나인 Jasper 모델은 최근 SOTA에 근접한 2.9%의 WER을 기록한 바가 있습니다.
Jasper 모델의 주요 특징으로 인코더로 사용된 `다층으로 쌓여진 1D convolution과 Skip connection`이 있으며
Convolution 기법 중 하나로 `depth-wise convolution`을 체택해 더 높은 속도와 정확성으로 모델을 학습 시킬 수 있었습니다.
다른 모델들과 비교해서 ``파라미터 개수가 적다는 장점``이 있지만, WER 면에서는 RNN/ㅆransformer 기반의 모델들에 현재 뒤쳐져 있습니다.

CNN 모델, RNN/Transformer 기반 모델들의 차이점를 알아보겠습니다.

> `bidirectional RNN Model`: 각 셀들이 전체 시퀀스에 관한 정보를 접근할 수 있습니다
> `Transformer Mdel`: 서로 다른 노드 두 곳이 서로를 접근할 수 있습니다
> `Naive CNN Model`: 제한된 커널 사이즈 때문에 제한된 시간 도메인 에 관한 정보만 접근할 수 있습니다

다른 모델들은 시퀀스의 ``전체적인 문맥``을 접근할 수 있는 반면, CNN 모델은 ``제한된 문맥 정보``만을 접근할 수 있기 때문에
더 낮은 WER을 보인다고 볼 수 있습니다.

이런 문제를 해결하기 위해 해당 논문에서 `Squeeze-and-Excitation(SE)` 계층을 포함한 새로운 CNN 모델을 사용하고
이 CNN 모델을 `ContextNet`이라고 부릅니다.
![이미지](https://i.imgur.com/9UFjxDA.png)

CNN의 입력은 여러개의 `feature`로 나누어져있고 `feature`은 각각의 `local feature vector`를 가지고 있습니다.
예를 들어 이미지의 경우 입력으로 RGB 세가지의 feature을 가지고 있으며 색마다 다른 값을 갖고 있습니다.
ContextNet은 feature로 `frequency`를 이용합니다,

이 각각의 피처 백터들이 서로의 정보에 접근할 수 있돌고 하는 것이 SE 계층이며,
SE 계층의 결과로 피처들 간의 정보가 포함된 `global context vector`를 갖습니다.  

마지막으로 피처들 간의 정보가 포함된 `global context vector`와 각 feature에 대한 정보를 갖는 `local feature vector`를 곱하여 ``전체적인 문맥 정보가 포함된 결과 값``을 얻을 수 있습니다.
기존의 CNN 계층 뒤에 SE 계층을 위치 시킴으로 이를 이룰 수 있을 것입니다.



ContextNet은 또한 `QuartzNet의 design choic`e들에 영향을 받았습니다.
예를 들어 QuertzNet의 `depthwise convolution`을 논문의 모델에서도 체택했습니다.
다른점도 많은데 SE 계층을 사용한다는 것 뿐만 아니라, QuartzNet에서 Alignment 방식으로 CTC 디코더를 이용하는 것과 달리
논문의 모델은 RNN-T 디코더를 사용합니다.
또, Swift 활성화 함수를 사용하므로써 조금이지만 지속적인 WER 감소를 야기합니다.

결과적으로 ContextNet은 LibriSpeech test-clean/test-other 에서 `1.9%/4.1%` Word Error Rate으로
현재까지의 CNN 모델들 중에 가장 좋은 결과를 보였으며 transformer과 LSTM기반의 모델들의 결과를 압도했습니다.

---
## Encoder Design
![이미지](https://user-images.githubusercontent.com/54731898/122670336-864ba400-d1fc-11eb-985e-e40e20339a68.png)
![이미지](https://user-images.githubusercontent.com/54731898/122670784-abd9ad00-d1fe-11eb-8be1-c1aa8f97a7bf.png)
![이미지](https://user-images.githubusercontent.com/54731898/122670308-4b497080-d1fc-11eb-93ae-cd2bd179440c.png)


---
## Depthwise Seperable Convolution
> 블로그 https://wingnim.tistory.com/104 의 내용을 참고했습니다
> 다음 글 https://towardsdatascience.com/a-basic-introduction-to-separable-convolutions-b99ec3102728 을 참고했습니다


Depthwise Seperable Convolution은 `depthwise convolution`과 `pointwise convolution` 두가지의 convolution을 같이 사용합니다.

* __DepthWise Convolution__
H*W*C의 conv output을 C 단위로 분리하여 각각 conv filter을 적용하여 output을 만들고 그 결과를 다시 합치면 conv filter가 ``훨씬 적은 파라미터``를 가지고 동일한 크기의 아웃풋을 낼 수 있다.
![이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAyNcz%2FbtqAmtNdX2P%2FUTwjESXKnxRUYuPgHKTEp1%2Fimg.png)


* __PointWise Convolution
1x1 Conv라고 불리는 필터이다. 기존의 Depthwise에서
channel 개수를 바꿔주지 못하기 때문에 1x1conv를 통해 출력의 채널 개수를 바꾸어준다.
![이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fda6O8n%2FbtqAjWJOKs3%2FAVCwUhwvvZegr65yFhPBVk%2Fimg.png)


3x3x3x3 필터를 이용한 컨볼루션을 하기 위해서 기존에는 ``81개의 파라미터``가 필요하지만

depthwise seperable convolution에서는
dethwise에서 3x3 3개로, 27개가 필요하고
pointwise에서는 1x1 3개로, 3개가 필요하다
총 30개의 파라미터로 81개의 파라미터와 같은 역할을 할 수 있다.

ContextNet에서는 1d convolution을 frequency마다 해준 후 통합해주고 1x1 conv을 통해 channel을 맞춰준다
