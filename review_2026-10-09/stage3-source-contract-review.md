# Stage 3 installed source review v4

PASS_SOURCE_CONTRACT_SCOPED_NOT_IMAGE_ACCEPTANCE; stage_pass=false; formal image/radiance validation remains pending.

Four current Assets lighting_v3 sources were checked against their installed receipts and actual bytes. Pilot128²/hero512² share camera/FOV30, six PLY meshes, closed smooth dielectric host eta1.5, NanoVDB density, neutral sigma_a=.1/sigma_s=.9/g0, and source-depth32. Clear changes only medium density scale .18→0. Environment=.5; key Le16/r24 and fill Le8/r18 share positions and float32 flux conversion. PBRT tent radius.5 and engine tent sampling correspond.

The loader sets mediumMode=host to bAirContributor=false and retains the dielectric BSDF. MediumSystem attaches optical data to matching mesh draws; BuildMediumBuffers composes native worldToIndex with inverse instance transform and takes host IOR from terminal material. DXR instance IDs and terminal lookup preserve the intended mapping in source. V5 meshlet generation applies only to pure AIR boundaries and therefore does not alter these Stage3 sources.

Independent decoded host geometry: 10242 vertices/20480 triangles, closed two-incident-edge topology, outward winding, all corner normals in the geometric hemisphere; grid bbox clearance 5.98705243. Exact current source files are archived beside this report.

24 completed CPU PBRT calibration commands preserve wavelength and pixel jitter, using the same pinned executable and white RGB illuminant representation. Existing source-only calibration is retained without fitted exposure/normalization; this review did not repeat calibration statistics.

Remaining scope: actual GPU host/medium binding, rendering, independent-seed radiance/noise, depth32→64 tail, and display QA. Same authored smooth normals do not prove identical shading-normal or finite-precision traversal policies. This is a solid dielectric smoke host, not a hollow glass shell. No Stage3 image PASS is inferred.
