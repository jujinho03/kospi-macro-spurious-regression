# 제출본 구성 검증

## 1. 프로젝트 기본 파일

| 파일                 | 포함 여부 | 역할                              |
| ------------------ | ----- | ------------------------------- |
| `README.md`        | 포함    | 프로젝트 개요, 분석 방법, 핵심 결과, 실행 방법    |
| `config.yaml`      | 포함    | 분석 기간, 변수, 홀드아웃 길이, 이상 잔차 기준 관리 |
| `requirements.txt` | 포함    | Python 패키지 설치 목록                |
| `.gitignore`       | 포함    | 가상환경, 임시 파일, 내부 작업 문서 제외 설정     |

---

## 2. 데이터 파일

### 원천 데이터

| 파일                           | 포함 여부 | 설명                         |
| ---------------------------- | ----- | -------------------------- |
| `data/raw/README.md`         | 포함    | 금리 데이터 배치 및 형식 안내          |
| `data/raw/interest_rate.csv` | 포함    | 한국은행 ECOS 국고채 3년 월별 금리 데이터 |

Yahoo Finance 기반 시장 데이터는 노트북 실행 시 자동으로 수집됩니다.

### 기준 데이터

| 파일                                              | 포함 여부 | 설명                    |
| ----------------------------------------------- | ----- | --------------------- |
| `data/reference/kospi_macro_monthly_legacy.csv` | 포함    | 프로젝트 재구축 이전 데이터 비교 기준 |

기준 데이터는 데이터 변경 이력을 검증하기 위한 감사 자료이며 회귀모형 입력으로 직접 사용하지 않습니다.

### 처리 데이터

| 파일                                            | 포함 여부 | 설명           |
| --------------------------------------------- | ----- | ------------ |
| `data/processed/kospi_macro_monthly.csv`      | 포함    | 최종 월별 수준 데이터 |
| `data/processed/kospi_macro_monthly_diff.csv` | 포함    | 최종 1차 차분 데이터 |

---

## 3. 분석 노트북

| 순서 | 파일                                            | 포함 여부 | 역할                                                   |
| -: | --------------------------------------------- | ----- | ---------------------------------------------------- |
|  1 | `notebooks/01_data_collection_cleaning.ipynb` | 포함    | 금융 데이터 수집, 월별 집계, 금리 파싱, 데이터 병합 및 품질 검증              |
|  2 | `notebooks/02_stationarity_diagnostics.ipynb` | 포함    | ADF·KPSS 정상성 검정, ACF 분석, 차분 데이터 생성                   |
|  3 | `notebooks/03_modeling_evaluation.ipynb`      | 포함    | 수준·차분 OLS, 공적분·잔차·VIF 진단, HAC 추론, 홀드아웃 평가 및 이상 시점 탐지 |

노트북은 다음 순서로 실행해야 합니다.

```text
1. 01_data_collection_cleaning.ipynb
2. 02_stationarity_diagnostics.ipynb
3. 03_modeling_evaluation.ipynb
```

---

## 4. 데이터 품질 및 정상성 산출물

| 파일                                                         | 포함 여부 |
| ---------------------------------------------------------- | ----- |
| `outputs/reports/data_quality_summary.csv`                 | 포함    |
| `outputs/metrics/data_regeneration_comparison_summary.csv` | 포함    |
| `outputs/metrics/data_regeneration_changed_values.csv`     | 포함    |
| `outputs/metrics/stationarity_level_tests.csv`             | 포함    |
| `outputs/metrics/stationarity_first_difference_tests.csv`  | 포함    |
| `outputs/metrics/stationarity_decision_summary.csv`        | 포함    |
| `outputs/reports/stationarity_findings.md`                 | 포함    |

재구축 전후 데이터 비교 결과는 다음과 같습니다.

* 원/달러 환율 변경값: 42개
* WTI 변경값: 26개
* 전체 변경값: 68개

---

## 5. 모형 적합 및 진단 산출물

| 파일                                                           | 포함 여부 |
| ------------------------------------------------------------ | ----- |
| `outputs/metrics/model_fit_summary.csv`                      | 포함    |
| `outputs/metrics/model_residual_diagnostics.csv`             | 포함    |
| `outputs/metrics/model_diagnostics_comparison.csv`           | 포함    |
| `outputs/metrics/level_engle_granger_cointegration_test.csv` | 포함    |
| `outputs/metrics/level_ols_coefficients.csv`                 | 포함    |
| `outputs/metrics/diff_ols_coefficients.csv`                  | 포함    |
| `outputs/metrics/level_ols_hac_inference.csv`                | 포함    |
| `outputs/metrics/diff_ols_hac_inference.csv`                 | 포함    |
| `outputs/metrics/level_vif.csv`                              | 포함    |
| `outputs/metrics/diff_vif.csv`                               | 포함    |

