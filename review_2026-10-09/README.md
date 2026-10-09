# PathTracing 검증 · 2026-10-09

기존 **309개 이미지(277 + 구형 광원 수정 32)**는 정확한 SHA 기준으로 사용자 육안 승인을 받았습니다. 이전의 superseded baseline 24개는 미승인 상태로 보존합니다. 새 Stage 2 38개 이미지는 육안 확인 대기입니다.

육안 승인과 수치·소스 계약 판정은 별개입니다. 모든 historical failure/OPEN/INCONCLUSIVE와 시간 측정 범위를 유지합니다.

[전체 이미지/판정 색인](INDEX.md)

| Stage 2 scene | Radiance | Variance | 사용자 육안 |
|---|---|---|---|
| [gallery_medium_stage2_constant_absorption_128](stage2-absorption/gallery_medium_stage2_constant_absorption_128/README.md) | INCONCLUSIVE | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_medium_stage2_ramp_absorption](stage2-absorption/gallery_medium_stage2_ramp_absorption/README.md) | INCONCLUSIVE | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_medium_stage2_constant_scatter_g0](stage2-scattering/gallery_medium_stage2_constant_scatter_g0/README.md) | INCONCLUSIVE | PASS_SCOPED | 대기 |
| [gallery_medium_stage2_ramp_scatter_gp05](stage2-scattering/gallery_medium_stage2_ramp_scatter_gp05/README.md) | INCONCLUSIVE | PASS_SCOPED | 대기 |
| [gallery_medium_stage2_ramp_scatter_gm05](stage2-scattering/gallery_medium_stage2_ramp_scatter_gm05/README.md) | INCONCLUSIVE | PASS_SCOPED | 대기 |
| [gallery_medium_stage2_smoke_scatter_g0](stage2-scattering/gallery_medium_stage2_smoke_scatter_g0/README.md) | INCONCLUSIVE | PASS_SCOPED | 대기 |
| [boundary_tangent_defect](stage2-boundary/boundary_tangent_defect/README.md) | FAIL_DETERMINISTIC_VACUUM | NOT_A_STOCHASTIC_VARIANCE_FAILURE | 대기 |

[stage2-report.md](stage2-report.md)

[stage2-summary.json](stage2-summary.json)

[stage2-timings.md](stage2-timings.md)

[stage2-timings.json](stage2-timings.json)

[stage2-engine-time-audit.md](stage2-engine-time-audit.md)

[stage2-engine-time-audit.json](stage2-engine-time-audit.json)

[stage2-engine-depth32-plan.json](stage2-engine-depth32-plan.json)

[stage2-reference-depth32-plan.json](stage2-reference-depth32-plan.json)

## 이전 Stage 1 게시 설명 (당시 상태를 보존한 기록)

# PathTracing 회귀 검증 · 2026-10-09

**기존 277개 이미지는 사용자 육안 승인을 받았습니다. 수치상 미통과·불확실 판정은 그대로 보존하며, 새 Shaderball/Killeroo 이미지는 이 승인에 포함되지 않습니다.**

레퍼런스 지정: **Mitsuba gallery → Mitsuba renderer**, **N-layered → Guo renderer**. 앞서 생성한 PBRT gallery/layered 비교는 보조 진단으로 분리했습니다.

Guo의 독립 seed·film·카메라를 검증한 비교를 아래 N-layered 목록에 추가했습니다. 사용자의 후속 요청에 따라 기존 Shaderball N3와 Killeroo N4 대표 씬을 추가 검증합니다.

## 최신: 구형 광원 MIS 누락 수정

광원 선택 후보가 빠진 문제가 아니라 BSDF/phase 광선의 구형 발광체 도달 기여가 빠진 문제였습니다. PBRT와 Guo 모두 이 보완 항을 포함합니다. 기존 PMF·power heuristic 정책은 유지하고 도달·차폐·매질 endpoint를 복구했습니다. [소스 비교와 수정 범위](sphere-fix-source_policy_audit.md).

