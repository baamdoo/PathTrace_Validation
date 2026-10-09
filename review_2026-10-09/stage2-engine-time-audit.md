# Stage2 엔진 시간 범위 감사

**116초를 렌더 계산 시간으로 해석하면 안 됩니다.** 현재 긴 지연은 마지막 checkpoint 저장 뒤에 집중되어 있습니다. 이 기록은 기존 실행자료의 범위 감사이며 Stage4 성능 검증이 아닙니다.

| 씬 | 외부 job(s) | Application 전체(s) | seed별 마지막 metadata 합(s) | metadata 구간 밖(s) | 마지막 저장→receipt 완료, 근사(s) |
|---|---:|---:|---:|---:|---:|
| constant_scatter_g0 | 116.193 | 115.795 | 5.399 | 110.396 | 109.163 |
| constant_scatter_vacuum | 2.918 | 2.522 | 1.049 | 1.473 | 0.228 |
| ramp_absorption | 22.424 | 22.015 | 1.995 | 20.020 | 18.677 |
| ramp_absorption_vacuum | 9.714 | 9.314 | 0.832 | 8.482 | 7.228 |
| ramp_scatter_gm05 | 115.801 | 115.414 | 5.317 | 110.097 | 108.790 |
| ramp_scatter_gp05 | 40.286 | 39.921 | 4.988 | 34.934 | 33.659 |
| ramp_scatter_vacuum | 2.745 | 2.367 | 0.927 | 1.439 | 0.200 |
| smoke_scatter_g0 | 116.660 | 116.278 | 11.843 | 104.435 | 103.138 |
| smoke_scatter_vacuum | 2.826 | 2.449 | 0.961 | 1.488 | 0.195 |

외부 job 시간은 Python wrapper와 검사도 포함합니다. Application 시간은 `subprocess.run`을 감싼 실제 프로세스 시간입니다. metadata는 각 seed의 첫 누적 dispatch 직전부터 해당 EXR의 동기 readback/write 완료까지의 `steady_clock` 시간이며, 같은 seed의64→256→1024 checkpoint에서 계속 누적됩니다. 따라서 각 seed의 마지막 값만 합해야 합니다. GPU-only 값이 아닙니다.

constant scatter는 Application115.795초 중 누적 구간 합5.399초, 나머지110.396초입니다. gm05도115.414초 중5.317초, 나머지110.097초입니다. 실제 metadata 파일 last-write와 최종 receipt UTC를 대조하면 긴 차이 대부분이 마지막 저장 이후입니다. 파일 시각은 보조 chronology이고 함수별 프로파일 측정값은 아니며, receipt의 최종 UTC에는 Python 후처리도 조금 포함됩니다.

코드상 완료된 batch는 PathTracer의 추가 dispatch 없이 반환합니다(`PathTracerNode.cpp:324`). 마지막 dump 뒤 completion flag를 설정하고(`PathTracerNodeDebug.cpp:237–252`), game thread가 이를 보고 창 닫기를 요청합니다(`RayTracingApp.cpp:476–480`). 종료 시 render thread join, queue idle/flush, scene/renderer 해제가 이어집니다(`BaambooEngine.cpp:218–244`, `Dx12Renderer.cpp:73–81`, `Dx12CommandQueue.cpp:102–125`). 이 구간은 마지막 metadata 시계 밖에 있습니다.

정확히 어느 종료 함수가 약109초를 소모했는지는 현재 로그만으로 확정할 수 없습니다. stdout에 단계별 시각이나 스택 샘플이 없습니다. join/fence/리소스 해제/driver·PIX 종료 비용 등은 조사 후보이며, 측정된 원인으로 단정하지 않습니다. 반대로 초기 scene loading이나 순수 매질 셰이더 계산이 이 긴 차이 전부라는 설명은 현재 저장 시각과 맞지 않습니다.

현재 `pt_validation=false`여서 validation AOV 추가 저장과 readback ring drain은 해당 코드 경로에서 빠집니다. metadata 설명문은 validation build에도 쓰이는 공통 문구이므로 이 비용을110초의 원인으로 옮겨 적으면 안 됩니다. Python wrapper 추가분은 해당 두 씬에서 약0.4초입니다.

보고서 권장 문구: “전체 배치 경과116.19초, 네 seed의 누적 렌더·readback 측정 구간 합5.40초. 마지막 checkpoint 이후의 종료 지연을 포함하며 해당 지연의 세부 원인은 미계측. 순수 GPU 렌더 시간 또는 heterogeneous-medium 성능 저하율로 사용하지 않음.”

Stage4에서는 초기화/첫 dispatch/마지막 저장/close signal/join/각 queue flush/해제 시각을 분리하고, PathTracer dispatch GPU timestamp와 전체 경과 시간을 함께 기록해야 합니다. 동일 SPP·depth·SPF 및 격리 조건에서 비교해야 하며 이번 캠페인의 중첩 작업 시간은 관찰값으로 보존합니다.

수치·source hash·개별 metadata 근거: [stage2-engine-time-audit.json](stage2-engine-time-audit.json).
