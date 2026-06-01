# 5G Throughput Prediction using Machine Learning

## 프로젝트 개요

본 프로젝트는 시점 (t)까지 관측된 네트워크 정보를 이용하여 다음 시점((t+1))의 Downlink Throughput을 예측하는 머신러닝 회귀 문제를 다룬다.

실제 5G 환경에서 수집된 데이터를 사용하여 다양한 머신러닝 모델을 비교하고, 어떤 정보가 throughput 예측에 중요한 역할을 하는지 분석하였다.

---

## 데이터셋

**Dataset:** 5G Production Dataset

원본 데이터는 서로 독립적인 83개의 trace로 구성되어 있으며, 다양한 서비스 환경과 이동 환경에서 수집되었다.

### Application

* Netflix
* Amazon Prime
* Download

### Mobility

* Driving
* Static

예측 대상(target)은 다음 시점의 Downlink Throughput이다.

---

## 데이터 전처리

주요 전처리 과정은 다음과 같다.

1. 83개 trace CSV 통합
2. 결측치 및 불필요 변수 제거
3. 범주형 변수 인코딩
4. Handover feature (`cellid_changed`) 생성
5. RF KPI 결측치 처리
6. Lag / Rolling 기반 시계열 feature 생성
7. Throughput 로그 변환

최종 feature는 다음 6개 그룹으로 구성하였다.

* Throughput History
* RF KPI
* Network Generation
* Application
* Mobility
* Cell Context

---

## 사용 모델

### Baseline

* Persistence
* Ridge Regression

### Tree-based Models

* Decision Tree
* Random Forest
* Gradient Boosting

하이퍼파라미터는 GridSearchCV와 GroupKFold를 이용하여 탐색하였다.

---

## 실험 설정

### Train/Test Split

* Trace-wise split
* Train : Test = 80 : 20
* Application × Mobility 기준 stratified split

### Cross Validation

* 5-fold GroupKFold
* Group = trace_id

### Evaluation Metrics

Log Scale

* R²_log
* RMSE_log

Original Scale (kbps)

* R²_kbps
* RMSE_kbps
* MAE_kbps

---

## 주요 결과

최종 모델은 **Random Forest**였다.

| Metric    | Value  |
| --------- | ------ |
| R²_log    | 0.761  |
| RMSE_log  | 2.197  |
| R²_kbps   | 0.742  |
| RMSE_kbps | 22,505 |
| MAE_kbps  | 7,073  |

주요 분석 결과는 다음과 같다.

* 예측 성능은 대부분 Throughput History feature에서 발생하였다.
* RF KPI 변수의 기여도는 예상보다 매우 낮았다.
* 주요 실패 사례는 고-throughput burst 과소예측과 급격한 throughput 감소 구간의 미포착이었다.
* 모델 자체보다 입력 데이터가 제공하는 정보의 한계가 성능을 제한하는 주요 원인으로 나타났다.

---

## 실행 방법 (Reproduction)

### Environment

실행 환경 정보는 `requirements.txt`에 정리하였다.

### Run

1. Google Colab에서 Notebook 실행
2. Dataset 업로드
3. 모든 셀 순차 실행
4. 결과 및 Figure 자동 생성

---

## Repository Structure

```text
.
├── throughput_prediction.ipynb
├── requirements.txt
├── README.md
├── figures/
└── artifacts/
```

---

## Use of Generative AI

본 프로젝트에서는 ChatGPT를 활용하여 보고서 문장 표현을 다듬고, 설명의 구조를 개선하며, 실험 결과 해석에 대한 아이디어를 논의하였다.

데이터 전처리, 모델 구현, 실험 수행, 결과 검증 및 최종 결론 도출은 모두 작성자가 직접 수행하였다.
