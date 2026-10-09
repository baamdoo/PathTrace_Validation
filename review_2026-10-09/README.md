# PathTracing 회귀 검증 · 2026-10-09

**진행 중입니다. 미통과·불확실 결과도 육안 확인을 위해 게시합니다. 단계 통과 또는 사용자 승인으로 간주하지 않습니다.**

레퍼런스 지정: **Mitsuba gallery → Mitsuba renderer**, **N-layered → Guo renderer**. 앞서 생성한 PBRT gallery/layered 비교는 보조 진단으로 분리했습니다.

현재 Guo의 실제 독립 seed 및 film 설정을 검증하는 별도 실행이 진행 중입니다. Guo 결과는 생성 후 이 폴더에 추가합니다. 다른 회귀 씬의 원인 수정과 재검증도 계속합니다.

## 원본 Mitsuba gallery 재검증

| Scene | Reference | Variance | Radiance |
|---|---|---|---|
| [gallery_breakfast_room](gallery-mitsuba/gallery_breakfast_room/README.md) | Mitsuba | INCONCLUSIVE | OPEN |
| [gallery_glass_of_water](gallery-mitsuba/gallery_glass_of_water/README.md) | Mitsuba | PASS | OPEN |
| [gallery_grey_white_room](gallery-mitsuba/gallery_grey_white_room/README.md) | Mitsuba | PASS | OPEN |
| [gallery_white_room](gallery-mitsuba/gallery_white_room/README.md) | Mitsuba | PASS | OPEN |

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
