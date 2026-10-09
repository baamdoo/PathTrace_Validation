# Stage2 경계 수리와 Stage3 실제 이미지의 기술 근거

이 문서는 기술 결과를 기록합니다. 새 Stage2 진단 PNG를 추가하지 않습니다. 기존 이미지는 보존하며 Stage2 육안 검토는 사용자 요청에 따라 **면제(waived), 승인 아님**입니다. Stage3 실제 대표 이미지에는 별도 사용자 육안 검토가 필요합니다.

## v4 GPU 통과와 실제 이미지 실패

v4 focused GPU 검사는38 cases/66,323 ray-generation probes와 별도 Primary prepass5개,65 acceptance rows에서 통과했습니다. +3ULP 행1개는 사전 정의된 observation입니다. 실제 gallery는 meshlet topology 생성을 끈 상태여서 contact helper를 연결하지 못했고,24개 constant absorption/vacuum RGB가 원래 결과와 bit-identical이었습니다.4096SPP의 vacuum 손실15건도 남았습니다.

[실제 실패 receipt](stage3-context-stage2-v4-image-failure.json). GPU 국소 검사 통과가 실제 앱 수리를 대신하지 않습니다.

## v5의 좁은 loader 연결과 완료된 수치 결과

pure AIR boundary에만 auxiliary meshlet 생성을 켰고 authored index 최적화는 계속 끕니다. glass host는 이 조건에 포함되지 않습니다. 기존 두 shader bytes는 그대로 유지했습니다. 실제 vacuum12개 checkpoint는 전 pixel/RGB가1이며 원래15개 고SPP 손실 위치도 회복했습니다. 이는 actual image witness이며 GPU topology buffer 직접 readback은 아닙니다.

[앱 회복 근거](stage3-context-stage2-v5-raw-integration.json), [loader 독립 소스 검토](stage3-loader-delta-review.json).

### gallery_medium_stage2_constant_absorption_128

상태 `COMPLETE_CONSTANT_REVALIDATION_SCOPED`; radiance `PASS_SCOPED_EQUIVALENCE`; variance `PASS_VARIANCE_DECREASE_ONLY`. 전체 stage 통과나 사용자 승인으로 합치지 않습니다.

[원래 수치·구간·근거](stage3-context-stage2-report-01.json).

Matched fixed protocols and reused PBRT/oracle; full heterogeneous scattering/depth matrices still pending; no Stage3/Stage4 acceptance.

전역 잔차 `3.250097408e-06`, expanded95 `[-0.000155605009328319, 0.00016197150047347897]`. 공통 protocol의 band에 대한 결과이며 보편적인 unbiasedness 증명은 아닙니다.

### gallery_medium_stage2_ramp_absorption

상태 `COMPLETE_RAMP_REVALIDATION_SCOPED`; radiance `PASS_SCOPED_EQUIVALENCE`; variance `PASS_VARIANCE_DECREASE_ONLY`. 전체 stage 통과나 사용자 승인으로 합치지 않습니다.

[원래 수치·구간·근거](stage3-context-stage2-report-02.json).

Original numerical protocol and PBRT/oracle bytes reused; bounded application source integration checked; no automatic Stage2/3/4 acceptance.

전역 잔차 `-5.626443643e-06`, expanded95 `[-0.00018505314263461692, 0.00017529330192034287]`. 공통 protocol의 band에 대한 결과이며 보편적인 unbiasedness 증명은 아닙니다.

### Completed scoped analysis

상태 `COMPLETE_SCATTER_REVALIDATION_SCOPED`; radiance `See receipt`; variance `See receipt`. 전체 stage 통과나 사용자 승인으로 합치지 않습니다.

[원래 수치·구간·근거](stage3-context-stage2-report-03.json).

Frozen practical mean/depth equivalence and variance decrease only; unequal renderer noise is measured, not gated to equality.

## Stage3 source와 실제 RGB

동일 PLY의 IOR1.5 solid glass host 내부에 gray smoke를 두고 clear는 densityScale만0으로 바꿉니다. PBRT wavelength jitter를 켜며 source-only white calibration에서 얻은 수치로 hero를 fitting하지 않습니다.

[읽기 쉬운 source review](stage3-source-contract-review.md), [상세 해시·제한](stage3-source-contract-review.json), [고정 분석 protocol](stage3-analysis-protocol.json), [동결 입력](stage3-formal-inputs.json).

정식 base/depth/hero 계획은 생성됐습니다. 이 문서의 준비는 renderer 실행이나 이미지 수치 승인으로 간주하지 않습니다. 실제 완료 자료가 입력될 때만 builder가68EXR와 QA를 확인하고 RGB를 먼저 게시합니다. 프리뷰는 정식 통계에 합치지 않습니다.

측정된 process/checkpoint 시간 범위를 보존하며 Stage4의 고립된 성능 판정을 대신하지 않습니다.
