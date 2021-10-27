---
layout: post
title:  "Transformer-Encoder"
subtitle: "Transformer(3)"
categories: projects
tags: blog github pages jekyll spacy transformer
comments: true
---


## 서론
>
> Librispeech 데이터셋에서 8위에 위치한 conformer에 대해 알아본다
>
>https://paperswithcode.com/paper/conformer-convolution-augmented-transformer
>
> https://github.com/espnet/espnet/blob/master/espnet2/asr/encoder/conformer_encoder.py


## Introduction
시퀀스를 모델링하는데 `Transformer 구조`(self-attention을 사용한 기법), `convolution 구조`가 최근 많이 사용된다

Transformer, Convolution, RNN 모두 각각 장단점이 있다

- __Transformer__ : long distance interactions, high training efficiency
  서로 다른 두 지점 간의 interaction을 한다
- __Convolution__ : capture local context efficiently
  커널 범위 내 지점들 간의 interaction을 한다. edge와 shape에 관한 feature들을 뽑아낼 수 있다.
- __RNN__ : wide range interactions
  전체 시퀀스 간의 interaction을 한다

__단점__
- __Transformer__ :local feature pattern을 파악하기 어렵다
- __Convolution__ : global 정보들을 파악하기 위해 더 많은 layer와 parameter가 필요하다

최근에 self-attention과 convolution을 결합하므로써 이러한 단점들을 개선할 수 있다고 알려졌다

![이미지](https://cosmoquester.github.io/assets/post_files/2021-05-05-convolution-augmented-transformer-for-speech-recognition/1.png)

본 논문은 음성의 global한 정보와 local한 정보들을 모두 capture할 수 있는 `Conformer model`을 소개한다

---

###__Conformer Model 구조__
1. SpecAug
2. convolution subsampling Layer
3. Linear
4. Dropout
5. Conformer Blocks
  5.1 macaron-style FeedForward Module
  5.2 multi-head self Attention Module
  5.3 Convolution Module
  5.4 macaron-style FeedForward Module

---

## Subsampling Layer
```
[논문의 음성 feature extraction 방식]
80-channel filterbanks features computed from 25ms window with a stride of 10ms
```
10ms 마다 뽑아낸 데이터들을 1/4 크기로 sub-sampling 한다.
stride 크기가 2인 convolution 블록을 2개 사용해 구현한다

## Feed Forward Module
residual connection, pre-norm, swish 활성화 함수, dropout을 함께 사용한다
![이미지](https://user-images.githubusercontent.com/1694368/103190710-1b847480-490d-11eb-8ea5-280749a32a24.png)

첫번 째 linear layer 에서 feature을 4배로 증가시킨 후 두번 째 linear layer에서 다시 원래대로 돌려놓는다

## Multi-Headed Self-Attention Module
Transformer-XL 논문에서 제안한 `relative positional embedding`이 포함된 multi-head self-attention(MHSA)을 사용한다.

self-attention이 다양한 말뭉치 길이를 받아들일 수 있도록 사용한다.

```
[관련 논문]
Relative Positional Encoding for Speech Recognition
and Direct Translation
https://arxiv.org/pdf/2005.09940.pdf

Transformer-XL: Attentive Language Models
Beyond a Fixed-Length Context
https://arxiv.org/pdf/1901.02860.pdf
```

 정규화를 위해 추가적으로 `pre-norm`, `residual`, `dropout`을 사용한다
![이미지](https://camo.githubusercontent.com/24db98539d02655eeda50a2fd0447237566611611b3b311a896459fe789b97a7/68747470733a2f2f696d616765732e6465657061692e6f72672f636f6e7665727465642d7061706572732f323030352e30383130302f78332e706e67)

기존 트랜스포머는 Post-norm인데 반해, pre-norm 적용하며, 이전 연구들에서 pre-norm은 깊은 모델 학습이 원활하게 되도록 도와주는 효과가 있다고 알려진다

## Convolution Module
Depthwise-Seperable Convolution와 비슷한 방식을 사용한다.
두가지 convoultion, depthwise와 pointwise convolution을 사용한다.

- __Depthwise Convolution__
![이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FAyNcz%2FbtqAmtNdX2P%2FUTwjESXKnxRUYuPgHKTEp1%2Fimg.png)
  feature 단위로 분리해 Conv1d를 따로따로 해준 후 나온 결과들을 합친다

- __Pointwise Convolution__
![이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fda6O8n%2FbtqAjWJOKs3%2FAVCwUhwvvZegr65yFhPBVk%2Fimg.png)
  Depthwise를 통해 채널의 개수를 바꿀 수 없으므로 1x1 kernel을 이용한 conv1d를 통해 출력 채널의 개수를 바꾸어준다

[o_channel, i_channel, W, H] 크기의 필터가 필요하던 기존의 convolution에 비해 적은 파라미터를 사용해 구현 불가능하다

![이미지](https://user-images.githubusercontent.com/42150335/105454437-30aeb200-5cc5-11eb-8624-1ea49b71c8cd.png)

leyernorm, GLU 활성화함수, batchnorm, swish 활성화함수, dropout을 함께 사용해 Convolution Module을 형성한다


PointWise에서 feature을 2배로 증가시킨다

## Conformer Block
![이미지](https://user-images.githubusercontent.com/42150335/105326425-13b8a700-5c11-11eb-804c-bd8efef6060b.png)

Transformer 논문에서 한개의 FFN을 사용한 반면 본 논문에서는 Macaron-Net에 영감을 받아 `2개의 FFN을 샌드위치처럼 쌓은 구조`를 사용한다
FFN 모듈에 half-step residual connection을 사용한다.



## Experiment
__구현 환경__
- __log-mel spectrogram__ : 80-channel filterbank
- __window__ : 20ms
- __stride__ : 10ms

- __SpecAugment__  
  - __frequency__ : F = 27
  - __time__ : $m_t$ = 10, p = 0.05, T = (utterance x p)

__Conformer Transducer__
- __encoder models__ : small(10M), medium(30M) and large(118M)
- __decoder__ : single LSTM layer
- __dropout__ : $P_{drop}$ : 0.1
- __L2 Regularization__ : $l_2$ : 0.06
- __Transformer Learning rate schedule__
  - __Warm-up__ : 10k
  - __peak lr__ : 0.05/$\sqrt{d}$ (`d`: model dimension in conformer encoder)

  ![이미지](https://user-images.githubusercontent.com/42150335/105327556-5cbd2b00-5c12-11eb-8714-2c0ce2c7a1b0.png)

## Result on LibriSpeech
- __다른 모델들과의 비교__
![이미지](https://user-images.githubusercontent.com/42150335/105327620-752d4580-5c12-11eb-9091-433ce8700141.png)

- __구성 요소들 중요도__
![이미지](https://user-images.githubusercontent.com/42150335/105327876-c9d0c080-5c12-11eb-8b02-948f87c5f47d.png)

- __구성 요소들 중요도__
![이미지](https://user-images.githubusercontent.com/42150335/105328157-1916f100-5c13-11eb-9473-69ac0c658e15.png)

- __Attention-Head 개수 비교__
 ![이미지](https://user-images.githubusercontent.com/42150335/105328376-54b1bb00-5c13-11eb-9059-38bc7361ba6d.png)


- __depthwise convolution kernel size 비교__
![이미지](https://user-images.githubusercontent.com/42150335/105328408-5aa79c00-5c13-11eb-94b2-8ee455c8daca.png)

32 kernel size까지 학습 결과가 지속적으로 좋아지는 것을 확인 할 수 있다.
