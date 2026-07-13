# KOSPI 포트폴리오 일관성 감사

기준일: 2026-07-13

이 문서는 README의 핵심 수치가 현재 공개 산출물과 일치하는지 확인하고, 과제 출발점과 개인 재구축의 범위를 구분하기 위한 감사 기록입니다.

## 범위와 역할 표기

- 프로젝트는 학부 `회귀분석 Team 과제`에서 출발했습니다.
- 지원서 기재 프로젝트 기간은 `2025.10.17–2025.12.07`입니다.
- 지원서에 정리된 본인 수행은 데이터 수집·일관성 검증, 시계열 진단·교정, 보고서 작성입니다.
- 현재 공개 저장소의 개인 재구축 범위는 외부 데이터 재수집, 68개 변경값 감사, ADF·KPSS, 공적분, HAC, 24개월 홀드아웃, 재현 문서입니다.
- 공개 Git 정리 기록은 `2026.06.27–현재`입니다.
- 원 과제 자료로 확인할 수 없는 팀 규모와 협업 방식은 추정해 적지 않습니다.

## 수치 일관성 확인

| 확인 항목 | README 표기 | 공개 산출물 | 상태 |
|---|---:|---|---|
| 분석 범위·완전성 | 2010-01~2024-12, 180개월, 결측 0개 | [`data_quality_summary.csv`](../outputs/reports/data_quality_summary.csv) | 일치 |
| 재수집 변경값 | 환율 42개 + WTI 26개 = 68개 | [`data_regeneration_comparison_summary.csv`](../outputs/metrics/data_regeneration_comparison_summary.csv) | 일치 |
| 수준 OLS | n=156, R² 0.8103, DW 0.2611 | [`model_diagnostics_comparison.csv`](../outputs/metrics/model_diagnostics_comparison.csv) | 일치 |
| 1차 차분 OLS | n=155, R² 0.5620, DW 2.0102 | [`model_diagnostics_comparison.csv`](../outputs/metrics/model_diagnostics_comparison.csv) | 일치 |
| 공적분 | p-value 0.5901, 공적분 근거 없음 | [`level_engle_granger_cointegration_test.csv`](../outputs/metrics/level_engle_granger_cointegration_test.csv) | 일치 |
| 차분 홀드아웃 | n=24, MAE 75.1762, RMSE 98.0467, R² 0.2307, 방향 정확도 66.67% | [`diff_holdout_metrics.csv`](../outputs/metrics/diff_holdout_metrics.csv) | 일치 |
| 같은 수준 척도의 RMSE | 수준 OLS 289.2909, 차분 수준 복원 253.4089 | [`holdout_same_scale_model_comparison.csv`](../outputs/metrics/holdout_same_scale_model_comparison.csv) | 일치 |

## 해석 일관성 확인

1. 수준 OLS의 높은 R²는 성과 지표가 아니라 허위회귀 위험을 보여주는 비교·진단 결과로 사용합니다.
2. Engle–Granger p-value 0.5901 때문에 수준 변수 사이의 안정적인 장기 균형관계를 주장하지 않습니다.
3. 1차 차분 OLS는 정상성이 확보된 단기 동시점 연관성의 주 모형이며, 계수를 인과효과로 해석하지 않습니다.
4. 잔차의 이분산성과 비정규성이 남아 계수 유의성은 HAC 표준오차 결과를 기준으로 판단합니다.
5. 홀드아웃 방향 정확도 66.67%는 24개월의 작은 표본에서 나온 보조 지표로 제한합니다.
6. 수준 복원 RMSE가 12.4% 개선됐지만 두 수준 예측의 R²가 모두 음수이므로 충분한 수준 경로 예측력을 주장하지 않습니다.

## 공개 기준

- 현재 저장소의 CSV와 노트북 결과를 수치의 단일 기준으로 사용합니다.
- README에는 CSV의 원값을 표시 자릿수에 맞게 반올림합니다.
- 과거 과제 보고서와 현재 공개 산출물의 수치가 다를 때는 재구축된 공개 산출물을 우선합니다.
- 개인 식별 정보와 지원 회사별 문서는 저장소에 공개하지 않습니다.
