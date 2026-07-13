# KOSPI 거시경제 변수 분석: 정상성 진단과 허위회귀 검증

![Python](https://img.shields.io/badge/Python-Data%20Analysis-3776AB?style=flat-square&logo=python&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-Data%20Processing-150458?style=flat-square&logo=pandas&logoColor=white)
![statsmodels](https://img.shields.io/badge/statsmodels-Econometrics-4051B5?style=flat-square)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)

**바로가기:** [분석 노트북](notebooks/) · [분석 보고서](docs/findings.md) · [프로젝트 설명](docs/portfolio_description.md) · [최종 산출물](#8-최종-산출물) · [일관성 감사](docs/consistency_audit.md)

> **문제** — 높은 R²를 그대로 신뢰할 때 발생하는 금융 시계열의 허위회귀 위험을 진단했습니다.<br>
> **한 일** — 2010~2024년 5개 월별 시계열에 정상성·공적분·잔차·HAC·24개월 홀드아웃 검증을 적용했습니다.<br>
> **핵심 결과** — 수준 OLS R² 0.8103에도 공적분 p-value 0.5901을 확인해 1차 차분 모형으로 해석을 교정했습니다.<br>
> **차별점** — 높은 적합도보다 진단 결과를 우선하고 모든 계수를 인과가 아닌 동시점 연관성으로 제한했습니다.

## 이 프로젝트로 보여주는 역량

- 외부 금융 데이터의 수집·월별 집계와 재구축 전후 품질 감사
- 정상성·허위회귀·잔차 구조를 확인한 뒤 수행한 강건 통계추론
- 시간 순서 기반 홀드아웃과 분석 한계를 포함한 결과 문서화

## 프로젝트 한눈에 보기

| 3분 요약 | 내용 |
|---|---|
| 문제 | 수준값의 높은 R²를 그대로 신뢰할 때 생기는 금융 시계열 허위회귀 위험을 검증했습니다. |
| 데이터 | 2010-01~2024-12 월별 180개 시점의 KOSPI·환율·금리·S&P 500·WTI를 사용했습니다. |
| 접근 | 데이터 재수집 감사 → ADF·KPSS → 공적분 → 수준/차분 OLS → 잔차·VIF → HAC → 24개월 홀드아웃 순으로 검증했습니다. |
| 핵심 결과 | 수준 OLS의 R²는 0.8103이지만 DW는 0.2611이었고 공적분 p-value는 0.5901이었습니다. 1차 차분 후 DW는 2.0102로 개선됐습니다. |
| 판단 교정 | 수준 모형은 허위회귀 위험을 보여주는 비교·진단용으로 제한하고, 차분 모형을 단기 동시점 연관성 해석의 주 모형으로 채택했습니다. |
| 최종 산출물 | 실행 노트북, 데이터 품질·재수집 감사, 진단·HAC 결과, 홀드아웃 평가, 대표 시각화, 분석 보고서를 함께 공개했습니다. |

## 나의 역할과 재구축 범위

| 구분 | 내용 |
|---|---|
| 프로젝트 출발점 | 학부 `회귀분석 Team 과제` |
| 지원서 기재 프로젝트 기간 | 2025.10.17–2025.12.07 |
| 지원서에 정리한 본인 수행 | 데이터 수집·일관성 검증, 시계열 진단·교정, 보고서 작성 |
| 개인 재구축 범위 | 외부 데이터 재수집과 68개 변경값 감사, ADF·KPSS, 공적분, HAC, 24개월 홀드아웃, 재현 문서를 전면 정리했습니다. |
| 공개 Git 정리 기록 | 2026.06.27–현재 |

기간과 역할은 과제 출발점, 지원서 기록, 이후의 개인 재구축을 구분해 적었습니다. 현재 저장소의 수치와 해석은 공개 산출물을 기준으로 하며, 과거 과제 보고서의 수치를 옮겨 쓰지 않았습니다.

## 1. 핵심 결과

| 항목 | 재현 결과 |
|---|---:|
| 분석 기간 | 2010-01 ~ 2024-12 |
| 수준 / 1차 차분 데이터 | 180행 × 5열 / 179행 × 5열 |
| 홀드아웃 | 2023-01 ~ 2024-12, 24개월 |
| 수준 OLS 학습 R² / Durbin–Watson | 0.8103 / 0.2611 |
| Engle–Granger 공적분 p-value | 0.5901 |
| 1차 차분 OLS 학습 R² / Durbin–Watson | 0.5620 / 2.0102 |
| HAC 5% 유의 변수 | 환율(−), S&P 500(+), WTI(+) |
| 차분 모형 홀드아웃 RMSE / 방향 정확도 | 98.0467 / 66.67% |
| 차분 수준 복원의 수준 OLS 대비 RMSE 개선 | 12.4% |

### 수준값과 1차 차분 모형의 판단 비교

| 구분 | 수준 OLS | 1차 차분 OLS |
|---|---:|---:|
| 학습 표본 | 156 | 155 |
| R² | 0.8103 | 0.5620 |
| Durbin–Watson | 0.2611 | 2.0102 |
| 포트폴리오 내 역할 | 허위회귀 위험 비교·진단 | 단기 연관성 해석의 주 모형 |
| 최종 판단 | 높은 설명력만으로 채택하지 않음 | 정상성 확보 후 HAC 추론과 홀드아웃으로 제한적 해석 |

Engle–Granger 공적분 p-value는 **0.5901**로, 수준 변수 사이의 안정적인 장기 균형관계를 지지하지 않았습니다. 따라서 R²가 0.8103에서 0.5620으로 낮아져도, 잔차 자기상관이 크게 완화된 차분 모형을 중심으로 해석을 전환했습니다.

수준 OLS는 R² 0.8103을 보였지만 비정상 변수, 강한 잔차 자기상관, 이분산성과 공적분 근거 부재가 함께 확인됐습니다. 따라서 주 모형으로 채택하지 않고 허위회귀 위험을 보여주는 비교·진단 모형으로 사용했습니다.

![월별 금융 변수 표준화 시계열](outputs/figures/monthly_series_standardized.png)

## 핵심 주장과 검증 경로

| 핵심 주장 | 공개 근거 |
|---|---|
| 2010-01~2024-12의 180개 월별 시점, 5개 변수, 결측 0개 | [`outputs/reports/data_quality_summary.csv`](outputs/reports/data_quality_summary.csv) |
| 재수집 전후 변경값은 환율 42개와 WTI 26개, 합계 68개 | [`outputs/metrics/data_regeneration_comparison_summary.csv`](outputs/metrics/data_regeneration_comparison_summary.csv) · [`outputs/metrics/data_regeneration_changed_values.csv`](outputs/metrics/data_regeneration_changed_values.csv) |
| 1차 차분 후 5개 변수 모두 정상성 판정 | [`outputs/metrics/stationarity_decision_summary.csv`](outputs/metrics/stationarity_decision_summary.csv) |
| 수준/차분 OLS의 표본·R²·DW·잔차 진단 비교 | [`outputs/metrics/model_diagnostics_comparison.csv`](outputs/metrics/model_diagnostics_comparison.csv) |
| Engle–Granger 공적분 p-value 0.5901 | [`outputs/metrics/level_engle_granger_cointegration_test.csv`](outputs/metrics/level_engle_granger_cointegration_test.csv) |
| HAC 5% 유의 변수는 환율·S&P 500·WTI | [`outputs/metrics/diff_ols_hac_inference.csv`](outputs/metrics/diff_ols_hac_inference.csv) |
| 차분 모형 홀드아웃 24개월, MAE 75.1762, RMSE 98.0467, 방향 정확도 66.67% | [`outputs/metrics/diff_holdout_metrics.csv`](outputs/metrics/diff_holdout_metrics.csv) |
| 동일한 KOSPI 수준 척도에서 RMSE 289.2909 → 253.4089 | [`outputs/metrics/holdout_same_scale_model_comparison.csv`](outputs/metrics/holdout_same_scale_model_comparison.csv) |

## 2. 분석 질문

1. KOSPI와 주요 금융 변수의 수준값은 정상 시계열인가?
2. 수준 OLS의 높은 R²와 유의한 계수를 신뢰할 수 있는가?
3. 수준 변수 사이에 안정적인 장기 균형관계인 공적분이 존재하는가?
4. 1차 차분 후 정상성과 잔차 자기상관이 개선되는가?
5. 이분산성을 고려한 HAC 추론에서도 유의한 변수가 유지되는가?
6. 차분 모형은 미래 24개월의 KOSPI 변화량과 방향을 어느 정도 설명하는가?
7. 차분 예측을 KOSPI 수준으로 복원했을 때 수준 OLS보다 오차가 줄어드는가?
8. 선형모형이 충분히 설명하지 못한 이상 시점은 언제인가?

## 3. 데이터 품질과 검증 설계

| 변수 | 의미 | 원천 |
|---|---|---|
| `KOSPI` | KOSPI 월말 종가 | Yahoo Finance |
| `Exchange_Rate` | 원/달러 환율 월말값 | Yahoo Finance |
| `Interest_Rate` | 국고채 3년 월별 금리 | 한국은행 ECOS |
| `SP500` | S&P 500 월말 종가 | Yahoo Finance |
| `WTI_Oil` | WTI 원유 선물 월말 종가 | Yahoo Finance |

Yahoo Finance의 일별 자료는 각 월의 마지막 유효 거래일 종가로 변환했고, 한국은행 ECOS의 금리 CSV는 월별 시계열로 파싱했습니다. 최종 데이터의 결측치는 0개입니다.

외부 소스의 과거값이 재수집 시점에 달라질 수 있어 재구축 전 데이터를 `data/reference/` 아래에 동결했습니다. 재구축 전·후를 비교한 결과 환율 42개월, WTI 26개월의 총 68개 값이 달라졌으며, 변경 내역을 별도 CSV로 보존했습니다. 기준 데이터는 감사용이며 모형 입력으로 사용하지 않습니다.

무작위 분할 대신 2010~2022년을 학습, 2023~2024년을 홀드아웃으로 설정해 시간 순서를 보존했습니다.

## 4. 분석 파이프라인

```text
금융시장 데이터 수집·ECOS 금리 CSV 파싱
→ 일별 데이터의 월말 집계·변수 병합
→ 날짜·중복·결측·재생성 검증
→ 수준·1차 차분 ADF·KPSS·ACF 진단
→ 수준 OLS·Engle–Granger 공적분 검정
→ 1차 차분 OLS·잔차·VIF 진단
→ HAC(Newey–West) 강건 표준오차 추론
→ 24개월 시계열 홀드아웃 평가
→ 차분 예측의 KOSPI 수준 복원·이상 잔차 탐지
```

수준 모형은 허위회귀 위험을 진단하는 비교 모형, 정상성이 확보된 1차 차분 모형은 단기 연관성을 해석하는 주 모형으로 사용했습니다.

## 5. 허위회귀 위험 진단

| 진단 | 수준 OLS | 1차 차분 OLS |
|---|---:|---:|
| 학습 표본 | 156 | 155 |
| R² | 0.810265 | 0.561996 |
| Durbin–Watson | 0.261111 | 2.010219 |
| Ljung–Box p-value | < 0.000001 | 0.859924 |
| Breusch–Pagan p-value | 0.000001 | 0.009529 |
| Jarque–Bera p-value | 0.195416 | 0.000006 |
| 최대 VIF | 5.783620 | 1.325317 |

KOSPI, S&P 500, 환율, 금리는 수준에서 비정상으로 판정됐고 WTI는 ADF와 KPSS 판정이 일치하지 않았습니다. 1차 차분 후에는 모든 변수가 정상성을 확보했습니다.

Engle–Granger 공적분 검정 p-value는 0.590111로, 수준 변수 사이에 안정적인 장기 균형관계가 있다는 근거를 확인하지 못했습니다. 1차 차분 후 잔차 자기상관은 크게 완화됐지만 이분산성과 비정규성이 남아 HAC 표준오차를 적용했습니다.

![수준 OLS 잔차 진단](outputs/figures/level_ols_residual_diagnostics.png)

![1차 차분 OLS 잔차 진단](outputs/figures/diff_ols_residual_diagnostics.png)

## 6. HAC 강건 추론

최대 시차 12개월의 HAC(Newey–West) 표준오차를 적용했습니다.

| 변수 | 계수 | HAC p-value | 해석 |
|---|---:|---:|---|
| 원/달러 환율 변화 | -0.561855 | 0.009472 | 유의한 음의 연관성 |
| 금리 변화 | -78.578855 | 0.070881 | 5% 수준에서 유의하지 않음 |
| S&P 500 변화 | 0.464325 | < 0.000001 | 유의한 양의 연관성 |
| WTI 변화 | 2.476767 | 0.000069 | 유의한 양의 연관성 |

금리 변화는 일반 OLS 표준오차에서는 5% 수준으로 유의했지만 HAC 적용 후 유의성을 잃었습니다. 이는 잔차 구조를 고려하지 않으면 변수의 유의성을 과대평가할 수 있음을 보여줍니다.

## 7. 홀드아웃과 해석 원칙

| 평가 대상 | MAE | RMSE | R² | 추가 지표 |
|---|---:|---:|---:|---:|
| 수준 OLS | 239.0324 | 289.2909 | -4.3679 | 평균오차 -237.4468 |
| 1차 차분 OLS | 75.1762 | 98.0467 | 0.2307 | 방향 정확도 66.67% |
| 차분 OLS 수준 복원 | 180.7887 | 253.4089 | -3.1189 | 평균오차 -77.3822 |

차분 모형의 방향 정확도는 24개월 중 16개월인 66.67%입니다. 표본이 작아 무작위 추측보다 통계적으로 우수하다고 단정하지 않고 보조 지표로만 해석했습니다.

차분 예측을 KOSPI 수준 경로로 복원하면 수준 OLS 대비 MAE는 24.4%, RMSE는 12.4% 개선됩니다. 그러나 두 수준 예측의 R²가 모두 0보다 낮아 평균 기준을 넘는 수준 경로 예측력을 확보했다고 보지 않습니다.

![KOSPI 월간 변화량 홀드아웃 예측](outputs/figures/holdout_kospi_first_difference_prediction.png)

![KOSPI 수준 경로 비교](outputs/figures/holdout_kospi_level_model_comparison.png)

표준화 잔차 `|z| ≥ 2`를 기준으로 수준 OLS와 차분 OLS에서 각각 8개 이상 시점을 식별했습니다. 수준 모형의 이상 잔차가 2021년에 연속적으로 집중된 현상은 개별 충격보다 모형의 지속적 과소예측과 자기상관을 의심할 근거로 해석했습니다.

## 8. 최종 산출물

| 구분 | 파일 | 설명 |
|---|---|---|
| 분석 노트북 | [`01_data_collection_cleaning.ipynb`](notebooks/01_data_collection_cleaning.ipynb) · [`02_stationarity_diagnostics.ipynb`](notebooks/02_stationarity_diagnostics.ipynb) · [`03_modeling_evaluation.ipynb`](notebooks/03_modeling_evaluation.ipynb) | 데이터 수집·정제부터 진단과 모델 평가까지의 실행 경로 |
| 데이터 품질·재수집 감사 | [`data_quality_summary.csv`](outputs/reports/data_quality_summary.csv) · [`data_regeneration_comparison_summary.csv`](outputs/metrics/data_regeneration_comparison_summary.csv) · [`data_regeneration_changed_values.csv`](outputs/metrics/data_regeneration_changed_values.csv) | 월별 데이터 완전성과 재수집 전후 68개 변경값 기록 |
| 진단·강건 추론 | [`stationarity_decision_summary.csv`](outputs/metrics/stationarity_decision_summary.csv) · [`model_diagnostics_comparison.csv`](outputs/metrics/model_diagnostics_comparison.csv) · [`level_engle_granger_cointegration_test.csv`](outputs/metrics/level_engle_granger_cointegration_test.csv) · [`diff_ols_hac_inference.csv`](outputs/metrics/diff_ols_hac_inference.csv) | 정상성, 수준·차분 진단, 공적분과 HAC 추론 결과 |
| 홀드아웃 평가 | [`diff_holdout_metrics.csv`](outputs/metrics/diff_holdout_metrics.csv) · [`holdout_same_scale_model_comparison.csv`](outputs/metrics/holdout_same_scale_model_comparison.csv) | 24개월 변화량 평가와 동일 수준 척도 비교 |
| 대표 시각화 | [`monthly_series_standardized.png`](outputs/figures/monthly_series_standardized.png) · [`diff_ols_residual_diagnostics.png`](outputs/figures/diff_ols_residual_diagnostics.png) · [`holdout_kospi_first_difference_prediction.png`](outputs/figures/holdout_kospi_first_difference_prediction.png) · [`holdout_kospi_level_model_comparison.png`](outputs/figures/holdout_kospi_level_model_comparison.png) | 시계열·잔차 진단과 홀드아웃 예측 비교 |
| 해석·수치 감사 | [`findings.md`](docs/findings.md) · [`consistency_audit.md`](docs/consistency_audit.md) | 결과 해석·한계와 README·CSV 간 교차검증 |

## 9. 프로젝트 구조

```text
.
├─ README.md
├─ requirements.txt
├─ config.yaml
├─ data/
│  ├─ raw/
│  │  ├─ README.md
│  │  └─ interest_rate.csv
│  ├─ reference/                    # 재구축 비교용 기준 데이터
│  └─ processed/
├─ notebooks/
│  ├─ 01_data_collection_cleaning.ipynb
│  ├─ 02_stationarity_diagnostics.ipynb
│  └─ 03_modeling_evaluation.ipynb
├─ docs/
│  ├─ consistency_audit.md
│  ├─ findings.md
│  ├─ portfolio_description.md
│  └─ submission_manifest.md
└─ outputs/
   ├─ figures/
   ├─ metrics/
   └─ reports/
```

## 10. 재현 방법

### 10.1 Python 환경 만들기

저장소 최상위 폴더에서 PowerShell을 열고 다음 명령을 순서대로 실행합니다.

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

가상환경을 활성화하지 않고 전용 Python 실행파일을 직접 사용하므로 PowerShell 실행 정책의 영향을 덜 받습니다.

### 10.2 데이터 준비

`data/raw/interest_rate.csv`를 유지합니다. KOSPI, S&P 500, 원/달러 환율, WTI는 첫 번째 노트북이 Yahoo Finance에서 수집하므로 인터넷 연결이 필요합니다. 세부 안내는 [`data/raw/README.md`](data/raw/README.md)에서 확인합니다.

### 10.3 노트북 실행

```powershell
.\.venv\Scripts\python.exe -m jupyter lab
```

JupyterLab에서 `01_data_collection_cleaning.ipynb` → `02_stationarity_diagnostics.ipynb` → `03_modeling_evaluation.ipynb` 순으로 실행합니다. 분석 기간, 변수, 홀드아웃 길이와 이상 잔차 기준은 `config.yaml`에서 관리합니다.

재현 기준 환경은 Python 3.13.3, `requirements.txt`의 고정 패키지 버전과 프로젝트 상대경로입니다.

## 11. 한계와 개선 방향

- 월별 동시점 선형관계만 분석했으며 시차효과는 모형화하지 않았습니다.
- 정책·수급·투자심리·변동성 등 중요한 설명변수가 누락될 수 있습니다.
- 구조적 단절과 변동성 군집을 별도 모형로 추정하지 않았습니다.
- 24개월 홀드아웃은 방향 정확도의 일반화 근거로 부족합니다.
- 차분 예측을 누적하면 오차가 쌓여 장기 수준 경로가 이탈할 수 있습니다.
- 이상 잔차와 경제 사건 사이의 인과관계는 검증하지 않았습니다.
- 모든 결과는 통계적 연관성이며 인과관계를 의미하지 않습니다.

자세한 검증 결과와 교정 전후 해석은 [`docs/findings.md`](docs/findings.md)에서 확인할 수 있습니다.

## 12. 결론

수준 OLS의 높은 R²를 성과로 받아들이지 않고 정상성·공적분·잔차 구조를 먼저 확인해 허위회귀 위험을 진단했습니다. 1차 차분과 HAC 강건 표준오차로 단기 연관성의 해석을 교정했으며, 24개월 홀드아웃 결과는 표본 제약이 있는 보조 지표로 제한했습니다. 이 프로젝트는 높은 적합도보다 검증 절차와 해석의 신뢰성을 우선한 과정입니다.
