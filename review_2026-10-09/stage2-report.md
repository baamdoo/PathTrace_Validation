# Heterogeneous-medium Stage 2 결과

**전체 판정: 미통과 / INCONCLUSIVE.** 흡수·산란의 아래 수치 검증과 별도로 결정론적 진공 대조군에서 엔진 경계 결함이 확인됐습니다. 사용자 육안 검토는 아직 대기이며 Stage3는 시작하지 않았습니다.

128×128, 4개의 독립 seed(11/29/47/83), 6개 매질 변형과5개 진공 대조군으로 **320개 EXR**를 정상 종료로 확보했습니다. 흡수는256/1024/4096 SPP, 산란은64/256/1024 SPP와 depth32·1024 SPP 추가 cohort를 사용했습니다. 모든 정량 비교는 linear EXR이며 PNG는 검토용입니다.

| 씬 | 평균 기준 | 분산 감소 | depth16→32 | Engine / PBRT Y 분산 |
|---|---|---|---|---:|
| constant_absorption | PASS_SCOPED_EQUIVALENCE | PASS_VARIANCE_DECREASE_ONLY | depth1 absorption oracle | 1.0342× |
| ramp_absorption | PASS_SCOPED_EQUIVALENCE | PASS_VARIANCE_DECREASE_ONLY | depth1 absorption oracle | 1.0240× |
| constant_scatter_g0 | PASS_SCOPED | PASS_SCOPED | PASS_SCOPED | 1.7536× |
| ramp_scatter_gp05 | PASS_SCOPED | PASS_SCOPED | PASS_SCOPED | 1.5326× |
| ramp_scatter_gm05 | PASS_SCOPED | PASS_SCOPED | PASS_SCOPED | 1.6970× |
| smoke_scatter_g0 | PASS_SCOPED | PASS_SCOPED | PASS_SCOPED | 1.5244× |

분산비는 최고SPP에서 각 renderer의 진공으로 정규화한 표본 Y 분산입니다. 동일 노이즈를 요구하거나 GPU 속도비로 해석하지 않습니다. 평균은 고정 영역별95% 구간이 실용 오차 밴드 안에 있는지, 분산은 독립 seed를 재표집해 감소하는지 검사했습니다. 흡수는 tent-filtered trilinear CPU 적분 oracle 및 보수적인 수치 오차 범위와 비교했습니다. 산란의 평균 밴드는 max(0.003, reference 영역 평균의2%)입니다. 네 seed에 근거한 비동시 영역 구간이라는 한계는 각 summary에 남겼습니다.

## 남은 경계 결함

[실패 위치와 실제 광선 진단](stage2-boundary/boundary_tangent_defect/README.md): 상자 모서리를 정확히 스친 광선에서 EXIT가 첫 교차로 반환됩니다. 빈 air set에서 exit 제거가 실패해 해당 path가 검게 종료됩니다. 상수 진공4096SPP의15개 pixel/seed에서 각1 sample 손실이 확인됐습니다. 평균값이나 medium/vacuum 비만으로 이 결함을 통과시키지 않았습니다.

출력 폴더 안에서만 두 수정안을 시험했습니다. 첫 query는 후보를 못 찾았고, 두 번째는 원래 접촉을 복구했지만 양수 길이2.65e-7인 인접 광선도 접촉으로 오인했습니다. **두 안 모두 production에 적용하지 않았습니다.** 상태 guard는 유지했고 원래 shader SHA로 복원했습니다. 실제 짧은 통과와 접촉을 구별하는 대칭적인 경계 처리, entry-first 경우와 shadow 일관성 검증이 다음 작업입니다.

## 준비·측정상 수정과 한계

- 최초64×64 요청은 엔진에서120×64로 나와 비교에서 제외하고 원본을 보존했습니다. 카메라·필터·밀도·광학 설정은 유지한128×128 별도 cohort를 실제 EXR/metadata로 검증했습니다.
- PBRT의 spectrum L 진폭이 photometric 정규화에서 사라지는 점을 반영해 unit flat-spectrum shape와 명시 light scale(환경0.05, 구면8)을 사용했습니다. 잘못된 v1 레퍼런스는 보존·제외했습니다. 렌더된 medium 이미지에 밝기를 맞춰 보정하지 않았습니다.
- 분리 실행 계획에서 PrimaryMediumQueryCS의 outer guard가 누락됐습니다. 기존 사전 frozen snapshot·각 child launch·현재 SHA 일치를 별도 증거로 확인했습니다. 과거 guard가 실행됐다고 바꾸지 않았고 향후 생성기를 수정했습니다.
- [시간 기록](stage2-timings.md): engine 누적 render/readback과 PBRT whole-process는 다른 구간입니다. 엔진 일부 실행은 마지막 저장 뒤 약109초의 종료 지연이 있으며 세부 함수 원인은 미계측입니다. Stage4의 격리된 성능·정상성 판정은 아직 하지 않았습니다.
- 엔진 production, 기존 normal-map/Raycone 정책은 이번 Stage2에서 수정하지 않았습니다. validation tooling/reference/진단 변경과 원인·원리·거부한 대안은 프로젝트 내부의 튜터용 ownership 가이드에 기록했으며 학습 상태는 UNASSESSED입니다.

## 비교 이미지

- [gallery_medium_stage2_constant_absorption_128](stage2-absorption/gallery_medium_stage2_constant_absorption_128/README.md)
- [gallery_medium_stage2_ramp_absorption](stage2-absorption/gallery_medium_stage2_ramp_absorption/README.md)
- [gallery_medium_stage2_constant_scatter_g0](stage2-scattering/gallery_medium_stage2_constant_scatter_g0/README.md)
- [gallery_medium_stage2_ramp_scatter_gp05](stage2-scattering/gallery_medium_stage2_ramp_scatter_gp05/README.md)
- [gallery_medium_stage2_ramp_scatter_gm05](stage2-scattering/gallery_medium_stage2_ramp_scatter_gm05/README.md)
- [gallery_medium_stage2_smoke_scatter_g0](stage2-scattering/gallery_medium_stage2_smoke_scatter_g0/README.md)
- [boundary_tangent_defect](stage2-boundary/boundary_tangent_defect/README.md)

기존309개 승인 이미지와24개 superseded baseline은 보존했습니다. 이번 이미지는 통과하지 않은 결과도 모두 게시하며 새 사용자 육안 확인을 자동으로 부여하지 않습니다.
