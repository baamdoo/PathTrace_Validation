# Stage2 관찰 시간

모든 시간은 **초(s)**입니다. 128×128, seed11·29·47·83의 평균이며 비율은 **매질 평균 ÷ 대응 vacuum 평균**입니다. 각 vacuum은 같은 카메라·경계·조명을 유지하고 densityScale=0으로 둔 대조입니다. 산란 ramp의 g±0.5는 같은 vacuum을 공유합니다.

**Stage4 성능 판정이 아닙니다.** CPU 작업이 중첩됐고, 엔진과 PBRT는 측정 범위가 다릅니다. 이 표로 엔진의 저하율이 정상인지 또는 어느 렌더러가 더 효율적인지 판정하지 않습니다.

## 엔진: 최고 SPP까지의 seed별 누적 구간

초기화·종료를 제외한 누적 dispatch/readback/이전 checkpoint 저장/프레임 스케줄링 시간의 4-seed 평균입니다. GPU-only 시간이 아닙니다.

| 매질 | SPP / depth / SPF | 매질 평균(s) | vacuum 평균(s) | 관찰 비율 |
|---|---|---:|---:|---:|
| 일정 밀도 · 흡수 | 4096 / 1 / 8 | 0.3676 | 0.1972 | 1.864× |
| 비대칭 ramp · 흡수 | 4096 / 1 / 8 | 0.4987 | 0.2080 | 2.398× |
| 일정 밀도 · 산란 g=0 | 1024 / 16 / 1 | 1.3498 | 0.2624 | 5.145× |
| 비대칭 ramp · 산란 g=+0.5 | 1024 / 16 / 1 | 1.2469 | 0.2319 | 5.378× |
| 비대칭 ramp · 산란 g=−0.5 | 1024 / 16 / 1 | 1.3291 | 0.2319 | 5.732× |
| smoke · 산란 g=0 | 1024 / 16 / 1 | 2.9608 | 0.2402 | 12.326× |

## PBRT: 최고 SPP의 개별 CPU 프로세스 전체

시작·씬 로드·렌더·EXR 저장·종료를 포함한 4-seed 평균입니다. 엔진 표와 시간 범위가 같지 않습니다. CPU 8 threads 설정입니다.

| 매질 | SPP / depth | 매질 평균(s) | vacuum 평균(s) | 관찰 비율 |
|---|---|---:|---:|---:|
| 일정 밀도 · 흡수 | 4096 / 1 | 12.5729 | 14.4023 | 0.873× |
| 비대칭 ramp · 흡수 | 4096 / 1 | 12.2775 | 14.1932 | 0.865× |
| 일정 밀도 · 산란 g=0 | 1024 / 16 | 5.5101 | 3.6104 | 1.526× |
| 비대칭 ramp · 산란 g=+0.5 | 1024 / 16 | 4.3818 | 3.0595 | 1.432× |
| 비대칭 ramp · 산란 g=−0.5 | 1024 / 16 | 4.3777 | 3.0595 | 1.431× |
| smoke · 산란 g=0 | 1024 / 16 | 3.0884 | 2.3276 | 1.327× |

## 전체 cohort의 프로세스 시간

각 씬은4 seeds×3 checkpoint입니다. 엔진은 하나의 Application에서 누적하고, PBRT는 각 SPP를 새로 렌더한12개 프로세스의 합입니다. 따라서 두 열의 총 작업량도 같지 않습니다. A=흡수256/1024/4096 SPP·depth1·엔진 SPF8, S=산란64/256/1024 SPP·depth16·엔진 SPF1입니다.

| 씬 | 종류 | 엔진 Application 전체(s) | 엔진 최종 seed 구간 합(s) | PBRT12 프로세스 합(s) |
|---|---|---:|---:|---:|
| constant_absorption_128 | A | 21.623 | 1.470 | 66.239 |
| constant_vacuum_128 | A | 9.173 | 0.789 | 75.344 |
| ramp_absorption | A | 22.015 | 1.995 | 63.096 |
| ramp_absorption_vacuum | A | 9.314 | 0.832 | 72.245 |
| constant_scatter_g0 | S | 115.795 | 5.399 | 28.921 |
| constant_scatter_vacuum | S | 2.522 | 1.049 | 18.900 |
| ramp_scatter_gp05 | S | 39.921 | 4.988 | 23.026 |
| ramp_scatter_vacuum | S | 2.367 | 0.927 | 16.174 |
| ramp_scatter_gm05 | S | 115.414 | 5.317 | 23.013 |
| smoke_scatter_g0 | S | 116.278 | 11.843 | 16.415 |
| smoke_scatter_vacuum | S | 2.449 | 0.961 | 12.525 |

**후행 종료 지연:** constant scatter의 엔진 Application 전체는115.795초지만 네 seed의 최종 누적 구간 합은5.399초입니다. 마지막 metadata 저장 이후 receipt 완료까지 약109초가 남습니다. gm05와 smoke에서도 큰 후행 지연이 관찰됐으며 어느 종료 함수의 비용인지는 미계측입니다. 이 전체 프로세스 시간을 “순수 렌더116초”로 표시하면 안 됩니다. 자세한 근거는 [시간 범위 감사](stage2-engine-time-audit.md)에 보존했습니다.

각 seed의 checkpoint 시간은 누적되므로64·256·1024 값을 모두 더하지 않았습니다. 최고 SPP 값만 seed당 한 번 사용했습니다. Depth32는 별도 깊이 꼬리 진단이며 이 표에서 제외했습니다([엔진 계획](stage2-engine-depth32-plan.json), [PBRT 계획](stage2-reference-depth32-plan.json)).

원시 receipt·metadata·plan·queue의 정확한 경로와 SHA256, seed별 값은 [stage2-timings.json](stage2-timings.json)에 있습니다. 구현 소스별 소유권과 hash 연결은 프로젝트의 `Docs/Learning/implementations/reference-validation-ownership-recovery.md` 및 동명 `.sources.json`을 참조합니다. JSON에 기록된 guide hash는 후속 append 이전 snapshot으로 명시했습니다.
