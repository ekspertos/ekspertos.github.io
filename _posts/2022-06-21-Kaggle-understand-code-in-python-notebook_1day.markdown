---
layout: post
title:  "AI4Code 대회 1일차"
subtitle: "Kaggle"
categories: projects
tags: blog github pages jekyll spacy
comments: true
---

>
> Google과 Alphabet이 Kaggle에서 주최한 자연어처리 관련 대회를 진행한다
>
> https://www.kaggle.com/competitions/AI4Code
>

- 목차
  - [대회설명](#대회설명)
  -

## 대회설명

대회의 주제는 `주피터 노트북 셀 재정렬 알고리즘 설계` 이다.
먼저 주피터 노트북이 무엇인지 알아보자.

```
주피터 노트북은 웹브라우저 상에서 파이썬 코드를 단계적으로 쉽게 실행하고, 시각적으로 빠르게 확인 수 있도록 해주는 프로그램이다. 이런 방식은 탐색적 데이터 분석에 아주 적합하여 많은 데이터 분석가가 주피터 노트북을 사용하고 있다

- https://sdc-james.gitbook.io/onebook/2.-1/1.6./1.1.1.-jupyter-notebook
```

주피터 노트북은 개발자가 코드를 실행하기 위해 적은 `코드 셀`과 그 코드들을 설명하기 위해 적은 `마크다운 셀`로 구성된다.

보통 마크다운 셀들은 코드에 대해 잘 설명할 수 있도록 코드 셀 중간 중간에 삽입된다.

AI4Code는 ``앞서 설명한 노트북 셀들의 정렬 알고리즘을 설계``하는 대회이다.
모델 훈련을 위해서 대회에서는 무작위로 배치된 노트북 셀들을 제공한다.

다음과 같이 무작위로 배열된 셀들이 주어진다.
```
<code_1> -> <code_2> -> <code_3> -> <markdown_1> -> <markdown_2>
```
우리는 이러한 셀들을 의미에 맞게 정렬을 해주어야 한다.
정렬된 한 가지 예시는 아래와 같다.
```
<code_1> -> <markdown_2> -> <code_2> -> <code_3> -> markdown_1
```
마크다운 셀이 나오는 순서는 바뀔 수 있는 반면, 코드 셀이 나오는 순서는 바뀔 수 없다

마크다운 셀은 앞뒤 코드 셀들을 설명하는 기능을 한다.
우리가 코드와 마크다운 사이의 관계를 이해할 수 있다면
노트북을 환경에서 개발되는 많은 프로젝트들에 도움이 될 것이다.

- 효율적인 데이터 필터링 혹은 전처리 파이프라인 설계
- 노트북의 가독성에 대한 자동 평가 기능

캐글로부터 160000 개의 python notebook을 데이터로 제공한다.

## 제공 데이터
대회를 위해 총 3종류의 데이터가 제공된다.

__1. train/test dataset__
json 파일 형태로 제공되며 파일의 구성은 다움과 같다
```json
"code" {
  "cell_type" : {
    "<id_1>" : "code",
    "<id_2>" : "code",
    "<id_3>" : "markdown"
  },
  "source" : {
    "<id_1>" : "<raw code>",
    "<id_2>" : "<raw code>",
    "<id_3>" : "<raw markdown>"
  }
}
```

__2. train_orders.csv__
 노트북 내 셀들의 정확한 순서를 제공한다

 ||cell order|
 |---|---|
 |notebook1|[ id_3 id_1 id_2 ] |
 |notebook2| [ id_1 id_2 id_3 ] |
 |notebook3| [ id_1 id_3 id_2 id_4 ] |


__3. train_ancestors.csv__
 노트북과 관련된 다른 노트북들을 제공한다
 캐글에서 `fork`된 기록들을 통해 구성된 데이터이다.

 ||ancestor|parent|
 |---|---|---|
 |notebook1|notebook_a||
 |notebook2|notebook_b|notebook_d|
 |notebook3|notebook_c||



## 평가 방법
알고리즘의 평가 방법으로 Kendall tau correlation 을 사용한다. `Kendall tau correlation은 일반적인 correlation와 RANK를 결합한 방식`이다.

두 가지 배열의 상관관계를 측정하는 대표적인 방법들은 다음과 같다.

- Pearson's correlation
- Spearman's rank correlation
- Kendall's tau rank corrlation


### Kendall's tau correlation

degree of concordance between two columns of ranked data.

concordance : 용어 색인

The greater number of inversions the smaller the coefficient will be

tau-a vs tau-b

$$
Kendall's tau = \frac{C-D}{C+D}
$$

Concordant pair

|Master|Sudent|C|D|
|---|---|---|---|
|1|2|10|1|
|2|1|10|0|
|3|4|8|1|
|4|3|8|0|
|5|6|6|1|
|6|5|6|0|
|7|8|4|1|
|8|7|4|0|
|9|10|2|1|
|10|9|2|0|
|11|12|0|1|
|12|11|||


Master가 평가한 것과 Student 가 평가한 것을 보고 싶다.

Concordant pair
7보다 밑에 있으면서 7보다 작은 경우

Discordant pair
그 반대

Kandall's tau = 60-6/60+6
  = 0.818

master과 student 사이의 correspondance를 볼 수 있다

$$
Z = \frac{3 * tau * \sqrt{n(n-1)}}{\sqrt{2(2n+5)}}
$$

Z =

1.96보다 클 경우 statistically significant 하다

|Master|Sudent2|C|D|
|---|---|---|---|
|1|12|0|11|
|2|2|9|1|
|3|3|8|1|
|4|4|7|1|
|5|5|6|1|
|6|6|5|1|
|7|7|4|1|
|8|8|3|1|
|9|9|2|1|
|10|10|1|1|
|11|11|0|1|
|12|1|||

그렇다면
Kendal's tau = 45-21/45+21
= 0.354 -> not statistically significant

## Pearsons correlation coefficient
common measure of correlation

cardinal correlation

coefficient of determination
https://www.youtube.com/watch?v=lng4ZgConCM

how muct of the variation in y is described by the variation in x

total variation in y : E(y-avg_y)^2

즉, 선과 관련없이 x에 따른 출력으로 나오는 y 분포의 분산

variation in X

not describe by variation in x

how much of the total variation is not described by the regression line

what % of total variation is not described by the variation in regression line?

선과 관련이없는, variation line으로 표시 되지 않는 정도


[단점]
- linear dependence/assosiation 만을 측정한다
  - non linear dependence 를 측정하지 못한다

- independent 한 경우 correlation = 0
  그러나 crrelation = 0 이라고 해도 indenpendent이라고 할 수 없다
  non linearly dependent 할 수 있으므로/


- sensitive to outliers

## Spearman's rho ?

Spearman's rank correlation.
- not sensitive to outliers



### 제한 조건
9시간 이하의 CPU와 GPU 런타임


### Getting Started with AI4Code (예시1)
https://www.kaggle.com/code/ryanholbrook/getting-started-with-ai4code

- 대회 데이터에서 학습 데이터와 검증 데이터 분리
-
- XGBoost 기반 모델 구축
- 대회에서 제공한 성능 평가 방법을 통한 성능 예측
-

우리의 모델은 셀이 노트북의 어느 위치에 배정되어야 하는지 포함하는 내용을 보고 예측한다. 예를 들면, "introduction" 혹은 "import"가 포함된 셀은 주로 노트북의 시작 부분에 위치한다. 반면 "Submit" 혹은 "Submission.csv"가 포함된 셀은 노트북의 마지막 부분에 위치한다.

이러한 간단한 특징들이 데이터 사이언스의 전체적인 순서도를 재구성(reconstruct) 할 수 있다.  

그러나 `상호작용` 혹은 ``셀 간의 상관관계``를 이해하는 것은 더 적절한 솔루션을 필요로 한다.

최근 신경망 언어모델들을 탐구하는 과정이 되었으면 좋겠다.


```python
import json
from pathlib import path

import numpy as np
import pandas as pd
from scipy import sparse  #
from tqdm import tqdm     # 학습 모니터링 관련 모둘

pd.options.display.width = 180
pd.options.display.max_colwidth = 120

data_dir = Path("../input/AI4Code")
```

각각의 노트북은 json 파일로 저장된다. jupyter notebook들에서 표현되는
흔치 않은 메타 데이터들은 필터링되어 제공된다.

`cell_type` 과 `source` 만 제공된다.

[데이터](https://www.kaggle.com/competitions/AI4Code/data) 페이지 에서 대회와 관련된 데이터의 전체적인 설명을 볼 수 있다

path.stem

### pathlib
glob 패털을 사용하여 파일/폴더를 얻을 수 있다
files = path.glob('*.json') # path 안에 파일/폴더 중 .json 파일  

Path 의 다른 속성들
- .name : 디렉토리가 제외된 파일 이름
- .parent: 파일이 있는 디렉토리 또는 path가 디렉토리인 경우는 상위 디렉토리
- .stem : 접미사가 없는 파일 이름
- .suffix : 파일 확장자
- .anchor : 디렉토리 가장 앞의 경로 부분

### read_json


### 데이터 형식
```json
"root" {
  "cell_type" : {
    "<id_1>" : "code",
    "<id_2>" : "code",
    "<id_3>" : "markdown"
  },
  "source" : {
    "<id_1>" : "<raw code>",
    "<id_2>" : "<raw code>",
    "<id_3>" : "<raw markdown>"
  }
}
```

```python
pd.read_json(
            path,
            dtype={'cell_type': 'category', 'source': 'str'})
        .assign(id=path.stem)
        .rename_axis('cell_id')
```

code 셀은 정확한 순서로 정렬되어 있다.
반면 markdown 셀은 섞여 있다

training, categorical, read data 순서로 저장되어 있다?

예를 들어 뒤섞인 노트북이 아래와 같다면:

```
code_1
code_2
code_3
markdown_1
markdown_2
```
정확하게 배열된 노트북은 다음과 같을 수 있다:
```
code_1
markdown_2
code_2
code_3
markdown_1
```

### Ordering the Cells

train_orders.csv 파일에 정확한 순서가 제공된다.

```python
pd.read_csv(
  data_dir / 'train_orders.csv',
  index_col='id',
  squeeze=True
).str.split()

```


### assert_frame_equal
두 개의 DataFrame을 비교하여 차이점을 출력하기 위한 것입니다.


각각의 데이터 프레임에 대한 rank들을 만들어주자
df_orders.to_frame().join(
  df.reset_index('cell_id').groupby('id')['cell_id'].apply(list),
  how='right',
  )

데이터 프레임에 cell_order 추가


## Split
df_ancestors.csv 파일은 비슷한 origin에서 만들어진 노트북들을 보여준다
notebooks belonging to the same forking tree

parent 와 ancestor을 보여준다

ancestor id를 grouping factor로 사용

## splitter = GroupShuffleSplit
https://davinci-ai.tistory.com/18
클래스의 치우침을 방지하고 랜덤 분할 하는 방식

![이미지](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3NE1E%2FbtqBuQIK7e7%2FqVSJFqIKFOe01hJndo7dQ0%2Fimg.png)

k fold를 할 때 같은 조상을 가진 데이터들은 같은 위치에 배정 받는다

## TF-IDF (Term Frequency Inverse Document Frequency)

단어의 빈도와 역 문서 빈도를 사용하여 DTM(Document Term Matrix) 내의 각 단어들마다 중요한 정도를 가중치로 만들어주는 방법

d: 문서
t: 단어
n: 문서의 총 수

tf(d,t) : 특정 문서 d 에서 t의 등장 횟수
df(t) : 특정 단어 t 가 등장한 문서 수

$$
idf(t) = log{\frac{1+n}{1+df(t)}} + 1
$$

```python
from sklearn.feature_extraction.text import TFidfVectorization
# https://scikit-learn.org/stable/modules/feature_extraction.html#text-feature-extraction
# tf(t,d) x idf(t)
```


단어의 빈도와 역 문서 빈도를 사용하여 DTM 내의 각 단어들마다 중요한 정도를 가중치로 주는 방법

모든 문서에서 자주 등장하는 단어는 중요도가 낮다고 판단하며,
특정 문서에서만 자주 등장하는 단어는 중요도가 높다고 판단한다.
TF-IDF 값이 낮으면 중요도가 낮은 것이며, TF-IDF 값이 크면 중요도가 큰 것이다.

log를 씌워주어 희귀 가중치에 대한 가중치를 낮추어 준다

code에서의 TF-IDF와

markdown에서의 TF-IDF는 다르지 않을까

### sparse.hstack
sparse : 값이 대부분 0인 희소 행렬

```python
X_train = sparse.hstack((
  X_train,
  np.where(
    df_train['cell_type'] == 'code',
    df_train.groupny(['id', 'cell_type']).cumcount().to_numpy() + 1,
    0,
    ).reshape(-1,1)
  ))
```
https://swalloow.tistory.com/97
sparse.hstack

TfidfVectorizer를 사용하여 변환된 sparse matrix에 unigram을 이어서 붙이고 싶을 때 사용


## xgboost XGBRanker
XGBoost 란 Gradient Boosting 알고리즘을 분산환경에서도 실행할 수 있도록 구현해 놓은
라이브러리이다. Regression, Classification 모두 지원하며
성능과 자원 효율이 좋아서 인기 있게 사용되는 알고리즘이다.

XGBoost 는 여러개의 Decision Tree를 조합해서 사용하는 Ensemble 알고리즘이다

### Bagging
중복을 허용한 n개의 샘플을 반복 추출하여 평균을 구하는 작업을 m번 반복
예로 RandomForest

### Boosting
### Boosting
bagging은 독립적인 input data를 가지고 독립적으로 예측하지만
부스팅은 이전 모델이 다음 모델에 영향을 준다
Bagging과 다르게 일반적인 모델에 집중되어 있지 않고
맞추기 어려운 문제를 맞추는데 초점을 둔다

![이미지](https://ars.els-cdn.com/content/image/1-s2.0-S1566253520303195-gr1.jpg)

### adaboost
이전에 학습된 모델에서 underfit된 부분을 더 초점에 맞춘다
새로운 모델이 어려운 케이스에 대해 더 많이 집중한다
인스턴스에 대한 가중치가 모델이 학습될 수록 업데이트된다.

1. $ w_i = 1/N $ 으로 가중치 초기화
2. m = 1,2,..,M 에 대하여
  - weighted error $ err_m $ 측정
  - $ \alpha_m = log(\frac{1-err_m}{err_m})   
    - 각각의 G(x)의 기여도를 측정한다.
  - 가중치 값 변경 $w_i \leftarrow w_i exp[\apha_m \cal{I} (y^{(i)} \neq G_m(x^{(i)}))]$

3. 출력 G(x) = sign[\sum_{m=1}^M \alpha_m G_m(x)]
  - weighted majority vote

### gradient boost
https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-15-Gradient-Boost

adaboost는 stump로 구성되어 있다. 하나의 stump에서 발생한 error가 다음 stump에 영향을 준다. 이런 식으로 여러 stump가 순차적으로 연결되어 최종 결과를 도출한다.

반면 Gradient Boost 는 Stump나 tree가 아닌 하나의 leaf 부터 시작한다.
leaf는 타겟 값에 대한 초기 추정값 값을 나타낸다(보통 평균값)
AdaBoost와 동일하게 이전 tree의 error는 다음 tree에 영향을 준다
하지만 AdaBoost 와 다르게 stump가 아닌 tree로 구성되어 있다
leaf가 8개에서 32개되는 tree로 구성한다


m1~m3 모델이 있을 때, m1에는 x에서 샘플링된 데이터를 넣는다
그리고 나온 결과 중에서 예측이 잘못된 x 중의 값들에 가중치를 반영해서 다음 모델인 m2에 넣는다 과정을 m3에도 반복한다

y = m1(x) + error(x)

boosting 중 gradient boost 가 대표적인데, 이 알고리즘을 병렬 학습이 지원도도록
구현한 라이브러리가 XGBoost 이다.



https://www.kaggle.com/code/aerdem4/ai4code-pytorch-distilbert-baseline
AI4Code Pytorch DistilBert Baseline
