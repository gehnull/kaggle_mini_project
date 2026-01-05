# kaggle_mini_project

<div align="center">
  <img src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white">
  <img src="https://img.shields.io/badge/pandas-150458?style=for-the-badge&logo=pandas&logoColor=white">
  <img src="https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white">
  <img src="https://img.shields.io/badge/xgboost-EB4223?style=for-the-badge&logo=xgboost&logoColor=white">
</div>

# 🩺 프로젝트 주제: 머신러닝 기반 당뇨병 진단 예측 모델
- 본 프로젝트는 대규모 건강 설문 데이터를 활용하여 **당뇨병 고위험군을 사전에 선별할 수 있는 머신러닝 기반 예측 모델을 구축** 하고, 개인의 생활습관 및 사회적 요인이 당뇨병 진단에 미치는 영향을 분석하는 것을 목표로 한다. 


## 1. Project Overview
- **문제정의** : 의료 접근성이 제한된 환경에서도 간단한 설문 정보만으로 당뇨병 고위험군을 식별할 수 있는 방법이 필요함
- **과제** : 생활습관 또는 신체 상태를 활용한 당뇨병 유무 이진 분류
- **데이터셋** : [Diabetes Health Indicators Dataset](https://www.kaggle.com/datasets/mohankrishnathalla/diabetes-health-indicators-dataset/data)
- **데이터 개요** 
  + **관측치 수** : 100,000명
  + **변수 수** : 31개
  + **데이터 유형** : 건강 설문 기반 정형 데이터
  + **타겟 변수** : diagnosed_diabetes (이진 분류)
- **목표** : 설문 응답만으로 **당뇨병 고위험군을 선별할 수 있는 예측 모델 구축**

## 2. 변수 요인군
본 프로젝트에서는 변수의 인과적 역할과 데이터 누수 가능성을 고려하여 다음과 같이 요인군을 구성하였다.

| 요인군 | 주요 변수 | 변수 유형 |
|---|---|---|
| 배경변수 | age, gender, ethnicity | 연속형 / 범주형 |
| 사회경제/환경 | education_level, income_level, employment_status, screen_time_hours_per_day | 연속형 / 범주형 |
| 생활습관 | smoking_status, alcohol_consumption_per_week, physical_activity, diet_score, sleep_hours_per_day | 연속형 / 범주형 |
| 유전/병력 | family_history_diabetes, hypertension_history, cardiovascular_history | 이진 범주형 |
| 신체/대사 지표 | bmi, waist_to_hip_ratio, systolic_bp, diastolic_bp, heart_rate, cholesterol_total, triglycerides, hdl_cholesterol, ldl_cholesterol | 연속형 |
| 타겟 변수 | diagnosed_diabetes | 이진 범주형 |

※ 각 변수의 세부 정의 및 값의 의미는 아래 Data Dictionary에 정리하였다.


## 3. Problem Definition
- **데이터 특성**
  + 건강 설문 기반의 정형 데이터로 이진 분류 문제에 해당함
  + 범주형 및 연속형 변수가 혼재된 구조
  + 다수의 이진 변수를 포함하며 변수 간 상호작용 가능성 존재
  + 설문 응답 기반 데이터 특성상 노이즈 및 응답 편향 가능성 존재
  + 당뇨 진단 결과와 정보적으로 중복될 가능성이 있는 변수는
  데이터 누수 방지를 위해 설명 변수에서 제외함
- **분석 방향**
    + 통계분석 : 다중회귀, 분산분석, 로지스틱회귀
    + 머신러닝 : 로지스틱회귀, 결정트리, XGBoost, LightGBM

## 4. 데이터 전처리
- **결측치 확인** : train.csv 기준 결측치 없음
- **범주형 변수 처리**
    + 순서형 : Ordinal Encoding 적용
    + 명목형 : One-Hot Encoding 적용
- **데이터 스케일링** : 수치형 변수에 대해 StandardScaler를 이용한 표준화
- **클래스 불균형 해소** : 전체 데이터에 대한 오버샘플링은 적용하지 않았으며, 트리 기반 모델(XGBoost, LightGBM)에 한해 scale_pos_weight를 사용하여 불균형을 보정함

## 5. Feature Engineering 
본 프로젝트에서는 각 변수의 단독 효과를 명확히 이해하기 위해 단일 변수 기반 분석을 먼저 수행하였다. 

이후 기존 문헌의 가설을 데이터 구조에 맞게 재구성하여 대사적 위험, 생활습관 패턴, 사회경제적 요인의 결합 효과를 반영한 파생변수 및 상호작용 항을 생성하는 방향으로 분석을 확장할 계획이다. 

- **파생 변수 생성**
  + 지질 대사 위험 지표:  
    `TG_HDL_ratio = triglycerides / hdl_cholesterol`
  + 생활습관 위험 점수:  
    흡연, 신체활동 부족, 고위험 음주, 낮은 식이 점수를 이진화한 후 합산한  
    `lifestyle_risk_score`
- **변수 변환**
  + 음주량(`alcohol_consumption_per_week`)에 대해 문헌 기준을 참고한 상대적 고위험 음주군 파생 변수 생성
- **상호작용 항 생성**
  + 유전 × 생활습관 상호작용:  
    `family_history_diabetes × smoking_status`,  
    `family_history_diabetes × age`
  + 사회경제적 요인 × 행동 요인 상호작용:  
    `income_level × physical_activity_minutes_per_week`
  + 신체 지표 간 상호작용:  
    `bmi × waist_to_hip_ratio`


## 6. 통계분석 핵심 인사이트
- 다변량 로지스틱 회귀분석 결과 가족력, 연령, 체질량지수(BMI), 수축기 혈압은 다른 요인들을 통제한 이후에도 당뇨 진단 여부와 통계적으로 유의미한 연관성을 보이는 핵심 위험 요인으로 확인되었다. 
- 특히 가족력은 전체 변수 중 가장 큰 효과 크기를 보이며, 유전적 요인의 중요성을 시사하였다. 
- 혈당 및 진단 관련 변수(glucose, insulin, HbA1c)는 당뇨 진단 결과와 정보적으로 중복될 가능성이 있어 데이터 누수 방지를 위해 예측 모델 입력 변수에서는 제외하였다. 

## 7. 모델링 평가지표

본 모델 비교 결과는 조별 프로젝트에서 수행한
Stratified K-Fold 교차검증 기반 검증 실험 결과를 정리한 것이다.

| Model | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.63 | 0.75 | 0.59 | 0.66 | 0.69 |
| Decision Tree | 0.63 | 0.75 | 0.61 | 0.67 | 0.69 |
| XGBoost | 0.68 | 0.70 | **0.85** | **0.77** | 0.72 |
| **LightGBM** | 0.65 | **0.77** | 0.63 | 0.69 | **0.72** |

- 평가는 Stratified K-Fold 교차검증 기반 Validation 결과이다.
- ROC-AUC를 주요 지표로 삼았으며 Precision과 Recall의 균형을 함께 고려하였다.
- 조별 프로젝트 기준에서는 LightGBM이 ROC-AUC 측면에서 가장 안정적인 성능을 보여
  최종 모델로 선정되었다.

> Note: 조별 프로젝트에서 Kaggle 대회에 제출한 결과,
> Public/Private Leaderboard 기준 ROC-AUC는 약 0.69 수준을 기록하였다.


## 8. Feature Importance
다변량 로지스틱 회귀 분석 결과를 통해 각 변수의 독립적인 영향력을 시각적으로 확인하였다. 
- 가족력, 중성지방, BMI, 고혈압 병력, 연령, 수축기 혈압 등이 당뇨 진단 위험과 양의 방향으로 강한 연관성을 보임
- HDL 콜레스테롤, 식이 점수, 신체활동은 보호 요인으로 작용하였다. 


![Logistic Regression Coefficients](output/Logistic_Regression_Coefficients.png)

## 9. 결론
- 가족력, 연령, BMI, 수축기 혈압, 중성지방이 당뇨병의 핵심 위험 요인으로 확인되었다. 
- 혈당 관련 변수를 제외한 상태에서도 ROC-AUC 약 0.69 수준의 예측 성능을 확보하였다. 
- 설문 기반 정보만으로도 당뇨병 고위험군 선별 가능성을 확인하였다. 

## 10. 한계 및 향후 분석 방향
- 한계
  + 각 변수의 단독 효과를 명확히 파악하기 위해 단일 변수 기반 분석을 우선적으로 수행한 점
- 향후 분석 방향
  + 향후 Feauture Engineering 단계에서 제안한 바와 같이 지질 대사 지표, 생활습관 위험 점수, 사회경제적 요인과 행동 요인의 상호작용 항 등을 추가로 생성해서 분석 확장 계획이다

# 보고서
- 프로젝트 상세보고서는 PDF 슬라이드 자료를 참고하여 주세요
- 00 보고서 : [당뇨병 예측 모델링 : 통계분석 및 머신러닝 접근](report/프로젝트보고서.pdf)
- 분석코드 : [분석코드](분석코드.ipynb)




## [부록] Data Dictionary (주요 핵심 변수)

| 변수명 | 설명 | 값의 의미 |
|---|---|---|
| Diabetes_binary | 당뇨 여부 (Target) | 0: 음성, 1: 당뇨/전단계 |
| HighBP | 고혈압 여부 | 0: 정상, 1: 고혈압 |
| BMI | 체질량 지수 | 연속형 |
| Smoker | 흡연 여부 | 0: 비흡연, 1: 100개비 이상 |
| PhysActivity | 신체 활동 여부 | 최근 30일 운동 여부 (0/1) |
| GenHlth | 주관적 건강 상태 | 1(매우 좋음) ~ 5(매우 나쁨) |
| Age | 연령대 | 1(18–24) ~ 13(80+) |

