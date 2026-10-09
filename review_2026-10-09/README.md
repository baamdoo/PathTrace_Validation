# PathTracing 회귀 검증 · 2026-10-09

**진행 중입니다. 미통과·불확실 결과도 육안 확인을 위해 게시합니다. 단계 통과 또는 사용자 승인으로 간주하지 않습니다.**

레퍼런스 지정: **Mitsuba gallery → Mitsuba renderer**, **N-layered → Guo renderer**. 앞서 생성한 PBRT gallery/layered 비교는 보조 진단으로 분리했습니다.

Guo의 독립 seed·film·카메라를 검증한 최종 비교를 아래 N-layered 목록에 추가했습니다. 다른 회귀 씬의 원인 수정과 재검증도 계속합니다.

## 엔진 수정 후 새 렌더

Production 수정: 환경맵의 U 반복/V 극점 clamp, generic conductor의 Fresnel 외부 반사율 배율. 실제 GPU의 수정 전 실패와 수정 후 통과를 확인했습니다. 아래는 공통 최종 셰이더로 새로 렌더한 완료 항목입니다.

| Scene | 평균 Y 차이 (128 SPP) | 분산 수렴 |
|---|---:|---|
| [complex_room_envmap_mis](engine-surface-fixes/complex_room_envmap_mis/README.md) | +0.028259% | PASS |
| [gallery_breakfast_room](engine-surface-fixes/gallery_breakfast_room/README.md) | +0.035808% | INCONCLUSIVE |

## 최신 수정 및 진단

- [Many-lights: 발광체 차폐 조건 수정 전후](corrected-many-lights/cornell_many_lights_matched/README.md) — PBRT 평균 Y −0.0147%, 기존 SSIM 기준·분산 수렴 PASS.
- [Many-lights: Mitsuba spot 감쇠식 교정 전후](corrected-many-lights-spot/cornell_many_lights_matched/README.md) — 평균 Y +0.4742% → +0.0192%; 노이즈 차이는 별도 표시.
- [Normal-map: 필터링 원인 대조](normal-map-filter-diagnostic/cornell_normal_map/README.md) — 평균 Y +2.4576% → +0.0758%; production 기본 필터 선택 대기.
- [N-layered: 지정 Guo 레퍼런스](layered-guo/gallery_nlayer_sphere_alpha01_n2/README.md) — 평균 Y +0.04379%, 양쪽 분산 수렴 PASS.

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

[N2 이미지·중앙 표면 ROI·독립 seed 노이즈 비교](layered-guo/gallery_nlayer_sphere_alpha01_n2/README.md)

128 SPP 평균 Y 차이 +0.04379%; 테스트한 RGB/Y 평균 차이 구간은 모두 0을 포함합니다. 양쪽 분산 수렴 PASS. 전체 단계와 사용자 육안 승인은 별도입니다.

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