| Scene / 이미지 종류 | 분산 수렴 | 사용자 육안 확인 |
|---|---|---|
| [gallery_nlayer_killeroo_n4_alpha01_bright10x (수정 전후 반복 검증)](sphere-mis-fixed-regression/gallery_nlayer_killeroo_n4_alpha01_bright10x/README.md) | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_nlayer_shaderball_n3 (수정 전후 반복 검증)](sphere-mis-fixed-regression/gallery_nlayer_shaderball_n3/README.md) | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_nlayer_killeroo_n4_alpha01_bright10x (1024 SPP 대표)](sphere-mis-fixed-hero/gallery_nlayer_killeroo_n4_alpha01_bright10x/README.md) | NOT_APPLICABLE_SINGLE_SEED | 대기 |
| [lambertian_sphere_scalar (PBRT 해석해 대조)](sphere-mis-pbrt-control/lambertian_sphere_scalar/README.md) | OBSERVED_DECREASE_FOUR_SEEDS | 대기 |

Killeroo의 vertex normal/BSDF/filter 차이와 남은 radiance 잔차는 각 비교에 공개합니다. 수정 전 baseline과 이전 사용자 승인 범위는 유지합니다. [실행 시간 기록](sphere-fix-timings.md).

## 수정 전 baseline: Shaderball · Killeroo

| Scene / 이미지 종류 | 밝기·씬 매칭 판정 | 분산 수렴 | 사용자 육안 확인 |
|---|---|---|---|
| [gallery_nlayer_shaderball_n3 (반복 검증)](layered-showcase-regression/gallery_nlayer_shaderball_n3/README.md) | OPEN: 작은 밝기 잔차 | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_nlayer_shaderball_n3 (1024 SPP 대표)](layered-showcase-hero/gallery_nlayer_shaderball_n3/README.md) | OPEN: 작은 밝기 잔차 | NOT_APPLICABLE_SINGLE_SEED | 대기 |
| [gallery_nlayer_killeroo_n4_alpha01_bright10x (반복 검증)](layered-showcase-regression/gallery_nlayer_killeroo_n4_alpha01_bright10x/README.md) | 미통과: 구형 광원·노멀 | PASS_VARIANCE_DECREASE_ONLY | 대기 |
| [gallery_nlayer_killeroo_n4_alpha01_bright10x (1024 SPP 대표)](layered-showcase-hero/gallery_nlayer_killeroo_n4_alpha01_bright10x/README.md) | 미통과: 구형 광원·노멀 | NOT_APPLICABLE_SINGLE_SEED | 대기 |

새 비교의 수치 결과·씬 매칭 한계는 각 링크에 명시했습니다. 아래 기존 277개 이미지의 육안 승인은 새 이미지에 자동 적용하지 않습니다.

[N3/N4 소스 계약 감사](nlayer-source-contract.md): Killeroo의 구형 광원 MIS 기여 누락과 노멀 처리 차이, 정확한 수정에 필요한 범위를 기록했습니다. [추가 렌더 시간](nlayer-timings.md)도 원래 측정 범위를 구분해 기록했습니다.

## 엔진 수정 후 새 렌더

Production 수정: 환경맵의 U 반복/V 극점 clamp, generic conductor의 Fresnel 외부 반사율 배율. 실제 GPU의 수정 전 실패와 수정 후 통과를 확인했습니다. 아래는 공통 셰이더 적용 시점의 결과이며, White 불투명도 후속 수정은 별도 링크를 보세요.

| Scene | 평균 Y 차이 (128 SPP) | 기존 SSIM .99 검사 | 분산 수렴 |
|---|---:|---|---|
| [complex_room_envmap_mis](engine-surface-fixes/complex_room_envmap_mis/README.md) | +0.028259% | 0.997503 / PASSED | PASS |
| [gallery_breakfast_room](engine-surface-fixes/gallery_breakfast_room/README.md) | +0.035808% | 0.997565 / PASSED | INCONCLUSIVE |
| [gallery_grey_white_room](engine-surface-fixes/gallery_grey_white_room/README.md) | +0.419345% | 0.998534 / PASSED | PASS |
| [gallery_white_room](engine-surface-fixes/gallery_white_room/README.md) | -0.172790% | 0.989376 / FAILED | PASS |

