

>
> 캐글 대회 참여 1일차
> https://www.kaggle.com/competitions/AI4Code
>

## 과제 내용
https://www.kaggle.com/competitions/AI4Code

python notebook 안에 있는 code 와 comment 사이 관계를 이해하는 것이 목표이다.

markdown cell들을 재구성하는 것이 목표이다

어떤 natural language가 어떤 코드를 가르키는지 알아보는 것이다.


python 노트북은 독보적인 특징을 갖는다. 커멘트를 달고 그 커멘트와 관련하여 작성자의 의도가 담긴 코드가 작성되기 때문이다

코드와 마크다운 사이의 관계를 이해한다면 그것은 AI 기반의 것들에 많은 도움이 될 것이다.

예를 들어
  - 더 효율적인 데이터 필터링 혹은 전처리 파이프라인
  - notebook의 가독성에 대한 자동 평가 기능

  캐글로부터 160000 개의 python notebook을 데이터로 제공한다.

## 평가 방법
Kendall tau correlation 을 통해 예측된다.

8월 4일까지

### 제한 조건
9시간 보다 낮아야 한다.
submission.csv


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


### gradient boost
https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-15-Gradient-Boost


m1~m3 모델이 있을 때, m1에는 x에서 샘플링된 데이터를 넣는다
그리고 나온 결과 중에서 예측이 잘못된 x 중의 값들에 가중치를 반영해서 다음 모델인 m2에 넣는다 과정을 m3에도 반복한다

y = m1(x) + error(x)

boosting 중 gradient boost 가 대표적인데, 이 알고리즘을 병렬 학습이 지원도도록
구현한 라이브러리가 XGBoost 이다.



https://www.kaggle.com/code/aerdem4/ai4code-pytorch-distilbert-baseline
AI4Code Pytorch DistilBert Baseline
