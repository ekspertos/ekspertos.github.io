
## 서론
> Self-Attention을 배우기에 앞서 Attention 기법을 소개드립니다
> `밑바닥부터 시작하는 딥러닝2`를 참고 했습니다.


---

## Attention
  LSTM 계층의 인코더에서 출력하는 은닉 상태 __h__는 가장 최근에 입력된 값에 영향을 가장 많이 받습니다.

  인코더의 입력으로 '조유리는 아이즈원의 멤버`라는 문장이 입력되면
  디코더로 보내지는 은닉 상태는 ``멤버``단어의 영향을 가장 많이 받았다고 할 수 있습니다.

  디코더에서 마지막에 출력되는 은닉 상태 뿐만 아니라, ``인코더에서 생성되는 은닉상태들을 모두 사용``한다는 개념이 `Attention mechanism`입니다.

  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-21-attention-mechanism.PNG)
  디코더의 단어들이 입력된 은닉상태들 중 어느 곳에 집중할지 정할 수 있습니다.
  그림의 가중치 값들은 학습을 통해 모델이 원하는 값으로 업데이트 됩니다.

---

## Self-Attention
  ![이미지](https://ekspertos.github.io/assets/img/review/2021-07-21-self-attention.PNG)
  `self-attention`은 입력과 출력 사이의 attention값을 구하는 것이 아닌
  `하나의 문장 내에서 각 원소가 다른 원소들과 어떻게 관련되는지` 살펴볼 수 있습니다.
