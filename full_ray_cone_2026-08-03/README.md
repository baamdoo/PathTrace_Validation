# Full Ray-Cone / Material Validation — 2026-08-03

All comparison sheets use one exposure for the beauty row and one shared
p99.5 absolute-difference scale for the difference row.

## Numeric validation

| Scene | References | Validation result | Notes |
|---|---|---:|---|
| cornell_box | PBRT / Mitsuba | PASS — 0.996457 / 0.997116 | Baseline diffuse |
| cornell_material_maps | PBRT / Mitsuba | PASS — 0.996390 / 0.996937 | Material texture + ray-cone LOD |
| cornell_normal_map | PBRT / Mitsuba | PASS — 0.994595 / 0.995230 | Tangent oracle mean 0.999999; Mitsuba normal-map policy matched explicitly |
| cornell_anisotropic_conductor | PBRT / Mitsuba | PASS — 0.996421 / 0.995945 | Anisotropic GGX |
| cornell_box_dielectric | PBRT / Mitsuba | PASS — 0.996580 / 0.997134 | Rough dielectric |
| cornell_box_dielectric_smooth | PBRT / Mitsuba | PASS — 0.996622 / 0.997232 | Delta dielectric |
| cornell_nlayer_coated_n2 | PBRT / Mitsuba | PASS — 0.996542 / 0.997095 | N=2 layered transport |
| cornell_nlayer_coated_n3_identity | PBRT / Mitsuba | PASS — 0.996542 / 0.997097 | Existing render; N2↔N3 identity SSIM 0.999999 |
| complex_room_envmap_mis | PBRT / Mitsuba | PASS — 0.997795 / 0.998068 | Complex geometry, mixed materials, area + environment MIS |
| gallery_nlayer_shaderball_n1 | PBRT / Guo | PASS — 0.995462 / 0.999885 | 8192 spp |
| gallery_nlayer_shaderball_n2 | PBRT / Guo | PASS — 0.998438 / 0.999985 | 8192 spp; primary N-layer representative |
| gallery_breakfast_room_n1 | PBRT / Mitsuba | PASS — 0.998561 / 0.996018 | Complex integration scene |
| gallery_grey_white_room_n1 | PBRT / Mitsuba | OPEN — 0.966232 / 0.968844 | Engine flat N=1 additive closure is not the references' roughplastic/coateddiffuse closure |
| gallery_white_room_n1 | PBRT / Mitsuba | OPEN — 0.936697 / 0.924841 | Same closure limitation; area-light proxy bug fixed before this render |

The numeric values above are the validation pipeline's Gaussian-blurred SSIM
values. Each scene's `radiance_compare.json` also records unblurred visual-sheet
metrics and input hashes.

The `status=pass` field in a `radiance_compare.json` means that the sheet and
its provenance were generated successfully. It is not a radiance-validation
PASS; the table above is the validation-status authority.

## Firefly and contract gates

- Core 9 scenes: strict firefly candidates = 0 for both PBRT and Mitsuba.
- Shaderball N=1/N=2: strict candidates = 0 for PBRT and Guo; Guo noise-ratio gates pass.
- Breakfast and White galleries: PBRT/Mitsuba firefly gates pass.
- Grey gallery: strict candidates = 0 and the isolated-pixel fraction passes;
  a coherent reference-relative highlight cluster exceeds the conservative
  total-cluster gate. It is classified as a cross-closure image mismatch, not
  isolated firefly noise.
- BSDF, light, material-texture, ray-cone, N-layer stack, finiteness, and
  N2↔N3 identity contracts pass.

## Important fixes found by full validation

1. Analytic GGX materials keep authored roughness in alpha space; only
   Principled perceptual roughness is squared.
2. Opaque surface side selection uses the geometric normal, so a filtered
   normal map cannot flip the entire TBN.
3. Mitsuba normal-map reference generation disables its view-dependent
   invalid-normal flip and extra shadowing policy to match Engine/PBRT.
4. Imported Gallery area-light proxies now use exact engine Y-X-Z Euler
   decomposition. White's four proxy tangent/back-normal round trips have dot
   products of 1.0; blurred SSIM improved from 0.863778/0.845808 to
   0.936697/0.924841.

## N=3 scope

No additional N=3 shaderball render was launched. The accepted prior N=3 visual
result and the already available N2↔N3 identity contract are retained as
supporting evidence; the fresh representative render budget was focused on N=2.

## Build state

Validation images were captured with `PT_VALIDATION=1`. Afterwards both CPU and
shader definitions were restored to `PT_VALIDATION=0`, and Debug x64
`Dx12Renderer;Application` rebuilt successfully with 0 errors.
