## KNN
주변 가장 가까이 있는 k개를 확인 한 후 어떤 클래스가 더 많은지 확인한다

Test 과정이 복잡한 알고리즘으로 `Lazy Algorithm`이라고도 한다.

k가 너무 작을 경우 overfit 되고, k가 너무 클 경우 mojority 클래스로만 예측한다.

변수 : K

## Decision Tree
  DecisionTreeClassifier
    max_depth

weighted gini score을 사용한다


feature_names
feature_importances_ : feature들이 얼마나 loss에 기여하는지 수치로 나타낸다

### Regression
  MSE 사용


## RandomForest
Bagging을 사용한 방법이다.

min_samples_split 때문에 max_depth를 주지 않아도 100%가 되지 않는다

min_sample_split : split하기 위해 필요한 sample들의 수이다

decision tree는 하나의 feature의 중요도가 클 수 있따

random forest는 여러개의 estimator을 이용하기 때문에
feature들의 중요도롤 나누어 할당해줄 수 있다

boosting을 사용할 경우 sequential이기 때문에 feature imporances_의 비율은 유지된다 반면 bagging을 사용할 경우 feature importances_에 새로운 값이 나온다
