# 2. 데이터 표현 디자인 패턴

## 2.1 데이터 표현

### 2.1.1 numeric input

- 스케일링
  - 선형스케일링
    - min-max scaling
    - clipping
    - z score standardization
    - winsorizing
  - 비선형변환: 데이터가 치우쳐져 있거나, 종모양이 아니라면 적용하기 유용
    - scaling 전에 log 취하기
    - box-cox transformation
- 수의 배열
  - input이 수로 이루어진 배열인 경우도 존재
  - 길이가 다른 경우 적절한 방법 사용

### 2.1.2 categoric input

- 원-핫 인코딩
- 카테고리 변수를 함부로 수치화하면 안됨
- 수치 변수를 버킷화해서 카테고리 input으로 이용
- 카테고리 변수의 배열
  - 발생횟수 카운팅 등의 방법 사용
  - 길이가 다른 경우 적절한 방법 사용

## 2.2 디자인 패턴 1: feature hash

- hashed feature
  - 불완전한 데이터, cardinality, cold start 문제 해결

### 2.2.1 문제

- 예를 들어, 요일 같은 경우는 문제가 되지 않는다. 그런데 카테고리 변수가 유저id이 경우는 어떨까?
  - train data에 모든 데이터가 포함되지 않을 수 있다. (불완전한 데이터)
  - high cardinality, 원-핫 인코딩을 한다고 하면 너무 feature가 많아진다.
  - cold start, 특히 모델 production 상태에서는 따로 처리가 필요하다.

### 2.2.2 솔루션

1. 카테고리 input을 고유한 문자열로 변환한다.
2. 문자열에 대해 hash 알고리즘을 적용한다.
3. hash 결과를 원하는 bucket 수로 나누고 나머지 값을 최종 결과로 이용한다.

### 2.2.3 작동원리

- 불완전한 데이터
  - 당연히 정해진 bucket 수에 따라서 나머지 값들도 정해지기 떄문에 새로운 데이터가 발생해도 오류가 발생하지 않는다.
  - 물론 해당 값을 가지는게 정말 좋은 feature가 되기는 어렵다. 적절한 수의 bucket을 정해야 한다.
- high cardinality
  - 300명의 유저id가 있고 30개의 bucket을 사용하면 cardinality의 수가 30개로 줄어들고 10명이 같은 값을 갖는다.
- cold start

### 2.2.4 trade-off와 대안

- bucket collision: 어쩔 수 없다. 잘 조절해야 한다. bucket 수는 하이퍼파라미터이다.
- skewness: 카테고리 input의 분포가 많이 치우쳐 있으면 그만큼 손실이 커진다.
- feature aggregate: hash때문에 정보가 손실되니까 가능하면 feature의 정보를 이용해서 새롭게 만든다.

## 2.3 디자인 패턴2: 임베딩

### 2.3.1 문제

- unstructed feature를 모델에 넣어야 하는 경우가 많다.
- 또는 원-핫 인코딩보다 더 좋은 성능을 내고 싶을 수 있다.

### 2.3.2 솔루션

- 임베딩으로 고차원의 카테고리 input feature를 저차원 공간의 실수 벡터로 매핑

### 2.3.3 작동원리

### 2.3.4 trade-off와 대안

- 임베딩 차원: 하이퍼파라미터이다. 책에서는 카테고리 원소 수의 네제곱근 ~ 카테고리 원소 수의 제곱근의 1.6배라고 추천한다.
- AutoEncoder
- context language model: Word2Vec, BERT 같은 것들을 이용한다.

## 2.4 디자인 패턴 3: feauture cross

- input값들의 조합을 별도의 feature로 만들어서 모델의 input으로 사용

### 2.4.4 trade-off와 대안

- 무조건 교차를 해서 feature를 만드는 것은 아니다.
- numeric input의 경우는 binning을 하기도 한다.
- high cardinality의 경우 전처리를 하거나 임베딩을 이용한다.

## 2.5 디자인 패턴 4: multimodal input

### 2.5.1 문제

- input의 종류가 이미지, 수치, 언어 등 다양한 모델을 만들어야 한다.

### 2.5.2 솔루션

- 각각을 모델에 넣을 input화 하면 된다. 주로 임베딩을 한다.

### 2.5.3 트레이드오프와 대안

- 종류가 다른 input을 잘 결합해서 사용
- 동일한 데이터를 다양한 방법으로 표현
  - 예를 들어, 텍스트 데이터를 임베딩도 하고 BOW값도 feature로 사용할 수 있다.
