# KOSPI 거시경제 변수 분석: 정상성 진단과 허위회귀 검증

> 높은 수준 회귀 설명력을 그대로 신뢰하지 않고 정상성·공적분·잔차·강건 추론을 통해 금융 시계열 모형을 재검증한 프로젝트

2010년 1월부터 2024년 12월까지의 월별 KOSPI, 원/달러 환율, 국고채 3년 금리, S&P 500, WTI 시계열을 분석했습니다. 수준 OLS의 높은 R²를 성과로 제시하기보다 비정상성과 잔차 자기상관, 이분산성, 공적분 부재를 확인하고 1차 차분 OLS와 HAC 강건 표준오차로 해석을 교정했습니다.

모든 계수는 동일 월에 관측된 금융 변수와 KOSPI 변화 사이의 조건부 통계적 연관성으로 해석하며, 인과효과로 해석하지 않습니다.

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

수준 OLS는 R² 0.8103을 보였지만 비정상 변수, 강한 잔차 자기상관, 이분산성과 공적분 근거 부재가 함께 확인됐습니다. 따라서 주 모형으로 채택하지 않고 허위회귀 위험을 보여주는 비교·진단 모형으로 사용했습니다.

![월별 금융 변수 표준화 시계열](outputs/figures/monthly_series_standardized.png)

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

## 8. 저장소 구조

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
│  ├─ findings.md
│  ├─ portfolio_description.md
│  └─ submission_manifest.md
└─ outputs/
   ├─ figures/
   ├─ metrics/
   └─ reports/
```

## 9. 재현 방법

### 9.1 Python 환경 만들기

저장소 최상위 폴더에서 PowerShell을 열고 다음 명령을 순서대로 실행합니다.

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

가상환경을 활성화하지 않고 전용 Python 실행파일을 직접 사용하므로 PowerShell 실행 정책의 영향을 덜 받습니다.

### 9.2 데이터 준비

`data/raw/interest_rate.csv`를 유지합니다. KOSPI, S&P 500, 원/달러 환율, WTI는 첫 번째 노트북이 Yahoo Finance에서 수집하므로 인터넷 연결이 필요합니다. 세부 안내는 [`data/raw/README.md`](data/raw/README.md)에서 확인합니다.

### 9.3 노트북 실행

```powershell
.\.venv\Scripts\python.exe -m jupyter lab
```

JupyterLab에서 `01_data_collection_cleaning.ipynb` → `02_stationarity_diagnostics.ipynb` → `03_modeling_evaluation.ipynb` 순으로 실행합니다. 분석 기간, 변수, 홀드아웃 길이와 이상 잔차 기준은 `config.yaml`에서 관리합니다.

재현 기준 환경은 Python 3.13.3, `requirements.txt`의 고정 패키지 버전과 프로젝트 상대경로입니다.

## 10. 한계

- 월별 동시점 선형관계만 분석했으며 시차효과는 모형화하지 않았습니다.
- 정책·수급·투자심리·변동성 등 중요한 설명변수가 누락될 수 있습니다.
- 구조적 단절과 변동성 군집을 별도 모형로 추정하지 않았습니다.
- 24개월 홀드아웃은 방향 정확도의 일반화 근거로 부족합니다.
- 차분 예측을 누적하면 오차가 쌓여 장기 수준 경로가 이탈할 수 있습니다.
- 이상 잔차와 경제 사건 사이의 인과관계는 검증하지 않았습니다.
- 모든 결과는 통계적 연관성이며 인과관계를 의미하지 않습니다.

자세한 검증 결과와 교정 전후 해석은 [`docs/findings.md`](docs/findings.md)에서 확인할 수 있습니다.