환경맵 CDF 생성의 수평 이웃도 U 반복에 맞췄습니다. 희소 seam fixture의 기존 proposal이 만드는 큰 분산은 새 baker로 줄였고, 실제 GPU support/MIS 검사를 통과했습니다. 기존 네 자연 환경맵의 cache는 양의 sampling support를 유지하므로 변경하지 않았으며, 위 이미지는 그 동일 cache로 비교했습니다.

유리잔 금속 반사율 수정: 평균 Y +4.7600% → +0.9104%, SSIM 0.991912 / PASSED, 분산 PASS. [전후 이미지와 노이즈](glass-conductor-fixed/gallery_glass_of_water/README.md).

White 불투명도 수정 / 동일 volpath reference: 평균 Y -0.1596% → -0.1818%, SSIM 0.989897 / FAILED, 분산 PASS. [최신 전후 이미지·노이즈](white-scalar-mask-volpath/gallery_white_room/README.md).

White 불투명도 수정 / 보존한 원본 path reference: 평균 Y -0.1728% → -0.1950%, SSIM 0.989367 / FAILED. [별도 대조군](white-scalar-mask-path/gallery_white_room/README.md).

White 바닥 분포만 맞춘 진단: 동일 engine 대비 native Y -0.1818% / GGX control Y +0.1433%, control SSIM 0.990240 / PASSED. [원인 분리 이미지](white-floor-distribution-control/gallery_white_room/README.md). 원본 gallery의 판정은 유지합니다.

## 최신 수정 및 진단

- [Many-lights: 발광체 차폐 조건 수정 전후](corrected-many-lights/cornell_many_lights_matched/README.md) — PBRT 평균 Y −0.0147%, 기존 SSIM 기준·분산 수렴 PASS.
- [Many-lights: Mitsuba spot 감쇠식 교정 전후](corrected-many-lights-spot/cornell_many_lights_matched/README.md) — 평균 Y +0.4742% → +0.0192%; 노이즈 차이는 별도 표시.
- [Normal-map: 필터링 원인 대조](normal-map-filter-diagnostic/cornell_normal_map/README.md) — Mitsuba 대비 평균 Y +2.4576% → +0.0758%. normal 샘플링만 LOD0로 바꾼 진단이며 전체 PASS는 아닙니다. 사용자가 현 ray-cone 설정을 유지하고 다음 검증으로 진행하도록 승인했습니다.
- [N-layered: 최종 엔진 / 지정 Guo 레퍼런스](layered-guo-surface-fixes/gallery_nlayer_sphere_alpha01_n2/README.md) — 평균 Y +0.04379%, 양쪽 분산 수렴 PASS.

아래 7개 RGB 보충 비교는 기존 전체 영상 SSIM .99 기준과 양쪽 분산 감소 검사를 통과했습니다. 재질 모델의 정확한 동등성 및 전체 단계 승인을 뜻하지 않습니다.

| Scene | 평균 Y 차이 (128 SPP × 4) | 기존 SSIM .99 검사 |
|---|---:|---|
| [complex_room_envmap_mis](remaining-native-rgb/complex_room_envmap_mis/README.md) | +0.028259% | 0.997503 · PASSED |
| [cornell_anisotropic_conductor](remaining-native-rgb/cornell_anisotropic_conductor/README.md) | +1.434576% | 0.999186 · PASSED |
| [cornell_material_maps](remaining-native-rgb/cornell_material_maps/README.md) | +0.562703% | 0.999757 · PASSED |
| [cornell_box_dielectric](remaining-native-rgb/cornell_box_dielectric/README.md) | -0.002772% | 0.998914 · PASSED |
| [module5d3_water_glass](remaining-native-rgb/module5d3_water_glass/README.md) | -0.056473% | 0.994014 · PASSED |
| [module5d3_camera_inside_water](remaining-native-rgb/module5d3_camera_inside_water/README.md) | -0.004007% | 0.999580 · PASSED |
| [module5d3_thin_neutrality](remaining-native-rgb/module5d3_thin_neutrality/README.md) | -0.069790% | 0.996879 · PASSED |

