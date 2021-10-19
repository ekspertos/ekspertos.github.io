---
layout: post
title:  "디지털과 아날로그 통신"
subtitle: "Sampling & DAC"
categories: university Jekyll
tags: communications blog github pages jekyll spacy
use_math: true
comments: true
---

## 디지털과 아날로그의 특성
- __Analog System__
  - 품질이 우수하다
  - 노이즈에 약하다
  - 해상도에 제한이 없다

- __Digital System__
  - 품질에 제약이 있다
  - 외부 노이즈에 강하다



## 아날로그 신호
신호를 아날로그로 저장하는 대표적인 매체는 LP판이다
LP 판은 음성의 음압에 따라 다른 깊이의 구멍을 디스크에 새긴다

브라운 운동을 하는 입자들에 영향을 받아 새기는 과정에서 노이즈가 생긴다
아날로그로 저장된는 LP판을 이용하면 ``노이즈가 섞인 소리를 들을 수 밖에 없다``

## 디지털 신호
아날로그 신호의 `Sampling`과 `Quantization` 과정을 통해 디지털 신호를 만든다

Sampling과 Quantization 과정을 지나기 때문에 원래 정보를 정확하게 저장하지 못하므로 품질 저하는 필연적이다.

### Sampling
  Nyquist Theorm에 의해 최대 주파수보다 2배 높은 sampling rate을 이용해 신호를 표본화한다.
  T 시간마다 표본화된 값을 구할 수 있다.

  > __[위키백과] Sampling Theorm__
  >
  > "만약 신호가 대역제한(bandlimited)신호이고, 표본화 주파수가 신호의 대역의 두 배 이상이라면 표본으로부터 연속 시간 기저 대역 신호를 완전히 재구성할 수 있다."
  >

### Quantization
  Sampling 된 신호를 실제 디지털 장비들에서 사용할 수 있도록 바꾸어준다. 즉, 파형의 진폭을 나타내는 실수 데이터를 컴퓨터에서 사용되는 bit 데이터로 바꾸어주는 과정이다.
  ![이미지](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/3-bit_resolution_analog_comparison.png/220px-3-bit_resolution_analog_comparison.png)
  다음 그림은 3-bit (Analog-Digital Converter)ADC의 예이다.

  그림은 111~000 순서대로 부호화를 하였지만 이것은 선택이다. 다른 방식으로 부호화를 해줄 수도 있으며, 송신자와 수신자 간의 ``매핑룰이 공유``된 상태면 어떤 부화화 방식을 선택하던 자유이다.

  Quantization은 uniform과 non-uniform으로 나뉜다.

  - Uniform Quantization
    Quantization에서 표현될 수 있는 범위를 `Dynamic range`라고 한다.
    dynamic range에서 동일한 범위의 양자화를 하는 것을 uniform Quantization이라고 한다.

  - non-uniform Quantization
    신호가 밀집된 곳에서는 더 촘촘히 양자화하는 것을 non-uniform Quantization이라고 한다.

  Quantization에서 표현될 수 있는 범위를 `Dynamic range`라고 한다.
  이 dynamic range를 벗어나는 underflow와 overflow를 합쳐 `saturation`이라고 한다.
  같은 부호화 bit를 사용하면서 Dynamic Range를 줄이면 우리가 보는 해상도가 좋아진다. `dynamic range와 resolution 사이에 trade-off`가 존재한다.

  모든 신호가 Dynamic Range에서 수신될 수 있도록 사용할 수 있는 두가지 방법이 있다.

  1. AGC(Automatic Gain Control)
  ```
  [위키백과] Automatic Gain Control
  자동 이득 제어는 증폭기 또는 증폭기 체인의 폐쇄 루프 피드백 조절 회로로, 그 목적은 입력에서 신호 진폭의 변화에도 불구하고 출력에서 ​​적절한 신호 진폭을 유지하는 것입니다
  ```
  작은 신호가 들어올 경우 증폭시키고 큰 신호가 들어오면 감쇄시킨다
  감쇄와 증폭을 모두 사용해 Dynamic Range에서 동작할 수 있도록 한다

  ``출력 파워를 일정하게 유지하도록 하는 장치`` , 진폭을 조절하는 것이 아닌 신호 파워를 조절한다

  2. ALC(Automatic Level Control)
  Analog RF에서 주로 사용하며, 일정 범위를 넘어서는 신호를 조절할 수 있다



