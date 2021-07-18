---
layout: post
title:  "Transformer-Encoder"
subtitle: "Transformer(3)"
categories: projects
tags: blog github pages jekyll spacy transformer
comments: true
---

## 서론
> [다음 글](https://github.com/bentrevett/pytorch-seq2seq/blob/master/6%20-%20Attention%20is%20All%20You%20Need.ipynb)을 참고해 작성했습니다.
>`Preparing The Data`에서는 데이터 셋의 토큰화 및 정수 인코딩 주었습니다.
> 그 다음 과정인 트랜스포머`의 ``인코더``를 구현해 볼 것입니다.

전체적인 그림은 다음과 같습니다.
![이미지](https://ekspertos.github.io/assets/review/2021-07-16-encoder-input.PNG, "encoder_input")


- 목차
  -
  -
  -


## Input Enbedding
입력이 주어지면 처음 거치는 과정이 임베딩 과정입니다.
Transformer에서는 두가지 임베딩 과정을 진행합니다. `standard embedding`과 `positional embedding`입니다.

- __Standard Embedding__
  임베딩 층의 입력으로 정수 인코딩된 단어를 사용합니다.
  입력 정수를 밀집 백터(dense vector)로 매핑 해주는 것이 임베딩의 과정입니다.
  우리는 이 단계에 PyTorch에서 제공하는 임베딩 함수를 사용하겠습니다.

  ![이미지](https://ekspertos.github.io/assets/review/2021-07-16-embedding-table.PNG, "embedding-table")

  임베딩 함수를 사용하게 되면 그림에서 보시다시피 ``임베딩 테이블``이 생성 됩니다.
  우리 예제에서는 단어가 3개를 6차원 밀집백터로 변환하고자 하며
  각각의 밀집 백터가 다른 값을 가지고 있음을 알 수 있습니다.

  이 가중치들은 학습과정에서 모델이 풀고자하는 작업에 맞는 값으로 계속해서 업데이트 됩니다.
  이 때 코사인 유사도, PMI, PPMI, SVD가 사용되겠지요.
  다음으로 우리가 실습에서 사용할 임베딩 코드입니다.

  ```python
  self.tok_embedding = nn.Embedding(input_dim, hid_dim)
  # batch_size:  128
  # Unique tokens in source (de) vocabulary: 7853
  # hid_dim: 512
  ```
  Standard 임베딩 계층에서 [128x7853x52] 임베딩 테이블이 생긴다는 것을 알 수 있습니다.

- __Positional Embedding__
  Transformer에서는 토큰에 대한 처리가 동시에 일어납니다.
  이에 따라 처리과정에서 토큰의 위치정보를 사용할 수 없지요.
  이를 해결하고자 Transformer에서 사용하는 것이 Positional Embedding Layer입니다.

  Standard Layer에서의 과정과 똑같지만 입력으로 토큰이 들어가는 것이 아니라 토큰의 위치정보가 들어갑니다.
  위치정보는 <sos> 토큰을 0으로 하여 1씩 증가시키며 얻을 수 있습니다.

  ```python
  self.pos_embedding = nn.Embedding(max_length, hid_dim)
  ```

  원조 논문인 `Attention is All You Need`에서는 `static embedding`을 사용했지만
  저희는 Bert와 같은 최근 모델에서 사용되는 Positional Embedding을 사용했습니다.

  static embedding에 대한 자세한 설명은 [이곳](http://nlp.seas.harvard.edu/2018/04/03/attention.html#positional-encoding)을 참고하세요.

  - __Combined__
  우리가 학습에 사용할 백터는 토큰과 그 위치정보가 모두 포함되어 있어야 합니다.
  두가지 임베딩 과정에서 구해진 백터를 합쳐주면 구할 수 있겠지요.

  여기서 중요한 점은 우리는 구해진 백터에 scaling factor을 곱해줍니다.
  $$ scaling factor: sqrt(d_{model}) $$
  `d_model`은 밀집백터의 차원 수와 같습니다.
  왜 이것을 사용할까요? 그 이유는 임베딩 과정에서의 분산 값을 낮출 수 있다는 것입니다.
  분산을 줄이므로서 신뢰성 있는 학습을 할 수 있는 것이지요.


## Dropout & Normalization
