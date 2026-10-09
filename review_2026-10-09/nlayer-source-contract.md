# N3 / N4 authored baseline source-contract audit

Recorded UTC: 2026-10-09T06:55:07.731668+00:00

**Source audit complete; numerical/visual acceptance remains OPEN. No stage PASS.** The authored baselines are preserved. This audit does not authorize or implement analytic-light transport expansion.

## Confirmed conclusions

1. **N4 finite-light MIS has a missing BSDF-hit complement.** Analytic sphere LightComponents supply discounted NEE, but no sphere geometry or analytic hit traversal supplies the complementary emission hit or occlusion. N3 is environment-only. The depth-1 many-lights control did not exercise this continuation issue.
2. **N4 authored normal magnitudes change interpolation.** Engine identity import retains non-unit normals; Guo normalizes vertices first. Both meshes have 16,899 vertices / 33,264 triangles, normal-length median 2937.26869 and maximum 289685.25436. Runtime GPU buffers remain uncaptured.
3. **Normal hemisphere rules differ.** Guo flipNormals negates provided vertex normals, then the actual hit flips the geometric normal toward the shading normal. The engine flips the shading normal toward the geometric normal. Each mesh has 16 triangles with mixed corner hemispheres; unitization/global flip alone is insufficient.
4. **Authored generic alpha and optical depths align at source level.** N3 interface alpha is 0.09/0.28. N4 alpha is 0.01/0.1/0.1, not 0.1 at the top merely because of its name. This does not prove identical BSDF estimator or global-depth behavior.

## Recommended scope

Publish the measured authored baseline with these limits. A separate shared smooth-normal fixture can preserve all physical triangles while unitizing and orienting per-corner smooth normals, using the identical PLY and Guo flipNormals=false. Verify corner hemispheres, nonzero interpolation and actual hit frames; disclose local shading-field edits. It has not been implemented here.

A paired emissive sphere PLY follows the existing area-light integration pattern but is only an approximation control. The 1e-3 radial hit tolerance does not equate analytic and faceted support/PDF. A convex inscribed mesh avoids ordinary pre-target intersection, but the actual 1e-4 origin-offset/grazing shadow path needs a test. Two tessellation levels and error bounds are required before interpreting it. Do not call this an exact repair.

Exact analytic emission-hit/shadow integration is a separate transport change. Existing IntersectRaySphereLight / IntersectRayAreaLight helpers have definitions only. It would require nearest-hit ordering, medium endpoints, MIS and paired-mesh double-counting tests. It is deferred, with no implementation authorized by this audit.

## Review of completed render analysis

- Accept only normal-exit completed batches with frozen source/input and output hash validation; preserve failed or incomplete attempts.
- Verify scene-specific dimensions, SPP, seeds, path-depth mapping, camera/film and reference contract; do not inherit N2 settings silently.
- Compute float64 spatial summaries per independent render first; run-level uncertainty uses independent seeds, not pixels as independent replicates.
- Equal integer seed labels in different renderers do not establish paired random draws; engine/reference CI must use the independent-renderer method.
- Use each scene-specific roi_precommit.json and validate bounds. Reject N2 sphere/central ROI hardcoded coordinates or masks inherited into shaderball/killeroo.
- Separate coherent mean residual, raw SSIM, noise/variance and convergence; report actual gates and inconclusive outcomes. Noise compatibility does not prove zero bias.
- A passing image metric cannot override known N4 source-contract failures. Keep radiance OPEN and stage_pass=false until explicitly resolved/accepted; no blanket stage PASS or fabricated visual approval.

## Frozen evidence

