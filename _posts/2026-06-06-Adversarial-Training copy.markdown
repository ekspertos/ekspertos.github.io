---
layout: post
title:  "Adversarial Training"
subtitle: "Adversarial Training"
categories: projects
tags: blog github pages jekyll spacy
comments: true
hidden: false
---

> 딥러닝은 연구 단계를 넘어 우리 일상생활의 다양한 분야에 활용되고 있다. 대표적인 예로 자율주행, 문자 인식(OCR), 음성 인식, 스팸 탐지 등이 있으며, 이러한 기술들은 우리의 삶을 더욱 편리하게 만들어 주고 있다.
>하지만 딥러닝 모델이 잘못된 판단을 내릴 경우 그 영향은 단순한 오류를 넘어 심각한 사고로 이어질 수 있다. 예를 들어 도로에 출입금지 표지판이 있을 때 자율주행 시스템이 이를 올바르게 인식한다면 해당 도로로 진입하지 않을 것이다. 그러나 누군가 표지판에 작은 스티커를 붙이거나 특정한 패턴을 추가했을 때, 사람은 여전히 이를 출입금지 표지판으로 인식하지만 딥러닝 모델은 다른 표지판으로 오인식하거나 아예 인식하지 못할 수 있다. 그 결과 차량이 잘못된 경로로 진입하는 등의 문제가 발생할 수 있다.
> 이처럼 입력에 사람이 인지하기 어려운 작은 변화를 가해 모델의 예측을 크게 변화시키는 입력을 **adversarial example(적대적 예제)** 이라고 한다. 이번 포스트에서는 현실 세계에서 발생할 수 있는 adversarial example의 사례를 살펴보고, 이러한 현상이 왜 발생하는지 그리고 이를 방어하기 위한 방법에는 무엇이 있는지 알아본다.


---

## Real-World Examples of Adversarial Attacks
### 1. Autonomous Driving

자율주행 시스템은 도로 표지판 인식, 차선 인식, 보행자 탐지 등 다양한 컴퓨터 비전 기술에 의존한다.

실제로 *Robust Physical-World Attacks on Deep Learning Models* 연구에서는 정지 표지판에 검은색과 흰색 스티커를 부착하여 딥러닝 모델이 이를 다른 표지판으로 오인하도록 만들었다. 연구진은 실제 도로 환경에서 촬영한 영상에서도 공격이 성공함을 보였으며, 이동 중인 차량이 촬영한 프레임의 상당수에서 오인식이 발생하였다. 


<img src="https://ekspertos.github.io/assets/img/review/Adversarial/AdversarialExamples_StopSign.jpg" width="400">


또 다른 연구에서는 도로 위에 단순한 검은 선 몇 개를 추가하는 것만으로 차량이 잘못된 방향으로 조향하도록 유도할 수 있음을 보였다.

---

### 2. Optical Character Recognition (OCR)

OCR은 문서 자동 처리, 수표 금액 인식, 차량 번호판 인식 등 다양한 산업 분야에서 활용된다. 그러나 입력 이미지에 사람이 인지하기 어려운 작은 변화를 추가하면 OCR 시스템이 전혀 다른 문자나 숫자로 인식할 수 있다. 예를 들어 은행의 수표 처리 시스템이 금액을 잘못 읽거나, 번호판 인식 시스템이 차량 번호를 잘못 인식할 가능성이 존재한다. 이러한 공격은 이미지 분류 모델뿐만 아니라 실제 문서 처리 파이프라인 전체에 영향을 줄 수 있다.

---

### 3. Speech Recognition

음성 인식 기술은 Siri, Alexa, Google Assistant와 같은 음성 비서 서비스에 널리 사용되고 있다. 연구자들은 사람이 알아듣기 어려운 음성 신호를 생성하더라도 음성 인식 시스템은 이를 정상적인 명령으로 해석할 수 있음을 보여주었다. 예를 들어 사용자는 아무런 명령을 듣지 못했지만 시스템은 특정 웹사이트를 열거나 기기를 조작하는 명령으로 인식할 수 있다. 이러한 공격은 Hidden Voice Commands와 같은 연구를 통해 실제로 시연되었다.
대표 논문:
- Hidden Voice Commands (Carlini et al., 2016)

---

### 4. Spam Filtering

#### 이메일 필터
스팸 메일 작성자는 특정 단어를 변형하거나 무의미한 단어를 삽입하여 스팸 탐지기를 우회할 수 있다.

예시:

- free → fr33
- viagra → v1agra

실제로 adversarial machine learning이 연구되기 이전부터 사용되던 공격 방식이다.

---

### 5. Malware Detection