---

## 6. 홀드아웃 평가 산출물

| 파일                                                         | 포함 여부 |
| ---------------------------------------------------------- | ----- |
| `outputs/metrics/level_holdout_metrics.csv`                | 포함    |
| `outputs/metrics/diff_holdout_metrics.csv`                 | 포함    |
| `outputs/metrics/holdout_same_scale_model_comparison.csv`  | 포함    |
| `outputs/metrics/diff_reconstructed_level_metrics.csv`     | 포함    |
| `outputs/metrics/level_holdout_predictions.csv`            | 포함    |
| `outputs/metrics/diff_holdout_predictions.csv`             | 포함    |
| `outputs/metrics/diff_reconstructed_level_predictions.csv` | 포함    |

---

## 7. 이상 잔차 및 상세 보고서

| 파일                                             | 포함 여부 |
| ---------------------------------------------- | ----- |
| `outputs/metrics/level_residual_anomalies.csv` | 포함    |
| `outputs/metrics/diff_residual_anomalies.csv`  | 포함    |
| `outputs/reports/modeling_findings.md`         | 포함    |
| `outputs/reports/level_ols_summary.txt`        | 포함    |
| `outputs/reports/diff_ols_summary.txt`         | 포함    |
| `outputs/reports/level_ols_hac_summary.txt`    | 포함    |
| `outputs/reports/diff_ols_hac_summary.txt`     | 포함    |

---

## 8. 대표 시각화

| 파일                                                              | 포함 여부 |
| --------------------------------------------------------------- | ----- |
| `outputs/figures/monthly_series_standardized.png`               | 포함    |
| `outputs/figures/level_ols_residual_diagnostics.png`            | 포함    |
| `outputs/figures/diff_ols_residual_diagnostics.png`             | 포함    |
| `outputs/figures/holdout_kospi_first_difference_prediction.png` | 포함    |
| `outputs/figures/holdout_kospi_level_model_comparison.png`      | 포함    |
| `outputs/figures/residual_zscore_anomaly_timeline.png`          | 포함    |

정상성 진단용 수준·차분 시계열 및 ACF 그래프도 `outputs/figures/`에 포함합니다.

---

## 9. 포트폴리오 설명 문서

| 파일                              | 포함 여부 | 역할                      |
| ------------------------------- | ----- | ----------------------- |
| `docs/portfolio_description.md` | 포함    | 채용 담당자 및 면접 설명용 프로젝트 요약 |
| `docs/submission_manifest.md`   | 포함    | 제출 파일 구성 및 재현성 검증       |

---

## 10. Git 추적 제외 항목

다음 항목은 저장소에 포함하지 않습니다.

| 항목                    | 제외 사유              |
| --------------------- | ------------------ |
| `.venv/`              | 로컬 Python 가상환경     |
| `.ipynb_checkpoints/` | Jupyter 임시 체크포인트   |
| `.vscode/`            | 사용자별 IDE 설정        |
| `__pycache__/`        | Python 캐시          |
| `.env` 및 `.env.*`     | 환경변수 및 비밀정보        |
| `마스터프롬프트_*.md`        | 내부 작업용 문서          |
| 임시·로그 파일              | 재현성에 필요하지 않은 로컬 파일 |

---

## 11. 최종 재현성 검증 기준

### 데이터 수집 및 정제

* 최종 수준 데이터: 180행 × 5열
* 최종 결측치: 0개
* 변경값 추적: 총 68개
* 환율 변경값: 42개
* WTI 변경값: 26개

### 정상성 진단

* 수준 KOSPI, S&P 500, 환율, 금리: 비정상
* 수준 WTI: 판정 불일치
* 모든 1차 차분 변수: 정상
* 차분 데이터: 179행 × 5열

### 수준 OLS

* R²: 0.810265
* Durbin–Watson: 0.261111
* Ljung–Box p-value: 0.000001 미만
* Engle–Granger p-value: 0.590111

### 1차 차분 OLS

* R²: 0.561996
* Durbin–Watson: 2.010219
* Ljung–Box p-value: 0.859924
* Breusch–Pagan p-value: 0.009529

### HAC 추론

* 원/달러 환율 변화: 5% 수준에서 유의
* 금리 변화: 5% 수준에서 유의하지 않음
* S&P 500 변화: 5% 수준에서 유의
* WTI 변화: 5% 수준에서 유의

### 홀드아웃 평가

* 차분 모형 R²: 0.230724
* 방향 정확도: 66.67%
* 차분 수준 복원 모형 MAE: 180.788683
* 차분 수준 복원 모형 RMSE: 253.408947

### 이상 잔차

* 수준 OLS: 8개
* 1차 차분 OLS: 8개

---
