---
layout: post
title:  "SpecAugment: A Simple Data Augmentation Method"
subtitle: "A Simple Data Augmentation Method for Automatic Speech Recognition"
categories: projects Jekyll
tags: blog github pages jekyll spacy
use_math: true
comments: true
---

> [논문] SpecAugment: A Simple Data Augmentation Method for Automatic Speech Recognition
> 음성인식 모델 관련 논문을 찾아보면 SpecAugment가 대부분 사용되는 것을 볼 수 있다
> SpecAugment 논문에 어떤 기법들이 제시되어 있는지 알아본다.

## 1. Introduction
본 논문은 Augmenttation을 위한 3가지 기법을 소개한다.
1. Time Warping
2. Frequency Masking
3. Time Masking


## 2. Augmentation Policy

* __Time Warping__
 워핑은 주로 이미지에서 사용되며 Tensorflow에도 sparse_image_warp로 구현되어 있다.

 이미지 워핑은 쉽게 말해 이미지를 아래와 같이 찌그러트리는 기술이다.
 ![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-Image-warp.PNG)

 다음 그림은 이러한 워핑 기술을 mel-spectrogram 음성신호에 적용한 것이다.

 time warping은 신호에서 하나의 점을 기준으로 잡고 그 점을 오른쪽 혹은 왼쪽으로 밀어주는 과정이다.

 ![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-time-warp.PNG)
 아래에 있는 파형은 본 파형의 어떤 한 점을 기준으로 오른쪽으로 밀린 신호라고 볼 수 있다.
 오른쪽으로 밀렸기 때문에 오른쪽에 위치한 신호들은 짓눌린 형태(Squashed)로 보이고, 왼쪽에 위치한 신호들은 늘어진 형태(Stretched)로 보인다.

 상세한 과정은 다음과 같다.
  ![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-time-warp-image.PNG)
  1. 파란색으로 칠해진 여섯 지점을 고정시킨다
  2. [W, τ-W] 사이의 임의의 점 하나를 선택한다 (이미지 중앙의 수평선 위에 위차하는  점)
  3. [0,W] 사이의 선택된 값만큼 오른쪽 혹은 왼쪽으로 밀어준다

p 라는 파라미터를 두어

__[참고]
https://www.notion.so/SpecAugment-A-Simple-Data-Augmentation-Method-for-Automatic-Speech-Recognition-ece0a0be49844c7c93ab4f0045ee1562__
https://github.com/bobchennan/sparse_image_warp_pytorch


* Frequency / Time Masking
 ![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-masking.PNG)
 1. [0, T] / [0,F]사이의 t, f 값 선택 __(masking 폭 선정)__
 2. [0, τ-t] / [0, v-f] 사이에서 $ t_0, f_0 $  값 선택 __(masking 위치 선정)__
 3. $[t_0, t_0+t] / $[f_0, f_0+f]$ 사의 값들을 masking한다

본 논문은 LibriSpeech에 다음과 같은 파라미터를 사용해 Augmentation을 적용해 본다
![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-aug-options.PNG)
표에서 mF와 mT는 masking의 개수를 뜻하고, p는 time masking이 2개 이상일 때 겹칠 수 있는 최대 길이다. 예를 들어 SM과 SS의 경우 겹쳐진 time maksing의 길이가 전체의 0.2를 넘을 수 없다.


## 4. Experiment
![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-aug-result.PNG)

다음 표는 3가지 기법들 중 각각의 기법을 하지 않았을 경우의 WER을 보여준다.
- time warping : 3.8%
- frequency masking : 4.0%
- time masking : 4.1%

결과를 보면 알 수 있듯이 time warping은 성능에 어느정도 영향은 주지만 성능 향상의 주요한 요인은 아니다.
```
Time warping, being the most expensive as well as the least influential of the augmentations.
```

![이미지](https://ekspertos.github.io/assets/img/review/2021-10-13-underfitting.PNG)
학습 데이터에 overfitting 되는 문제를 Augmentation을 이용해 해결할 수 있다.


또한 본 논문에서는 이러한 Augmentation 기법들과 Label smoothing을 같이 사용하면  불안정한 학습이 진행될 수 있다고 한다. 특히 lr 이 감소되는 지점에서 더 크게 나타난다고 한다. 이것을 줄이고자 본 논문은 lr이 decay된느 지점에서는 label smoothing을 하지 않았다.