악성코드 탐지 시스템은 실행 파일이나 애플리케이션의 특징을 분석하여 악성 여부를 판단한다. 공격자는 악성코드의 실제 동작은 유지한 채 일부 코드나 데이터만 수정하여 탐지 모델을 우회하려고 시도한다. 이는 이미지에 작은 노이즈를 추가하여 분류 결과를 바꾸는 adversarial example과 매우 유사한 구조를 가진다. 악성코드 탐지는 공격자의 경제적 동기가 매우 강하기 때문에 adversarial machine learning이 실제 보안 분야에 적용되는 대표적인 사례로 평가된다.

대표 논문:

- ATMPA: Attacking Machine Learning-based Malware Visualization Detection Methods via Adversarial Examples (2018)
- AMGmal: Adaptive Mask-Guided Adversarial Attack against Malware Detection (2023)

공격자는 실제 금전적 이득을 얻을 수 있기 때문에 adversarial attack의 가장 현실적인 응용 분야 중 하나로 평가된다. :contentReference[oaicite:0]{index=0}

---

### 6. Face Recognition

#### Face ID
#### 공항 출입국 심사
#### 회사 출입 통제

특수 안경, 모자 또는 얼굴 패턴을 이용하여 얼굴 인식 시스템을 우회할 수 있다.

예시:

- 공격자를 다른 사람으로 인식
- 공격자를 탐지하지 못함

대표 논문:

- AdvHat: Real-world Adversarial Attack on ArcFace Face Recognition Systems

---

### 7. Medical AI

#### X-ray
#### CT
#### MRI

의료 영상에 사람이 인지하기 어려운 작은 변화를 추가하여 진단 결과를 바꿀 수 있다.

예를 들어:

- 암 → 정상
- 정상 → 암

으로 분류 결과를 변경하는 연구가 보고되었다.

대표 논문:

- Adversarial Attack Vulnerability of Medical Image Analysis Systems (2020)
- Universal Adversarial Attacks on Deep Neural Networks for Medical Image Classification (2021)

의료 AI는 진단과 보험 청구 등에 활용되므로 공격 성공 시 금전적·의료적 피해가 발생할 수 있다. :contentReference[oaicite:1]{index=1}

---

### 8. Financial AI

#### 신용평가
#### 사기 거래 탐지
#### 대출 심사

거래 패턴이나 입력 정보를 조작하여

- 사기 거래를 정상 거래로 인식
- 대출 심사를 통과

시키는 공격이 가능하다.

이 분야는 실제 금융 기관들이 adversarial robustness를 연구하는 주요 이유 중 하나이다.

---

### 9. Recommendation Systems

#### YouTube
#### Netflix
#### 쇼핑몰

사용자 행동을 인위적으로 생성하여 특정 콘텐츠를 추천하도록 유도할 수 있다.

예시:

- 특정 상품 순위 상승
- 특정 영상 추천 유도
- 가짜 리뷰 생성

이러한 공격은 보통 shilling attack 또는 poisoning attack으로 연구된다.

---

### 10. LLM / Generative AI

#### ChatGPT
#### Claude
#### Gemini

입력 프롬프트를 조작하여 모델이 원래 의도하지 않은 출력을 생성하도록 만들 수 있다.

예시:

- Prompt Injection
- Jailbreak
- Indirect Prompt Injection

LLM 시대에는 adversarial example의 개념이 이미지뿐 아니라 텍스트 영역으로도 확장되고 있다.


---

## 논문 요약

1. **Explaining and Harnessing Adversarial Examples (2014) — 20.6k citations**
2. **Towards Evaluating the Robustness of Neural Networks (2015) — 13.5k citations**
3. **Towards Deep Learning Models Resistant to Adversarial Attacks (2017) — 19.6k citations**
4. **Adversarial Patch (2018) — 1.8k citations**
5. **Certified Robustness to Adversarial Examples with Differential Privacy (2018) - 1.4k citations**
6. **Constructing Unrestricted Adversarial Examples with Generative Models (2018) — 0.4k citations**
7. **Obfuscated Gradients Give a False Sense of Security (2018) — 4.4k citations**

---

## 논문 리뷰

### 1. Explaining and Harnessing Adversarial Examples (2014) — 20.6k citations

---

### 2. Towards Evaluating the Robustness of Neural Networks (2015) — 13.5k citations

---

### 3. Towards Deep Learning Models Resistant to Adversarial Attacks (2017) — 19.6k citations

---

### 4. Adversarial Patch (2018) — 1.8k citations

---


### 5. Certified Robustness to Adversarial Examples with Differential Privacy (2018) - 1.4k citations

---

### 6. Constructing Unrestricted Adversarial Examples with Generative Models (2018) — 0.4k citations


---

### 7. Obfuscated Gradients Give a False Sense of Security (2018) — 4.4k citations

---


### 8. Adversarial Examples Are Not Bugs, They Are Features (2019) — 2.7k citations



---

## 참고

https://songsite123.tistory.com/78


Towards the Science of Security and Privacy in Machine Learning
https://jonathan-hui.medium.com/adversarial-attacks-b58318bb497b



https://chatgpt.com/c/6a0b049b-d58c-8322-992e-0f8bcb9b9723

