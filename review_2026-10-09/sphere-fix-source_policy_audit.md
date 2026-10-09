# Analytic sphere MIS source audit

PBRT CPU `PathIntegrator`와 Guo `pathwsampler`는 NEE의 power weight에 대응하는 BSDF 경로의 emitter-hit 항을 모두 계산한다. 기존 엔진의 analytic sphere에는 NEE를 보완하는 기여가 없었으므로, 이번 변경은 유효한 정책 차이를 바꾸는 것이 아니라 누락된 기여를 복구한다.

엔진의 power CDF/선택 PMF, power heuristic, 구면 전체 면적 샘플링, 방출 radiance 변환은 유지했다. PBRT·Guo의 projected-cone 샘플링과는 분산이 다를 수 있다. PBRT VolPath의 spectral/ratio MIS까지 동일하다는 주장은 하지 않는다.

구면은 앞면만 방출하는 검은 불투명 endpoint다. 원래 ray 범위에서 후보를 먼저 구해 alpha 뒤쪽으로 건너뛰는 오류를 막고, 더 가까운 mesh/boundary와 순서를 비교한다. 매질 수송을 해당 거리까지 처리한 뒤 도달한 구면의 방출량을 더한다. 내부·뒷면과 방출량 0인 구면도 하늘 및 shadow 연결을 가린다.

새 hit PDF는 정확한 sphere index를 사용하며 NEE와 동일한 면적 PDF 및 near-field support를 사용한다. 기존 mesh 위치 매칭의 1e-3 허용범위는 analytic hit에 적용하지 않는다. 정확한 동점에서는 하나의 endpoint만 선택한다. 완전히 겹치는 LightComponent는 각 성분별 기존 NEE/MIS 쌍으로 합산한다.

현재 sphere 중심·반경을 shader 경로 길이에 포함하므로 CPU draw AABB나 light dirty mask를 확장하지 않는다. CPU/GPU 공유 구조, 노멀 보간, 재질, ray cone, 원본 씬을 바꾸지 않았다. Killeroo의 기존 노멀 차이와 sphere 이외 analytic light 문제는 별도 범위다.

독립 소스 검토에서 tiny-radius PDF 및 near-field support 불일치를 수정했고, 남은 소스 blocker는 없다. 이는 실제 GPU 검증·이미지 통과·단계 PASS를 의미하지 않는다.

| Source | SHA256 |
|---|---|
| `Assets/Shader/HLSL/Sampling.hlsli` | `de08dad2a304545fad6a770a071ef2dab9866b4153215845b709e29e9f6aed88` |
| `Assets/Shader/HLSL/PathUtils.hlsli` | `af8036b639ac21e18731645e50dbd7b38fbe95f2f7ad5ac058ae034216409d24` |
| `Assets/Shader/HLSL/PathSampling.hlsli` | `5da34870be5de0084670a3ad3401587d1802b14a39258e1526626a3e15c68d52` |
| `Assets/Shader/HLSL/PathTracerLIB.hlsl` | `61162d6e633262d91efd6891718e08bdb8d2007c92157d7da89b113b0626247e` |


근거: PBRT `cpu/integrators.cpp:665–674,764–805`, `shapes.h:293–330`; Guo `pathwsampler.cpp:173–201,222–266`, `sphere.cpp:286–304`, `shape.cpp:48–64`. 정확한 발췌와 파일 해시는 companion JSON에 보존했다.


Companion: `source_policy_audit.json`, SHA256 `260782746fb8d92e07328553a85ccca5c74a4a1fde1868c7a7dfb68bbd378559`. 수정 전후 diff: `sphere_only_source.patch`.
