---
layout: post
title:  "WER with Levenstein Distance"
subtitle: "WER"
categories: projects Jekyll
tags: blog github pages jekyll spacy
use_math: true
comments: true
---
## 서론
> ASR 음성인식의 정확성을 검증하는데 사용하는 Word-Error-Rate(WER)에 대해 알아보겠습니다.
> WER은 `Levenshtein Distance`을 사용해 두 문장 간의 Distance를 계산합니다.
>


## WER이란?
WER은 `Levenshtein Distance Algorithm`을 이용해 유도합니다.
두 문장 간의 최소 편집거리를 구할 수 있습니다.

$$
WER = \frac{S + D + I}{N}
$$


- S: 문장에서 발생한 `substitution`의 개수
  다른 단어로 인식된 경우
- D: 문장에서 발생한 `deletion`의 개수
  단어가 인식되지 않을 경우
- I: 문장에서 발생한 `insertion`의 개수
  존재하지 않는 단어가 인식된 경우
- N: `reference` 단어의 개수

우리가 원하는 결과 `reference`와 인식된 결과 `hypothesis`, 두 문장을 이용해 편집거리를 구합니다

## Levenshtein Distance
Substitution, Deletion, Insertion을 `Levenshtein Distance` 알고리즘을 이용해 구합니다.
그 과정은 다음과 같습니다.


1. hypothesis를 행으로, reference를 열로 둔 행렬을 만들어줍니다.
  reference를 `GUMBO`로 hypothesis를 `GAMBOL`로 두었습니다.

  | ||G|U|M|B|O|
  |---|---|---|---|---|---|---|
  ||---|---|---|---|---|---|
  |__G__|---|---|---|---|---|---|
  |**A**|---|---|---|---|---|---|
  |**M**|---|---|---|---|---|---|
  |**B**|---|---|---|---|---|---|
  |**O**|---|---|---|---|---|---|
  |**L**|---|---|---|---|---|---|

2. 0으로 시작해 1씩 증가하도록 첫 행과 열을 채워줍니다

  | ||G|U|M|B|O|
  |---|---|---|---|---|---|---|
  ||0|1|2|3|4|5|
  |__G__|1|---|---|---|---|---|
  |**A**|2|---|---|---|---|---|
  |**M**|3|---|---|---|---|---|
  |**B**|4|---|---|---|---|---|
  |**O**|5|---|---|---|---|---|
  |**L**|6|---|---|---|---|

3. Substitution, Deletion, Insertion의 여부를 구해줍니다.
  - Substitution
    $$ d[i,j] = d[i-1,j-1] + cost $$

  - Deletion
    $$ d[i,j] = d[i-1,j] + 1 $$

  - Insertion
    $$ d[i,j] = d[i,j-1] + 1 $$

    cost 값은 행과 열의 값이 같을 시 `0`이고 그 외에는 `1` 입니다.

4. Substitution, Deletion, Insertion을 고려해 가장 작은 WER을 찾아줍니다.




- Substitution의 예

  | ||G|U|M|B|O|
  |---|---|---|---|---|---|---|
  ||0|1|2|3|4|5|
  |__G__|1|0|---|---|---|---|
  |**A**|2|---|1|---|---|---|
  |**M**|3|---|---|1|---|---|
  |**B**|4|---|---|---|1|---|
  |**O**|5|---|---|---|---|1|
  |**L**|6|---|---|---|---|---|

  GUMBO와 GAMBOL을 비교해 substitution이 `1`개 있음을 구할 수 있습니다

- Deletion의 예

  | ||G|U|M|B|O|
  |---|---|---|---|---|---|---|
  ||0|1|2|3|4|5|
  |__G__|1|0|1|2|3|4|

  GUMBO와 G를 비교해 deletion이 `4`개 있음을 알 수 있습니다

- Insertion의 예

  | ||G|
  |---|---|---|---|---|---|---|
  ||0|1|2|3|4|5|
  |__G__|1|0|
  |**A**|2|1|
  |**M**|3|2|

  G와 GAM을 비교해 Insertion이 `2`개 있을을 알 수 있습니다


우리는 Substitution, Deletion, Insertion을 고려해 에러가 가장 작은 경우를 구해줍니다.
$$ d[i,j] = min(d[i-1,j-1] + cost, d[i-1,j] + 1, d[i,j-1] + 1) $$

| ||G|U|M|B|O|
|---|---|---|---|---|---|---|
||0|1|2|3|4|5|
|__G__|1|0|1|2|3|4|
|**A**|2|1|1|2|3|4|
|**M**|3|2|2|1|2|3|
|**B**|4|3|3|2|1|2|
|**O**|5|4|4|3|2|1|
|**L**|6|5|5|4|3|2|

Levenshtein Distance 알고리즘을 통해 GUMBO와 GAMBOL 간의 `편집거리가 2`임을 구할 수 있습니다.

이경우 error rate는 distance를 reference의 길이로 나누어줘 `2 / 5 = 0.4` 임을 알 수 있습니다.

## 코드 구현

> 위에서는 char 단위로 distance를 계산했지만 WER을 구할 때는
> `word 단위로 split`하여 Levenshtein Distance를 구해주면 됩니다.
> 스크랩된 코드이며 [원본 코드](https://www.programcreek.com/python/?code=undertheseanlp%2Fautomatic_speech_recognition%2Fautomatic_speech_recognition-master%2Fegs%2Fvivos%2Fextension%2Fmetrics.py)를 참고하시기 바랍니다

```python
def calculate_wer(reference, hypothesis):
    """
        Calculation of WER with Levenshtein distance.
        Works only for iterables up to 254 elements (uint8).
        O(nm) time and space complexity.

        >>> calculate_wer("who is there".split(), "is there".split())
        1
        >>> calculate_wer("who is there".split(), "".split())
        3
        >>> calculate_wer("".split(), "who is there".split())
        3
    """
    # initialisation
    import numpy
    d = numpy.zeros((len(reference) + 1) * (len(hypothesis) + 1),
                    dtype=numpy.uint8)
    d = d.reshape((len(reference) + 1, len(hypothesis) + 1))
    for i in range(len(reference) + 1):
        for j in range(len(hypothesis) + 1):
            if i == 0:
                d[0][j] = j
            elif j == 0:
                d[i][0] = i

    # computation
    for i in range(1, len(reference) + 1):
        for j in range(1, len(hypothesis) + 1):
            if reference[i - 1] == hypothesis[j - 1]:
                d[i][j] = d[i - 1][j - 1]
            else:
                substitution = d[i - 1][j - 1] + 1
                insertion = d[i][j - 1] + 1
                deletion = d[i - 1][j] + 1
                d[i][j] = min(substitution, insertion, deletion)

    return d[len(reference)][len(hypothesis)] / float(len(reference))
  ```








[참고]
1. WER 소개 [https://init.cise.ufl.edu/2018/04/03/word-error-rate-wer-for-recognition-of-natural-interactions/]
2. Levenshtein Distance 소개 [https://people.cs.pitt.edu/~kirk/cs1501/Pruhs/Spring2006/assignments/editdistance/Levenshtein%20Distance.htm]
