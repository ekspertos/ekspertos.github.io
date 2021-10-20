---
layout: post
title:  "Link Budget Analysis"
subtitle: "디지털 통신"
categories: university Jekyll
tags: communications blog github pages jekyll spacy
use_math: true
comments: true
---

## 서론
> 통신 시스템의 실제 성능을 확인하기 위해서 수신 측에서 받는 SNR을 계산해야 한다
>
> 수신하는 SNR을 계산할 수 있도록 한 것이 `Link Budget` 이다
>
> 포스트에서 Link Budget 이 무엇인지 알아본다

$$
SNR(dB) = Recieved Signal Power(dBm) - Reciever Noise Power(dBm) \
\ \ \ \ \ \ = Transmitted Power(dBm) + Link Gains(dB) - Link Losses(dB) - Reciever Noise Power(dBm)
$$

![이미지](https://ekspertos.github.io/assets/img/university/2021-10-17-Link-Budget.PNG)

보통 기지국에서 43dBm의 출력으로 송신하면 우리의 스마트 폰에서 -110dBm 만큼 수신이 된다.
- __dBm to Watt__
  $
  43dBm = 40 + 3 = 10^4 * 2 = 20 \ Watt \\
  -110dBm = 10^{-11} = 10^{-14} \ Watt
  $

## Link Power
- __Transmit Power__
  안테나에서 송신하는 출력을 말한다
  송신할 수 있는 최대 출력이 전파법에 재정되어 있다

- __Noise Power__
  $$
  N = N_0 \times W \\
  N_0 = k \times T_r
  $$

  노이즈 파워는 noise spectral power density `N0`와 bandwidth 의 곱으로 계산되고, `N0 = kTr`는 볼츠만 상수 k와 수신 부의 온도를 이용해 구할 수 있다

  $$
  N_0(dB) = -173.9(dBm/Hz) + NF(dB)
  $$

  상온(약 20도)에서 -`174 dBm/Hz` 라는 값을 갖으며 온도에 따라 값이 증가한다

  ```
  [condition]
  상온에서 10MHz 대역폭을 이용할 경우의 노이즈 파워
  Interface ratio : -3dB (1/2)

  [result]
  `noise power`
  N = -173.9 dbm/Hz + 10log10^7 (dB*Hz) = -103.9dBm
  `interface power`
  N = -103dBm - 3dB = -106.9dBm
  `total power`
  N = 10log(10^(-10.69)+10^(-10.39))) = -102.2dBm
  ```
  Interface noise는 온도에 따른 노이즈 외의 노이즈들을 말한다(ex. )
  일반적으로 Interface noise에 대해 의미있는 값을 구하기 힘들기 때문에 고려하지 않는다.

  Interface와 noise는 곱하는 것이 아니라 더해야 하므로 위의 식을 이용한 것이다
  db 단위에서 더하는 의미는 실제로 서로 곱한다는 얘기이므로 이를 주의해야 한다.

- __Signal Power at the Reciever__
  `reciever sensitivity` 라고도 하며 요구된 Eb/N0 혹은 SNR 에 필요한 수신 전력을 구한다.

```
이것은 확실히 기억하고 가야한다
내가 송신 출력을 2배로 하든 채널 코딩을 해주든
SNR이 2배가 된다는 의미는 3dB 만큼 더해줘야 한다는 것이다. 1/4 된다는 것은 6dB를 해줘야 한다는 것이다.
```
```
2MHz bit - > 1MHz symbol
동일한 Eb/N0에서 required SNR 이 2배 증가한다
```

- __RSSI(Recieved Signal Strength Indicator)__
복잡한 추가적 복조 연산 과정을 포함하지 않은 수신 전력(단위 : [dBm])을 나타낸다.

수신 신호와 노이즈를 합한 신호이다.

$$
  r(t) = \alpha s(t) + n(t)
$$



## Link Gain
- __Antenna Gains__
  안테나로 Omni Antenna 와 Dipole Antenna 를 이용할 수 있다.
  ![이미지](https://cdn.shopify.com/s/files/1/1970/9543/t/12/assets/image-omni-combined.png?50163)
  한 방향으로 보내는 `Dipole Antenna`를 사용하면 특정 방향에 대한 Antenna Gain을 갖게되고 단위는 `dbi`로 정의된다.

  지향성 안테나는 beam forming 을 사용하며 현재 5G 기지국에서 사용되는 방식이다.

  특정 방향으로의 Gain을 갖는다




- __Diversity Gains__
  ![이미지](https://media.springernature.com/lw685/springer-static/image/art%3A10.1186%2Fs13638-019-1366-8/MediaObjects/13638_2019_1366_Fig3_HTML.png)
  송신 단에 안테나를 여러개 두거나 수신단에 여러개의 안테나를 두면 SNR을 증가시킬 수 있다.
  - __Tx Diversity Gain__
    여러개의 안테나를 사용하면 그중에 어느 하나를 수신해도 된다

  - __Rx Diversity Gain__
  $$
  r_0 + r_1 = 2\alpha + n + m \\
  SNR = \frac{4 \alpha^2}{2\sigma^2}
  $$
  2배로 증가된 SNR로 수신할 수 있고 다른 말로 하면 3dB 증가된 SNR을 얻을 수 있다.


  우리가 지금까지 말한 것은 Spacial Diversity 로, `Time Diversity (예. Interleaver), Freqiency Diversity` 도 있다.


  - __2개의 안테나 수신__
  $$
  \frac{(x + x)^2}{n^2+n^2} = 2 \times SNR
  $$
  2배의 SNR로 신호를 수신할 수 있다. 노이즈는 서로 correlation이 없기 때문에 위와 같이 계산된다.
  이러한 방식을 `Soft Combining Power Gain` 이라고 한다.

- __Coding Gains__
  channel coding에 의해 증가된 SNR이다
  `C = 1/4`로 channel coding 하면 6dB 증가된 SNR을 갖게된다



## Link Loss

- __Propagation Loss__
  모델링된 것 중 가장 많이 사용되는 것이 `HATA 모델`이다
  __HATA Model__
  ![이미지](https://ekspertos.github.io/assets/img/university/2021-10-17-HATA.PNG)
  HATA 모델에서 고려하는 파라미터는 다음과 같다

  - __Environment__
    환경에 따라서 구하는 공식이 달라진다. 예로, 자유공간에서 거리의 제곱에 비례해 신호가 감쇄되는 반면, 도심에서는 세제곱에 비례해 감쇄한다

  - __Link Distance__
    통신해야할 거리가 늘어날 수록 감쇄가 증가한다

  - __Center Frequency__
    신호는 `Direct Signal`(Line of Sight(LOS)) 뿐만 아니라 `Reflected 과 Diffracted Signal`(non-Line of Sight(non-LOS)) 을 통해 수신될 수 있다. 반사와 회절된 신호를 수신해도 정상적으로 동작할 수 있다.

    주파수가 증가할 수록 신호의 반사와 회절 능력이 감소하며 장애물을 뚫고 들어가면 결국 신호가 없어져 버린다.

    그래서 center frequency가 증가할 수록 loss가 증가한다

  - __Antenna Height Rx/Tx__
    일반적으로 안테나의 높이가 높을 수록 더 원할한 통신이 가능하다

- __Shadow / Fade Margin__
  ![이미지](https://ars.els-cdn.com/content/image/3-s2.0-B9780080508030500125-f07-06-9780080508030.gif)
  직선은 우리가 지금까지 알아본  `Macrosophic Path loss` 혹은 Long Term 특징이다.

  그러나 현실에서는 fading 혹은 Shadowing에 의해서 랜덤하게 움직임이 더해진다.

  이를 `Instantaneous/Microsophic Path Loss` 혹은 Short Term 특징이라고 한다

  Attenuation이 더 커지는 것이 문제이다

- __Transmission Losses__
  송신 부에서의 Loss이다 (impedence loss, cable loss 등)
- __Reception Loss__
  수신 부에서의 Loss이다


10의 SNR로 통신을 했다고 말했는데
고객이 느끼는 입장에서는 20의 Eb/N0으로 통신된 것이라고 느낀다




800MHz 대역을 SKT에서만 가지고 있었다
다른 통신사들은 2GHz를 사용하기 때문에 동일한 기지국이라도 1/2 거리만 지원 가능했다.

5G는 3.5GHz를 fade margin이 큰 도심에서 작동해야 하므로 거리를 늘리기 힘들다.
송신 Diversity를 만들어 약간의 완화를 시킨다.