## OverSampling

DAC 오버샘플링을 알아본다.
![이미지](https://ekspertos.github.io/assets/img/university/2021-10-19-oversampling.PNG)


\\<!--

## 3G / 4G 음성부호화
  > 3G 휴대전화의 구조 (14)-자연스러운 영상을 전송하는 부호
  >
  >https://itlab.co.kr/v7/?m=bbs&bid=mobile&cat=3G%2F4G%2F%ED%9C%B4%EB%8C%80%EC%9D%B8%ED%84%B0%EB%84%B7&sort=score1&uid=3319)


  1. 단말기에 입력된 음성의 파형을 10mm~50mm초 단위로 구분
  2. 이 신호에 가장 가까운 디지털 음을 생성하는 데 필요한 파형 및 소리의 크기 등을 나타내는 코드를 코드북에서 선택
    코드북이란 단말기 내에 있는 데이터베이스로서, 음성에서 빈번하게 사용되는 신호파형에 식별용 번호가 붙어있다.
  3. 수신한 측에서는 송신측과 같은 코드북을 통해 파형을 합성
  4.  배열해 음성신호를 복원


## LG QDAC
> What is the LG V20's "Quad DAC" and how does it affect audio quality?
>
> https://www.androidauthority.com/lg-v20-quad-dac-explained-713587/

컴퓨터 멀티미디어 제품, 오디오 DAC 및 ADC의 민간 제조업체 `ESS Thechonolgy에서 개발한 ES9218` 제품이다.

이 제품은 32-bit 394kHz & DSD512 까지 지원한다.
  - 샘플링 주파수 : 394kHz
  - 부호화 : 32-bit
  - DSD : 512

- 장점
여러개의 DAC를 사용해 가청영역의 노이즈를 줄인다. 그 과정에서 고주파 신호에 대한 노이즈가 증가한다.
![이미지](https://cdn57.androidauthority.net/wp-content/uploads/2016/09/Multiple-order-Modulator-performance.png.webp)
```
TI The use of multi-order modulators results in more high frequency noise, but less noise in the audible range that we actually care about.
```


DAC의 동작 과정
  1. OverSampling
  ```
  CD quality 44.1kHz file could be oversampled 4 times to produce a 176.4kHz output, or 16 times to reach a huge 705.6kHz sample rate. This doesn’t increase the quality of your audio file directly, but it has the very helpful benefit of spreading this aliasing noise into much higher frequencies.
  ```
  2. digital sigma-delta modulator
  PCM 데이터를 PPM bit stream으로 변환한다.
  ```
  This takes our 16 or 24-bit signal and converts it into a 1-bit data stream, but with a much higher sample rate. This spreads the noise across even more frequencies.
  ```

## DSD 란
> DSD가 뭐죠? 뭐가 좋은 거예요?
> https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=true4voice&logNo=220857375123

PCM(Pulse Code Modulation)은 ``파형의 진폭`` 즉, 높낮이를 기준으로 데이터를 기록하고, DSD(Direct Stream Digital)은 ``파형의 밀도``만을 놓고 데이터를 기록한다

밀도가 높으면 기준점보다 높은 위치, 밀도가 낮으면 기준점보다 낮은 위치에 저장한다.
높낮이를 무시하기 때문에 DSD 파일은 모두 1 비트를 사용한다.

DSD512는 샘플링주파수 394kHz 보다 412배 높은 주파수로 DSD를 해준 다는 의미이다.

-->
