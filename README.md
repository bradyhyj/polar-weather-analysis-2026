# 🌍 남극 기후 데이터를 활용한 한반도 기상 이변 예측 알고리즘

남극 관측 기지(세종, 장보고)의 기상 데이터를 분석해 한반도 주요 도시(대구, 부산)의 폭염과 한파를 사전 예측하는 머신러닝 프로젝트입니다. 

대구와 부산을 자주 오가며 체감한 극심한 기온 차이에서 착안하여, 사방이 막힌 분지(대구)와 바다가 인접한 해안가(부산)의 지형적 특성이 기상 이변 도달 시차(Time-Lag)에 어떤 영향을 미치는지 데이터로 교차 검증하고 지역 맞춤형 예측 모델을 구축했습니다.

---

## 🛠 Tech Stack
* **Language:** Python
* **Data Processing:** Pandas, NumPy
* **Visualization:** Matplotlib, Seaborn
* **Machine Learning:** Scikit-learn (Random Forest)
<br />

## 📊 Data Pipeline
1. **데이터 수집:** Open-Meteo API(극지방 날씨) 및 기상청 공공데이터(대구/부산 날씨) 연동.
2. **전처리:** 
   * 선형 보간법(Linear Interpolation)을 통한 결측치 처리.
   * 데이터 노이즈 제거를 위해 7일 이동평균(7-day Smoothing) 적용.
3. **파생 변수 생성:**
   * 최고기온 & 상대습도 ➔ 여름철 폭염 불쾌지수(THI) 도출.
   * 최저기온 & 풍속 ➔ 겨울철 한파 체감온도(WindChill) 도출.
<br />

## 🔍 Key Insights: 타임래그(Time-Lag) 교차 검증
남극 2개 기지와 한반도 2개 도시의 사계절 기후 데이터를 바탕으로 총 8가지 가설에 대한 타임래그 교차 검증을 수행했습니다.

* **세종기지 평균기온 ➔ 부산 체감온도:** 3일 뒤 최대 영향 (상관계수 -0.648)
* **장보고기지 평균기온 ➔ 대구 불쾌지수:** 12일 뒤 최대 영향 (상관계수 -0.839)
* **장보고기지 평균기온 ➔ 부산 불쾌지수:** 20일 뒤 최대 영향 (상관계수 -0.819)

**💡 지형적 차이 증명**
사방이 막힌 분지(대구)는 남극의 기후 변화가 12일 만에 도달하지만, 바다가 열용량 댐 역할을 하는 해안가(부산)는 20일이 소요된다는 사실을 통계적으로 증명했습니다.

<p align="center">
  <img width="2895" height="2346" alt="cross_validation_heatmap" src="https://github.com/user-attachments/assets/fd94f439-1de4-4656-ae40-87355437d2dd" alt="교차 검증 히트맵" width="48%" />
  <img width="4750" height="1752" alt="time_lag_results" src="https://github.com/user-attachments/assets/d9d9d886-1d92-4e5e-883a-56cc45a29a99" alt="타임래그 분석 결과" width="48%" />
</p>
<br />


## 🚀 Predictive Model (Random Forest)
단순 기온 데이터에 3일 이동평균(추세 변수)과 월별 데이터(계절성)를 결합하여 불쾌지수 상위 10%의 폭염을 예측하는 분류 모델을 구축했습니다. 
극심한 데이터 불균형 문제는 `class_weight='balanced'` 파라미터를 적용하여 해결했습니다.

| 타겟 지역 | 적용 시차(Lag) | 학습 피처(Features) | 정확도(Accuracy) | 폭염 재현율(Recall) |
| :--- | :--- | :--- | :--- | :--- |
| **대구 (분지)** | 12일 | `Jangbogo_Lag12`, `Lag12_MA3`, `Sejong_Lag12`, `Month` | 93.76% | 0.56 |
| **부산 (해안가)** | 20일 | `Jangbogo_Lag20`, `Lag20_MA3`, `Sejong_Lag20`, `Month` | 93.96% | 0.61 |

**📈 Feature Importance 분석 결과**
* 계절성(`Month`) 변수가 모델 중요도의 약 50~52.6%를 차지했습니다.
* 장보고/세종기지의 기온 흐름 데이터가 나머지 약 47.4~50%의 중요도를 기록하여, 남극 기상 데이터가 한반도 기후 예측의 핵심 선행 지표임을 머신러닝으로 입증했습니다.
