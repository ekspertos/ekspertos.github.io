---
layout: post
title:  "Data Regularization"
subtitle: "Regularization"
categories: projects
tags: regularization DeepLearning MachineLearning
comments: true
---

 
> 딥러닝 모델은 학습 데이터를 통해 입력과 출력 사이의 패턴을 학습하며, 이를 기반으로 새로운 입력에 대해 적절한 출력을 예측하는 방식으로 동작한다.
> 모델이 다양한 상황에서 잘 동작하도록 일반화(generalization)되기 위해서는 다양한 경우를 포함하는 충분히 풍부한 데이터가 필요하다.
> 하지만 실제로는 모든 상황을 충분히 포함한 데이터를 수집하기 어렵기 때문에, 제한된 데이터만으로 학습이 이루어지는 경우가 많다.
> 이러한 데이터 부족으로 인한 과적합을 완화하기 위해 모델의 복잡도를 조절하거나 학습을 안정화하는 다양한 정규화(regularization) 기법들이 사용된다.
> 이번 포스트에서는 최근 모델에서 일반적으로 사용되는 다양한 정규화 기법들을 자세히 살펴본다.

딥러닝 모델은 학습 데이터를 통해 입력과 출력 사이의 패턴을 학습하며, 이를 기반으로 새로운 입력에 대해 적절한 출력을 예측하는 방식으로 동작한다.
이러한 예측이 가능하도록 모델을 학습시키는 과정이 필요하다.
이 과정에서 모델은 입력 데이터로부터 유용한 표현을 학습하고, 이를 바탕으로 주어진 입력에 대해 적절한 출력을 생성하도록 최적화된다.
만약 데이터가 특정 상황에만 치우쳐 있다면, 모델 역시 그 제한된 패턴에 맞춰 학습되어 새로운 상황에 잘 대응하지 못할 수 있다.
모델이 다양한 상황에서 잘 동작하도록 일반화(generalization)되기 위해서는 다양한 경우를 포함하는 충분히 풍부한 데이터가 필요하다.
하지만 실제로는 모든 상황을 충분히 포함한 데이터를 수집하기 어렵기 때문에, 제한된 데이터만으로 학습이 이루어지는 경우가 많다.
이러한 데이터 부족으로 인한 과적합을 완화하기 위해 모델의 복잡도를 조절하거나 학습을 안정화하는 다양한 정규화(regularization) 기법들이 사용된다.
이번 포스트에서는 `A survey of regularization strategies for deep models(2019)` 논문에서 소개된 다양한 정규화 기법들을 자세히 살펴본다.


 

|기법|설명|
|-|--------------|
|**1. [Weight Decay](#AAA)**|aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa a a a a aa a a a a b b b b b b |
|8*2. Adding Noise|bbbbbbbbbbbbb|
|3. DropOut|ccccccccccccc|
|4. DropConnect|dddddddddddd|
|5. Ensemble|eeeeeeeeeee|
|6. Data Augmentation|ffffffffffffff|
|7. Early Stopping|ggggggggggg|
|8. Adversal Training|hhhhhhhhhhhh|
|9. Multi-task Learning|iiiiiiiiiiiiii|
|10.&nbsp;Layerwise&nbsp;Pretraining&nbsp;and&nbsp;Initialization|jjjjjjjjj|
|11. Architectural Regularization|kkkkkkkkk|
|12. Label Smoothing|llllllllll|
|13. Batch Normalization|mmmmmmmmmmm|