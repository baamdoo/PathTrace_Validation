# Stage3 실제 유리구슬·연기 대표씬 결과

**Smoke와 clear 모두 동결한 평균·분산 감소·depth 기준에서 PASS_SCOPED. 사용자 육안 검토는 PENDING이며 전체 stage_pass는 false다.**

[실제 RGB 비교와 24개 이미지](stage3-representative/glass_smoke_lighting_v3/README.md) · [전체 수치](stage3-representative/glass_smoke_lighting_v3/summary.json) · [실행·QA 영수증](stage3-representative/glass_smoke_lighting_v3/analysis_receipt.json)

68개 원본 EXR는 모두 정상 종료했고 actual dimensions·seed·SPP·depth·소스/실행 파일 해시를 검사했다. 기본128²는 smoke/clear×engine/PBRT×4 seeds(11,29,47,83)×64/256/1024 SPP의48장, depth64 대조는 같은4 seeds×1024 SPP의16장, 512² hero는 seed11×2048 SPP의4장이다. Hero는 독립4-run 통계에 합치지 않는다.

광학계는 회색 재질·파장 독립 IOR1.5·회색 흡수/산란 계수다. 양쪽이 동일 PLY, density, 카메라, 조명, tent film 계약을 사용한다. PBRT는 wavelength jitter를 켜서 raw RGB를 비교하고 그 분광 샘플링 노이즈를 그대로 포함한다. No-geometry white calibration은 광량 source 검사이며 이 이미지에 맞춘 보정계수를 사용하지 않았다. 표시식은 공통 exposure1 + Reinhard + sRGB이며 denoise·이미지별 노출 맞춤이 없다.

## 평균과 불확실성

5개 geometry-only 영역(full frame, glass foreground/interior/rim, far background)의 RGB와 Y를 검사한다. 렌더 전 고정한 band는 ±max(0.003, 0.02×|PBRT 평균|)다. 각 seed의 공간평균을 관측값으로 사용하는 Welch-t 95%와 독립 backend whole-run bootstrap 95%의 합집합 구간 전체가 band 안에 있어야 통과한다. 이는 개별 marginal CI이며 동시 신뢰구간이나 정확한 0 편향 증명은 아니다.

| 씬 / 영역 | Engine Y | PBRT Y | E−P | 95% CI | ±band |
|---|---:|---:|---:|---|---:|
| smoke / full_frame | 0.1977794 | 0.1978941 | -0.0001146575 | [-0.0004341586, 0.0002048436] | 0.003957881 |
| smoke / glass_foreground | 0.2109515 | 0.2111221 | -0.0001705837 | [-0.0004742364, 0.000133069] | 0.004222441 |
| smoke / glass_interior | 0.2011514 | 0.2012826 | -0.0001312704 | [-0.0004686699, 0.0002061291] | 0.004025653 |
| smoke / glass_rim | 0.2650917 | 0.2654794 | -0.0003877679 | [-0.001248452, 0.0004729162] | 0.005309589 |
| smoke / background_far | 0.1930703 | 0.1931353 | -6.500442e-05 | [-0.0004290358, 0.000299027] | 0.003862705 |
| clear / full_frame | 0.1910393 | 0.1911464 | -0.0001070982 | [-0.0002972837, 8.308736e-05] | 0.003822928 |
| clear / glass_foreground | 0.1919563 | 0.1921132 | -0.0001569053 | [-0.0003098519, -3.958626e-06] | 0.003842264 |
| clear / glass_interior | 0.1794655 | 0.1795829 | -0.0001173845 | [-0.0002646072, 2.983819e-05] | 0.003591658 |
| clear / glass_rim | 0.2609609 | 0.2613362 | -0.0003752355 | [-0.001058716, 0.0003082454] | 0.005226723 |
| clear / background_far | 0.1963603 | 0.196411 | -5.073287e-05 | [-0.0003776859, 0.0002762201] | 0.00392822 |

일부 clear 영역 CI는0을 제외해도 사전 허용범위 안에 들어간다. 이를 완전한 일치나 residual 부재로 바꾸지 않는다.

## 노이즈와 수렴

픽셀별 독립 seed 표본분산(ddof=1)을 영역에서 평균했다. 1024/64 SPP 분산비의 whole-run bootstrap 단측95% 상한<1이 양쪽 모두에서 성립한다. 중첩 SPP와 depth는 같은 seed 재추출을 공유하고 backend 간 재추출은 독립이다. 아래 다른 renderer 분산비는 진단값이며 양쪽 noise가 같다는 gate가 아니다.

| 씬 / 영역 | Engine/PBRT Y분산,1024SPP | 95% CI |
|---|---:|---|
| smoke / full_frame | 0.7099291 | [0.4040897, 1.226141] |
| smoke / glass_interior | 1.142995 | [0.6613412, 1.981502] |
| clear / full_frame | 0.4043034 | [0.2253273, 0.7349621] |
| clear / glass_interior | 0.8046747 | [0.4614454, 1.426804] |

| 씬 / renderer | 전체화면 Y분산 1024/64 | 단측95% 상한 |
|---|---:|---:|
| smoke / engine | 0.05509663 | 0.05722379 |
| smoke / pbrt | 0.05731455 | 0.0629447 |
| clear / engine | 0.04078954 | 0.04308063 |
| clear / pbrt | 0.05679911 | 0.06602228 |