| Source | Lines | SHA256 |
|---|---|---|
| Projects/Application/Applications/RayTracingApp.cpp | 301–314, 936–978, 2270–2291 | ca7aef9155af504fd192c831f65b03030ef199da2ba240cb32503748e89cbcfd |
| Projects/BaambooEngine/BaambooScene/Systems/LightSystem.cpp | 218–234 | 02358fc237e2ec544a898a38ebdd67883e06b0656bd5eb475414fbd251ac770e |
| Assets/Shader/HLSL/PathTracerLIB.hlsl | 280–295, 332–349, 412–414 | b3513c25f35fa0872d081e7ee87be01a9982521fe8b291918c9f6689d782bdf4 |
| Assets/Shader/HLSL/PathSampling.hlsli | 142–146, 356–381, 448–454, 560–571, 599–607, 618–628, 689–707 | d4f3cc8ec02a7d61a87a5e10aa764f415aa8abb20a037ff08dae26a1c91d9217 |
| Assets/Shader/HLSL/Sampling.hlsli | 294–356 | 69b232a4c93c6fb70912fb4b983dc7c12a9567ba2172646da83567cc8e2dbfac |
| Assets/Shader/HLSL/PathUtils.hlsli | 8–11, 511–513, 833–850 | d4068ff7f52e46e5096bf613da08e3eaacf6697a961dc282b4a2e0314757cbc8 |
| Assets/Shader/HLSL/PathSurface.hlsli | 99–105, 252–266 | a365a472bb89f0a710e1f3da19cab8dc2927a7a741b144d6b97eb43ac7d59560 |
| Projects/BaambooEngine/BaambooScene/ModelLoader.cpp | 104–118, 220–222, 252–254 | af5e3814e946f69b2a3517578942704efa65928e2c48e5023b321f9a0a153b5b |
| Projects/ThirdParties/assimp/code/PostProcessing/PretransformVertices.cpp | 163–171, 295–298, 315–321 | d9c3e699e7cd4e24e73c2e468d17cf5b29a6ec7a6bfc688f23905a9c454161bb |
| Projects/ThirdParties/assimp/code/PostProcessing/GenVertexNormalsProcess.cpp | 104–110 | 45a6f4b8d990ca947eab8460d1c08a095b487253cc58a933f781936767ee8f05 |
| Tools/tmp/EfficientComplexLayered/src/shapes/ply.cpp | 205–211 | 6239362c31432400c7010da52706d260f7ff728b4d6f934ca27298e25fab6354 |
| Tools/tmp/EfficientComplexLayered/src/librender/trimesh.cpp | 607–629 | c5320ce8d96a84c29beca99bb8953172e2e6e7ca3624b92b6ea4f964555b39cd |
| Tools/tmp/EfficientComplexLayered/include/mitsuba/render/skdtree.h | 367–396 | 5b360af870b69224ee3e21ea230bd3208ef5552d31c8976fe5e12bfb18708b75 |
| Assets/Generated/gallery_nlayer_shaderball_n3/scene.baamboo | 1–14 | 8351df68a1828ee4e165ac7ab174fadc0fe9518618217d6d3c60afaba4867c12 |
| Assets/Generated/gallery_nlayer_shaderball_n3/reference_guo_bidir.xml | 21–59 | 1604ea74a46e8a808d95bf304a442e9c214ac156889009490ab38b14baa2c4cd |
| Assets/Generated/gallery_nlayer_killeroo_n4_alpha01_bright10x/scene.baamboo | 6–24 | 67765a8c6dbe50b47c8140ac7653fed1c4396e2d952c37eef6e64415deb285e8 |
| Tools/tmp/EfficientComplexLayered/validation/n4_killeroo_alpha01_700_bright10x.xml | 30–43, 47–88, 134–144 | 4c6aad73c1783e98610ba4152c0b3241d01a8c6d51f99a3ef15221f851770938 |

Companion JSON: source_contract_audit.json; SHA256 9ef4d601d6c23d2a8d10b1468befcc70aba8ff510f5f44338e36361c53bdf4f4. The JSON contains exact excerpts, source/asset hashes, prepared contract/ROI hashes, limits and acceptance-review checks.

No source/assets, renderer processes or external publication were changed by this audit.
