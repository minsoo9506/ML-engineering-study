# 4. 모델 학습 디자인 패턴

## 4.1 일반적인 training loop

## 4.2 디자인 패턴 11: useful overfitting

- 어떠한 시스템의 해를 근사하는 ML모델과 같이 관찰되지 않은 변수(데이터)도 없고 노이즈도 없으며 통계적 변동성도 없는 경우가 있다. 이런 경우 overfitting이 되도록 한다.

## 4.3 디자인 패턴 12: checkpoint

- 모델을 주기적으로 저장

### 4.3.4 trade-off와 대안

- early stopping
- checkpoint 선택: val loss가 증가하는 순간의 모델보다 좀 더 에폭이 지난 모델이 더 좋은 경우도 있다. train, valid dataset이 불완전하게 뽑힐 수도 있기 때문이다.
- 정규화: 꼭 early stopping이나 checkpoint를 사용하지 않더라도 L2정규화 같은 방법으로도 val loss가 증가하지 않고 잘 수렴하도록 할 수 있다.
- fine tuning

## 4.4 디자인 패턴 13: transfer learning

## 4.5 디자인 패턴 14: distribution strategy

- 캐싱, 하드웨어 가속, 병렬화 등의 방법으로 training loop를 여러 worker에 걸쳐 확장시키는 디자인 패턴

### 4.5.2 솔루션

- data parallelism, model parallelism 이 있다.
- 동기식 학습: worker들이 서로 다른 input 데이터의 일부를 병렬로 학습하고 각 학습 단계가 끝날 때 기울기값을 집계한다.
- 비동기식 학습: 각 worker들이 서로 다른 input 데이터를 독립적으로 학습하고 모델 가중치 및 파라미터는 일반적으로 파라미터 서버 아키텍처를 통해 비동기식으로 업데이트된다.

### 4.5.4 trade-off와 대안

- model parallelism: 모델이 너무 커서 여러 장치로 분할해서 학습한다.
- 적절한 배치 사이즈 선택
- I/O 대기 최소화

## 4.6 디자인 패턴 15: hyperparameter tuning

### 4.6.2 솔루션

- 수동 조정
- grid search
- bayesian optimization: 위 두 개보다 더 빠를 것이다.
- 매번 동일하게 hp tuning을 하는 것보다 이전에 실험을 통해 얻은 결과를 잘 저장해서 다음 학습시에는 이를 참고하는 것도 좋은 방법이다. 범위가 줄어들 수 있을 것이다.