회색 engine 출력과 spectral PBRT 출력의 분산 차이를 관측했지만 모든 차이를 분광 샘플링 하나의 원인으로 분리한 실험은 아니다. 네 seed와 세 SPP만으로 수렴 차수나 모든 경로의 무편향성을 증명하지 않는다.

## Depth 32→64

같은 renderer·seed의 1024 SPP 차이(64−32)에 paired-t/whole-run bootstrap 95% 합집합 CI를 적용했다. 전체 RGB/Y·5영역 CI는 ±max(0.0015, 0.01×|depth64 평균|) 안이다. 효과가 정확히0인 것은 아니다.

| 씬 / renderer | 전체화면 ΔY(64−32) | 95% CI | ±band |
|---|---:|---|---:|
| smoke / engine | 4.085194e-05 | [3.280617e-05, 4.88977e-05] | 0.001978203 |
| smoke / pbrt | 3.754545e-05 | [3.602987e-05, 3.906102e-05] | 0.001979316 |
| clear / engine | 6.4267e-07 | [-8.601837e-08, 1.371358e-06] | 0.0019104 |
| clear / pbrt | 9.905766e-07 | [5.777671e-07, 1.403386e-06] | 0.001911474 |

## 실제 RGB와 보조 SSIM

| 씬 | 128²,1024SPP mean4 SSIM | 512²,2048SPP 단일hero SSIM |
|---|---:|---:|
| smoke | 0.97833158 | 0.94302309 |
| clear | 0.98885522 | 0.96680882 |

SSIM은 고정 표시식의 보조 진단이며 acceptance threshold를 두지 않았다. 해상도·평균 횟수가 다른 두 열의 SSIM을 수렴 비교로 해석하지 않는다. Hero에는 분산이나 CI가 없다. 개별24 PNG artifact QA는 라벨·스케일·원본 대응 확인이며 사용자 승인과 다르다.

## 측정 시간 — Stage4 성능 판정 아님

단위는 초다. Engine 수치는 각 seed의 첫 accumulation부터 현재 EXR readback/write까지 누적 wall time이며 이전 checkpoint 출력·CPU/frame scheduling을 포함하고 scene load를 제외한다. PBRT 수치는 매 표본의 CPU 프로세스 startup/load/render/output 전체다. 마지막 열은 matrix runner가 측정한 job 전체 프로세스 시간으로 engine 초기화·종료 대기를 포함한다. 범위가 달라 cross-renderer 속도 배율을 산출하지 않는다.

| 획득 / mode / renderer | SPP | n | 해당 범위 중앙값 [min,max] | job process |
|---|---:|---:|---|---:|
| base / smoke_engine | 1024 | 4 | 5.415 [5.380, 5.564] | 431.377 |
| base / smoke_pbrt | 1024 | 4 | 11.333 [11.171, 11.497] | 59.852 |
| base / clear_engine | 1024 | 4 | 1.692 [1.684, 1.830] | 145.167 |
| base / clear_pbrt | 1024 | 4 | 10.513 [10.452, 10.617] | 55.602 |
| depth / smoke_engine | 1024 | 4 | 5.499 [5.474, 5.657] | 436.523 |
| depth / smoke_pbrt | 1024 | 4 | 11.161 [11.143, 11.390] | 45.020 |
| depth / clear_engine | 1024 | 4 | 1.687 [1.674, 1.817] | 151.749 |
| depth / clear_pbrt | 1024 | 4 | 10.505 [10.436, 10.615] | 42.209 |
| hero / smoke_engine | 2048 | 1 | 54.086 [54.086, 54.086] | 147.844 |
| hero / smoke_pbrt | 2048 | 1 | 345.033 [345.033, 345.033] | 345.177 |
| hero / clear_engine | 2048 | 1 | 23.197 [23.197, 23.197] | 158.705 |
| hero / clear_pbrt | 2048 | 1 | 326.310 [326.310, 326.310] | 326.456 |

## 근거와 남은 경계

[독립 검산](stage3-independent-statistics-review.json)은 원본68 EXR·400개 해시·2456개 수치 항목을 별도식으로 확인했고 최대차9.75e−14, blocker0이었다. ROI는 저장된 geometry distance로 재구성했으며 mesh projection을 별도로 다시 증명하지 않았다. Producer SSIM과 그림은 독립 수치 검산의 재생성 범위 밖이며 이번 artifact QA로 별도 확인했다.

[Artifact QA](stage3-figure-qa.json) · [원 통계 완료 receipt](stage3-statistics-original-receipt.json) · [시간 원범위](stage3-measured-timings.json) · [Stage2 최종 준비 범위](stage3-stage2-final-report.md).

출처와 설치·경계 수정의 검증은 authored bounded scene에 한정된다. 모든 host/air overlap·vertex/nonmanifold·subepsilon interval을 보장하지 않고 실제 GPU optical-coefficient/flag AOV는 별도 측정하지 않았다. 원 EXR/NPZ/summary와 이전 실패 기록은 보존했다. Stage2 진단 육안 면제는 승인으로 바꾸지 않으며 새 Stage3 실제 이미지는 사용자 검토 대기다. Stage4의 고립된 시간 비교는 아직 수행하지 않았다.
