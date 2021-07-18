---
layout: post
title:  "Transformer-Preparing-Data"
subtitle: "Transformer(2)"
categories: projects
tags: blog github pages jekyll spacy
comments: true
---

## 서론
> [다음 글](https://github.com/bentrevett/pytorch-seq2seq/blob/master/1%20-%20Sequence%20to%20Sequence%20Learning%20with%20Neural%20Networks.ipynb)을 참고 했습니다.
> 트랜스포머를 구현해보기에 앞서 우리가 사용할 데이터들을 준비해 주어야 합니다.
> 학습과정은 `TorchText`의 Multi30k 데이터셋를 이용 할 것이며 중간 토큰화 과정에 `spaCy`를 사용할 것입니다.

- 목차
  - [Import Module](#import-module)
  - [Tokenizer](tokenizer)
  - [Multi30k DataSet](#multi30k-dataset)
  - [using GPU](#using-gpu)
  - [Vocabulary](#vocabulary)
  - [Iterator](#iterator)

## __Import Module__
사용할 모듈들을 임포트하고 랜덤 값을 설정 해주는 코드입니다.

  ```python
  import torch
  import torch.nn as nn
  import torch.optim as optim

  from torchtext.legacy.datasets import Multi30k
  from torchtext.legacy.data import Field, BucketIterator

  import spacy
  import numpy as np

  import random
  import math
  import time

  SEED = 1234

  random.seed(SEED)
  np.random.seed(SEED)
  torch.manual_seed(SEED)
  torch.cuda.manual_seed(SEED)
  torch.backends.cudnn.deterministic = True
  ```

---

## Tokenizer
* __토크나이저 패키지 설치__
spaCy는 뉴스 기사 및 웹 자료들을 이용해 미리 학습된 패키지를 제공합니다.
태깅, 파싱, 표제어 추출(Lemmatization)을 할 수 있도록 미리 학습된 패키지 입니다.

  spacy의 영어와 독일어 패키지를 이용해서 실습을 진행하겠습니다.
두 패키지를 다운 받는 코드입니다.

  ```python
  import sys
  !{sys.executable} -m spacy download de_core_news_sm
  !{sys.executable} -m spacy download en_core_web_sm
  spacy_de = spacy.load('de_core_news_sm')
  spacy_en = spacy.load('en_core_web_sm')
  ```

* __토크나이저 설정__
  문장을 단어별로 토큰화해서 list로 만들어 반환해주는 함수입니다.
  ```python
  def tokenize_de(text):
      """
      Tokenizes German text from a string into a list of strings
      """
      return [tok.text for tok in spacy_de.tokenizer(text)]

      def tokenize_en(text):
        """
        Tokenizes English text from a string into a list of strings
        """
        return [tok.text for tok in spacy_en.tokenizer(text)]
  ```

  데이터가 어떤 처리를 거쳐야하는지 알려주는 torchtext의 Field들을 만들어 주는 코드입니다.
  ```python
  SRC = Field(tokenize = tokenize_de,
           init_token = '<sos>',
           eos_token = '<eos>',
           lower = True)

  TRG = Field(tokenize = tokenize_en,
           init_token = '<sos>',
           eos_token = '<eos>',
           lower = True)
  ```
  Field의 파라미터로는 다음과 같으며 더 자세한 파라미터들은 [여기](https://torchtext.readthedocs.io/en/latest/data.html)를 참고하세요.
    - tokenize : 사용할 토큰나이저 정의
    - init_token : 문장의 시작에 '<sos>' 토큰 삽입
    - eos_token : 문장의 끝에 '<eos>' 토큰 삽입
    - lower : 텍스트를 모두 소문자로 변환해 토큰화 시행
---

## Multi30k DataSet
  사용할 데이터셋을 다운로드 해주는 함수입니다.
  ```python
  train_data, valid_data, test_data = Multi30k.splits(exts = ('.de', '.en'),
                                                    fields = (SRC, TRG))
  ```

  우리가 사용할 데이터셋 torchtext에서 제공하는 `Multi30k dataset` 입니다.
  Multi30k 데이터셋에는 ``각 언어별로 30000개의 문장``이 있고
  각 문장은 12 단어 이하로 구성됩니다.

  Multi30K 데이터셋을 받아오는 함수의 파라미터는 다음과 같습니다.
    - exts : 어떤 언어를 source와 target으로 사용할 지 정의
    - field : 소스와 타깃을 토콘화하는데 사용할 필드 정보


  ```python
  print(f"Number of training examples: {len(train_data.examples)}")
  print(f"Number of validation examples: {len(valid_data.examples)}")
  print(f"Number of testing examples: {len(test_data.examples)}"
  print(vars(train_data.examples[0]))
  ```
  >Number of training examples: 29000
  >Number of validation examples: 1014
  >Number of testing examples: 1000
  >{'src': ['.', 'büsche', 'vieler', 'nähe', 'der', 'in', 'freien', 'im', 'sind', 'männer', 'weiße', 'junge', 'zwei'],
  > 'trg': ['two', 'young', ',', 'white', 'males', 'are', 'outside', 'near', 'many', 'bushes', '.']}  

  Train 문장 29000개와 Test 문장 1000개, 총 30000개로 구성되고
  train_data에 독일어와 영어가 토큰화되어 리스트로 저장되 있는 것을 확인할 수 있습니다.

---
## Using GPU
  ```python
  device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
  ```

---

## Vocabulary
  각각의 토큰별로 Index(정수형)에 매칭 되는 `vocabulary`를 만들어 줄 것입니다.
  ```python
  SRC.build_vocab(train_data, min_freq = 2)
  TRG.build_vocab(train_data, min_freq = 2)
  ```
  `min_freq` 파라미터를 통해서 train_data에서 2번 이상 등장하는 단어들만 허용할 것입니다.
  1번 이하로 등장하는 단어들은 ``<unk> 토큰으로 변환``되어 표현됩니다.

  ```python
  print(f"Unique tokens in source (de) vocabulary: {len(SRC.vocab)}")
  print(f"Unique tokens in target (en) vocabulary: {len(TRG.vocab)}")
  ```
  > Unique tokens in source (de) vocabulary: 7853
  > Unique tokens in target (en) vocabulary: 5893

  더많은 vocabulary 정보
  [https://github.com/paul-hyun/torch-nlp-tutorial/blob/main/02-inputs-outputs/02-02-tokenizer-vocabulary.ipynb]

  ---

## Iterator
  배치의 크기 별로 학습을 진행하기 위해 정의합니다.
  `vocaulary`를 통해 index로 변환된 상태로 iterator에 저장됩니다.

  iterator을 사용할 때 유의할 점이 있습니다.
  바로 ``source와 target의 문장이 같은 길이로 패딩(padded)되어 있어야 한다``는 것입니다.
  __tochText iterator__에서 이 역할 또한 수행합니다.

  ```python
  BATCH_SIZE = 128
  train_iterator, valid_iterator, test_iterator = BucketIterator.splits(
    (train_data, valid_data, test_data),
    batch_size = BATCH_SIZE,
    device = device)
  ```
  일반 iterator보다 `BucketIterator`의 좋은 점은 src와 trg을 최소의 패딩을 이용한다는 것입니다.
