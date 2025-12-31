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
- **결측치 처리**
- **클래스 불균형 해소** : 오버샘플링(Oversampling) / 클래스 가중치(class weight) 적용
- **범주형 변수 처리**
    + 순서형 : Ordinal encoder 처리
    + 일반범주 : One-Hot Encoding 처리
- **데이터 스케일링** : StandardScaler를 이용한 표준화

## 5. Feature Engineering 
- **파생 변수 생성**
- **변수 변환**
- **상호작용 항 생성**

## 5. 통계분석 핵심 인사이트
- 혈당이 중요함 : 다른 알려진 요인보다 통계적으로 매우 훨씬 강력하게 유의미하게 영향이 있음을 확인 (via 회귀분석)
![Q-Q Plot](output/qqplot.png)

## 6. 모델링 평가지표
- 최종 모델은 XGBoost로 선정

| Model | Accuracy | Recall | F1-Score | AUC-ROC |
| :--- | :--- | :--- | :--- | :--- |
| Random Forest | 0.85 | 0.70 | 0.74 | 0.88 |
| **XGBoost** | **0.86** | **0.75** | **0.78** | **0.91** |

> **Note** : 최종 대회 결과는 Public 0.70807 / Private 0.6978 

## 7. Feature Importance (옵션)
- SHAP 활용
- 예측 모델에서 영향력이 가장 컸던 지표 순위
1. AGE
2. BMI
- 그림 추가

## 8. Conclusion
- 결론 1
- 결론 2
- 결론 3

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


# 🔗 배지 및 이모지 공식 소스 링크
| 용도 | 사이트 이름 | 링크 |
| :--- | :--- | :--- |
| **배지 생성** | Shields.io | [https://shields.io/](https://shields.io/) |
| **로고/색상 검색** | Simple Icons | [https://simpleicons.org/](https://simpleicons.org/) |
| **이모지 검색** | Emoji Cheat Sheet | [https://github.com/ikatyang/emoji-cheat-sheet](https://github.com/ikatyang/emoji-cheat-sheet) |