## 원본 Mitsuba gallery 재검증

| Scene | Reference | Variance | Radiance |
|---|---|---|---|
| [gallery_breakfast_room](gallery-mitsuba/gallery_breakfast_room/README.md) | Mitsuba | INCONCLUSIVE | OPEN |
| [gallery_glass_of_water](gallery-mitsuba/gallery_glass_of_water/README.md) | Mitsuba | PASS | OPEN |
| [gallery_grey_white_room](gallery-mitsuba/gallery_grey_white_room/README.md) | Mitsuba | PASS | OPEN |
| [gallery_white_room](gallery-mitsuba/gallery_white_room/README.md) | Mitsuba | PASS | OPEN |

## N-layered: Guo renderer

[최종 N2 이미지·중앙 표면 ROI·독립 seed 노이즈 비교](layered-guo-surface-fixes/gallery_nlayer_sphere_alpha01_n2/README.md) · [수정 전 엔진 baseline](layered-guo/gallery_nlayer_sphere_alpha01_n2/README.md)

128 SPP 평균 Y 차이 +0.04379%; 테스트한 RGB/Y 평균 차이 구간은 모두 0을 포함합니다. 양쪽 분산 수렴 PASS. 이 기존 N2 비교 이미지는 사용자 육안 승인을 받았으며 새 대표 씬 검증과 구분합니다.

## 다른 회귀 및 보충 비교

