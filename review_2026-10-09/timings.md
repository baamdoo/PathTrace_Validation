# 수정 후 렌더 시간 기록

성능 판정용 격리 측정이 아닙니다. 서로 다른 렌더러의 시간 비율이나 heterogeneous medium 성능 저하로 해석하지 않습니다.

| Scene | Renderer / batch | SPP | Seeds | Median (s) | Min–max (s) |
|---|---|---:|---:|---:|---:|
| complex_room_envmap_mis | engine / engine_surface_fixes | 16 | 4 | 0.367 | 0.354–0.580 |
| complex_room_envmap_mis | engine / engine_surface_fixes | 64 | 4 | 1.322 | 1.301–1.551 |
| complex_room_envmap_mis | engine / engine_surface_fixes | 128 | 4 | 2.590 | 2.534–2.825 |
| gallery_breakfast_room | engine / engine_surface_fixes | 16 | 4 | 3.865 | 3.798–3.988 |
| gallery_breakfast_room | engine / engine_surface_fixes | 64 | 4 | 15.286 | 15.229–15.454 |
| gallery_breakfast_room | engine / engine_surface_fixes | 128 | 4 | 30.471 | 30.367–30.574 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference | 16 | 4 | 0.017 | 0.017–0.380 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference | 64 | 4 | 0.049 | 0.044–0.782 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference | 128 | 4 | 0.083 | 0.081–0.089 |
| gallery_glass_of_water | engine / engine_surface_fixes | 16 | 4 | 0.197 | 0.190–0.323 |
| gallery_glass_of_water | engine / engine_surface_fixes | 64 | 4 | 0.691 | 0.657–0.816 |
| gallery_glass_of_water | engine / engine_surface_fixes | 128 | 4 | 1.324 | 1.312–1.476 |
| gallery_glass_of_water | mitsuba / mitsuba_gallery_correct_reference | 16 | 4 | 0.008 | 0.008–0.200 |
| gallery_glass_of_water | mitsuba / mitsuba_gallery_correct_reference | 64 | 4 | 0.025 | 0.023–0.378 |
| gallery_glass_of_water | mitsuba / mitsuba_gallery_correct_reference | 128 | 4 | 0.045 | 0.044–0.056 |
| gallery_grey_white_room | engine / engine_surface_fixes | 16 | 4 | 7.047 | 6.959–7.192 |
| gallery_grey_white_room | engine / engine_surface_fixes | 64 | 4 | 27.918 | 27.879–28.467 |
| gallery_grey_white_room | engine / engine_surface_fixes | 128 | 4 | 55.762 | 55.755–57.082 |
| gallery_grey_white_room | mitsuba / mitsuba_gallery_correct_reference | 16 | 4 | 0.017 | 0.016–0.260 |
| gallery_grey_white_room | mitsuba / mitsuba_gallery_correct_reference | 64 | 4 | 0.052 | 0.049–0.520 |
| gallery_grey_white_room | mitsuba / mitsuba_gallery_correct_reference | 128 | 4 | 0.094 | 0.092–0.111 |
| gallery_white_room | engine / engine_surface_fixes | 16 | 4 | 14.182 | 14.087–14.286 |
| gallery_white_room | engine / engine_surface_fixes | 64 | 4 | 56.679 | 56.337–56.793 |
| gallery_white_room | engine / engine_surface_fixes | 128 | 4 | 113.178 | 112.688–113.483 |
| gallery_white_room | mitsuba / mitsuba_gallery_correct_reference | 16 | 4 | 0.021 | 0.020–0.325 |
| gallery_white_room | mitsuba / mitsuba_gallery_correct_reference | 64 | 4 | 0.058 | 0.054–0.647 |
| gallery_white_room | mitsuba / mitsuba_gallery_correct_reference | 128 | 4 | 0.099 | 0.098–0.114 |
| gallery_nlayer_sphere_alpha01_n2 | engine / engine_surface_fixes | 16 | 4 | 1.653 | 1.639–1.784 |
| gallery_nlayer_sphere_alpha01_n2 | engine / engine_surface_fixes | 64 | 4 | 6.606 | 6.538–6.697 |
| gallery_nlayer_sphere_alpha01_n2 | engine / engine_surface_fixes | 128 | 4 | 13.167 | 13.070–13.249 |
| gallery_breakfast_room | engine / engine_surface_fixes_extra_seeds | 16 | 8 | 3.661 | 3.641–3.801 |
| gallery_breakfast_room | engine / engine_surface_fixes_extra_seeds | 64 | 8 | 14.565 | 14.457–14.651 |
| gallery_breakfast_room | engine / engine_surface_fixes_extra_seeds | 128 | 8 | 29.037 | 28.990–29.195 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference_extra_seeds | 16 | 8 | 0.017 | 0.016–0.029 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference_extra_seeds | 64 | 8 | 0.046 | 0.044–0.062 |
| gallery_breakfast_room | mitsuba / mitsuba_gallery_correct_reference_extra_seeds | 128 | 8 | 0.083 | 0.081–0.089 |
| gallery_nlayer_sphere_alpha01_n2 | guo / guo_camera_matched_batch | 16 | 4 | 3.717 | 3.716–3.831 |
| gallery_nlayer_sphere_alpha01_n2 | guo / guo_camera_matched_batch | 64 | 4 | 13.690 | 13.517–14.009 |
| gallery_nlayer_sphere_alpha01_n2 | guo / guo_camera_matched_batch | 128 | 4 | 26.866 | 26.541–27.062 |

Engine: seed별 누적 checkpoint 시간(합산 금지). Mitsuba: 동기화 render 호출. Guo: 프로세스 전체 시간. 일부 CPU 분석이 동시에 진행됐습니다.

White 및 breakfast 추가 seed는 SPF1, 다른 현재 engine batch는 SPF8입니다. 독립적인 성능 검증은 4단계에서 진행합니다.
