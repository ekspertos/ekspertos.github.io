---  
layout: post  
title: "자연어처리 논문 리뷰"  
subtitle: "리뷰를 해보자"  
categories: review  
tags: review  
comments: true  
---  

## 개요
> 딥러닝 자연어처리가 응용되는 분야를 찾아보려고 합니다.
> 여러 논문들을 번역하여 정리했습니다.

## 1. 음악 정보 검색 및 분류
[Music Information Retrieval and Classification using Deep Learning](https://www.academia.edu/49290410/Music_Information_Retrieval_and_Classification_using_Deep_Learning)

## 2. Normalization of Numeronyms
adavpur University에서의 [논문](https://ieeexplore.ieee.org/abstract/document/9106524)을 바탕으로 작성되었습니다.

* __numeronym이란?__
영어권에서는 길이가 긴 단어를 축약할 때 숫자를 사용합니다. 예로 송곳니를 뜻하는 canine은 K9이라고 하지요.
 k9 : Canine
 l10n : Localization
 i18n : Internationalization
```
The need to communicate and connect with the younger audience
is the main reason to adopt language localization
```
젊은 층들과의 소통을 위해 이런 축약된 말들을 사용할 필요가 있다고 합니다.
<span style=" color:gray"># [참고] [numeronym 모음](https://nhj12311.tistory.com/145)</span>

---

* __두가지 모델__
축약어를 정규화된 단어로 바꾸어주기 위한 두가지 방법을 제시한 후 정확도를 비교했습니다.
   1. Daumerau-Levenshtein distance & Cosine similarity
  영어와 벵골어에 대해 각 ``71%, 72%``의 정확도를 보였습니다.
   2. Deep Learning Architecture
  영어와 벵골어에 대해 모두 `89%`의 정확도를 보였습니다.

---

* __Dataset__
`8000개의 영어 축약어와 2000개의 벵골어 축약어`를 수집했습니다. 또한 축약어들을 정규화된 단어들로 바꾸는 작업을 해주었습니다.
---

## 3. Text Summerization Techniques
정보가 넘치는 시대에 우리는 헤매지 않기 위해 노력해야 합니다. 기사를 요약해 주는 모델이 이 과정에서 도움이 될 수 있겠지요.
[Abstractive Text Summarization Techniques using NLP](https://ieeexplore.ieee.org/abstract/document/9213079)

---