| Scene | Reference | Status |
|---|---|---|
| [cornell_box (feature-regressions)](feature-regressions/cornell_box/README.md) | PBRT / Mitsuba | Radiance OPEN |
| [cornell_directional_light (feature-regressions)](feature-regressions/cornell_directional_light/README.md) | PBRT | Radiance OPEN |
| [cornell_many_lights (feature-regressions)](feature-regressions/cornell_many_lights/README.md) | PBRT | Radiance OPEN |
| [complex_room_envmap_mis (feature-regressions)](feature-regressions/complex_room_envmap_mis/README.md) | PBRT | Radiance OPEN |
| [cornell_anisotropic_conductor (feature-regressions)](feature-regressions/cornell_anisotropic_conductor/README.md) | PBRT | Radiance OPEN |
| [cornell_box_dielectric (feature-regressions)](feature-regressions/cornell_box_dielectric/README.md) | PBRT | Radiance OPEN |
| [module5d3_water_glass (feature-regressions)](feature-regressions/module5d3_water_glass/README.md) | PBRT | Radiance OPEN |
| [module5d3_camera_inside_water (feature-regressions)](feature-regressions/module5d3_camera_inside_water/README.md) | PBRT | Radiance OPEN |
| [module5d3_thin_neutrality (feature-regressions)](feature-regressions/module5d3_thin_neutrality/README.md) | PBRT | Radiance OPEN |
| [cornell_material_maps (feature-regressions)](feature-regressions/cornell_material_maps/README.md) | PBRT | Radiance OPEN |
| [cornell_normal_map (feature-regressions)](feature-regressions/cornell_normal_map/README.md) | PBRT | Radiance OPEN |
| [cornell_directional_light (rgb-supplements)](rgb-supplements/cornell_directional_light/README.md) | Mitsuba RGB | Radiance OPEN |
| [cornell_principled_sheen (rgb-supplements)](rgb-supplements/cornell_principled_sheen/README.md) | Mitsuba RGB | Radiance OPEN |
| [cornell_normal_map (normal-map-rgb)](normal-map-rgb/cornell_normal_map/README.md) | Mitsuba RGB | Radiance OPEN |
| [cornell_normal_map (normal-map-filter-diagnostic)](normal-map-filter-diagnostic/cornell_normal_map/README.md) | Mitsuba RGB | Radiance OPEN |
| [cornell_many_lights_matched (corrected-many-lights)](corrected-many-lights/cornell_many_lights_matched/README.md) | PBRT primary / Mitsuba diagnostic | Radiance OPEN |
| [cornell_many_lights_matched (corrected-many-lights-spot)](corrected-many-lights-spot/cornell_many_lights_matched/README.md) | PBRT primary / Mitsuba cosine-smoothstep spot | Radiance OPEN |
| [complex_room_envmap_mis (remaining-native-rgb)](remaining-native-rgb/complex_room_envmap_mis/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [cornell_anisotropic_conductor (remaining-native-rgb)](remaining-native-rgb/cornell_anisotropic_conductor/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [cornell_material_maps (remaining-native-rgb)](remaining-native-rgb/cornell_material_maps/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [cornell_box_dielectric (remaining-native-rgb)](remaining-native-rgb/cornell_box_dielectric/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [module5d3_water_glass (remaining-native-rgb)](remaining-native-rgb/module5d3_water_glass/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [module5d3_camera_inside_water (remaining-native-rgb)](remaining-native-rgb/module5d3_camera_inside_water/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |
| [module5d3_thin_neutrality (remaining-native-rgb)](remaining-native-rgb/module5d3_thin_neutrality/README.md) | Mitsuba 3.9.1 RGB | Radiance OPEN |

## 과거 PBRT 보조 진단

아래 자료는 지정된 Mitsuba/Guo 검증의 통과 근거가 아닙니다.

- [gallery_breakfast_room (analysis_rng_fixed_12seeds)](historical-pbrt-gallery/gallery_breakfast_room/README.md)
- [gallery_glass_of_water (analysis_rng_fixed)](historical-pbrt-gallery/gallery_glass_of_water/README.md)
- [gallery_grey_white_room (analysis_rng_fixed)](historical-pbrt-gallery/gallery_grey_white_room/README.md)
- [gallery_white_room (analysis_rng_fixed_spf1)](historical-pbrt-gallery/gallery_white_room/README.md)
- [gallery_nlayer_sphere_alpha01_n2--analysis_feature_rng_fixed (analysis_feature_rng_fixed)](secondary-pbrt-layered/gallery_nlayer_sphere_alpha01_n2--analysis_feature_rng_fixed/README.md)
- [gallery_nlayer_sphere_alpha01_n2_constant_env_matched--analysis_constant_control (analysis_constant_control)](secondary-pbrt-layered/gallery_nlayer_sphere_alpha01_n2_constant_env_matched--analysis_constant_control/README.md)
- [gallery_nlayer_sphere_alpha01_n2_constant_env_matched--analysis_internal256_diagnostic (analysis_internal256_diagnostic)](secondary-pbrt-layered/gallery_nlayer_sphere_alpha01_n2_constant_env_matched--analysis_internal256_diagnostic/README.md)

## 판정과 시간 기록

분산 PASS는 표본 수 증가에 따른 관측 분산 감소 검사입니다. radiance 또는 전체 기능의 PASS를 뜻하지 않습니다. 노이즈 차이를 숨기기 위한 outlier 제거, renderer 간 평균 혼합, 임의 exposure 보정은 하지 않았습니다.

시간은 engine 누적 checkpoint 시간, PBRT 전체 process 시간, Mitsuba 동기화 render 호출 시간으로 각각 기록했습니다. 서로 다른 측정 범위와 동시 작업이 있으므로 이번 값으로 renderer 간 속도나 heterogeneous medium의 성능 저하를 판정하지 않습니다.

원본 EXR·상세 실행 로그는 로컬 campaign에 보존합니다. 공개 묶음에는 비교 PNG와 SHA256 provenance만 포함합니다.

[수정 후 렌더 시간 기록](timings.md) — 측정 범위가 서로 다르며, 4단계 성능 검증을 대신하지 않습니다.
