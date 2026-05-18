---
layout: post
title:  "Data Regularization"
subtitle: "Regularization"
categories: projects
tags: regularization DeepLearning MachineLearning
comments: true
---

 
> 딥러닝 모델은 입력과 출력 사이의 패턴을 학습하고 학습된 패턴을 기반으로 새로운 입력에 대해 적절한 출력을 예측한다.
> 그리고 다양한 입력에 대해 잘 일반화(generalization)하기 위해 다양한 상황를 포함한 충분한 데이터가 필요하다.
> 하지만 실제로 모든 상황을 고려한 입력을 수집하는 것은 불가능하기 때문에 제한된 입력으로만 학습이 되는 경우가 많다.
> 데이터가 부족 하면 모델은 사전에 접한 입력에만 출력을 예측하고 새로운 입력에 대해 제대로 응답을 예측 못할 수 있다. 
> 이러한 `과적합` (overfitting) 현상을 완화하기 위해 실제로는 모델 복잡도를 조절하거나 학습을 안정화하는 다양한 `정규화` (regularization) 기법들이 사용된다.
> 이번 포스트에서는 최근 모델에서 일반적으로 사용되는 다양한 정규화 기법들을 자세히 살펴본다.


이번 포스트에서는 `A survey of regularization strategies for deep models(2019)` 논문에서 다루는 정규화 기법들에 대해 자세히 알아본다.


 

|**기법**|**설명**|
|:-|--------------|
|**[1]  [Weight Decay](#AAA)**|로스 함수에 패널티 항을 추가해 모델 가중치(weight)들이 작아지도록 유도|
|**[2]  Noise Injection**|입력에 무작위 노이즈를 추가해 작은 입력 변화나 잡음에 민감해지는 것을 방지 |
|**[3]  DropOut**|학습때 뉴런을 랜덤하게 비활성해 뉴런 간 상호 의존도 감소시키는 방법으로, <br> bagging과 유사한 ensemble 효과로 일반화 성능 향상|
|**[4]  DropConnect**|뉴런을 비활성화하는 대신 뉴런 간에 연결하는 가중치를 랜덤하게 제거|
|**[5]  Ensemble**|다양한 모델의 출력을 평균내어 예측하는 방법으로, <br> 모델들 오차 간 공분산(covariance)이 작을 수록 예측 성능 향상|
|**[6]  Data Augmentation**|데이터 변형으로 학습 데이터의 다양성 증가|
|**[7]  Early Stopping**|Validation loss가 감소하다 증가하면 과적합의 신호로 보고, <br> 과적합이 발생하기 전에 학습 종료 |
|**[8]  Adversal Training**||
|**[9]  Multi-task Learning**||
|**[10]&nbsp;Layerwise&nbsp;Pretraining&nbsp;and&nbsp;Initialization&nbsp;**||
|**[11] Architectural Regularization**||
|**[12] Label Smoothing**||
|**[13] Batch Normalization**||