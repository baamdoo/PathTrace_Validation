# complex_room_envmap_mis

**Radiance: OPEN · Variance: See report · 사용자 육안 확인: 대기**

Reference: PBRT

현재 엔진 회귀 비교입니다. 분산 감소와 radiance 일치는 별도입니다. many-lights의 광원 차폐 조건과 normal-map의 필터링 조건은 수정/추적 중입니다.

평균 영상은 여러 seed의 평균이며 단일 렌더와 구분했습니다. 노이즈 영상은 seed 간 분산입니다. 노이즈 비율 1은 통과 기준이 아닙니다. 색상 스케일·SPP·seed 수는 각 그림에 표시됩니다.

## comparison_spp_16.png

![comparison_spp_16.png](comparison_spp_16.png)

## comparison_spp_64.png

![comparison_spp_64.png](comparison_spp_64.png)

## comparison_spp_128.png

![comparison_spp_128.png](comparison_spp_128.png)

## replicate_noise_stddev_128.png

![replicate_noise_stddev_128.png](replicate_noise_stddev_128.png)

## replicate_noise_variance_128.png

![replicate_noise_variance_128.png](replicate_noise_variance_128.png)

## replicate_noise_convergence.png

![replicate_noise_convergence.png](replicate_noise_convergence.png)
