# Sphere-light repair: observed render times

These observations do not replace the requested isolated stage-4 medium/control comparison.

| Scene | Capture | Renderer | SPP | Mean seconds |
|---|---|---|---:|---:|
| gallery_nlayer_killeroo_n4_alpha01_bright10x | four seeds | engine_before | 128 | 75.378 |
| gallery_nlayer_killeroo_n4_alpha01_bright10x | four seeds | engine_fixed | 128 | 71.726 |
| gallery_nlayer_killeroo_n4_alpha01_bright10x | four seeds | guo_unchanged | 128 | 65.562 |
| gallery_nlayer_shaderball_n3 | four seeds | engine_before | 128 | 73.286 |
| gallery_nlayer_shaderball_n3 | four seeds | engine_fixed | 128 | 74.411 |
| gallery_nlayer_shaderball_n3 | four seeds | guo_unchanged | 128 | 138.443 |
| gallery_nlayer_killeroo_n4_alpha01_bright10x | single seed11 hero | engine_before | 1024 | 598.074 |
| gallery_nlayer_killeroo_n4_alpha01_bright10x | single seed11 hero | engine_fixed | 1024 | 579.355 |
| gallery_nlayer_killeroo_n4_alpha01_bright10x | single seed11 hero | guo_unchanged | 1024 | 319.719 |

Engine timing is cumulative to each checkpoint, including prior checkpoint I/O. Guo timing is whole process, including load/output, and historical renders were concurrent. New GPU jobs are serialized; no controlled rerun of baseline binary or Guo for this repair. No hetero-medium or cross-renderer efficiency conclusion.

Descriptive fixed/before engine ratios (not causal speed regressions):

- gallery_nlayer_killeroo_n4_alpha01_bright10x, cohort: 0.9515x.
- gallery_nlayer_shaderball_n3, cohort: 1.0153x.
- gallery_nlayer_killeroo_n4_alpha01_bright10x, hero: 0.9687x.
