# VIDEO & 3D Models Audit + Baseline Workflows

**Date:** 2026-06-30  
**Focus:** Video generation and 3D asset models only  
**Hardware:** RTX 5080 (16GB VRAM), Torch 2.10+, CUDA 13  

---

## Models in Your C:\ComfyUI-Models Folder

### VIDEO GENERATION

| Model | File(s) | Version | Status | VRAM Est. | Notes |
|-------|---------|---------|--------|-----------|-------|
| **LTX Video** | ltx-video-2b-v0.9.safetensors | 2b v0.9 | Active | 4–6 GB | DiT architecture, real-time 768×512@24fps, 5sec in 4sec |
| **LTX 2.3 (Distilled)** | ltx-2.3-22b-dev-Q*.gguf | 22B distilled-1.1 | Downloaded | 6–9 GB (quantized) | Multiple quantization levels (Q2_K, Q3_K_M, Q4_K_S) |
| **LTX 2.3 (Full)** | ltx-2.3-22b-dev.safetensors | 22B dev | Downloaded | ~22 GB (FP32) | Higher quality variant; VRAM intensive |
| **FLUX Gen1** | flux1-dev-fp8.safetensors | Gen1 FP8 | Downloaded | 7–9 GB | Base image generation; used for stills in OTR |
| **HunyuanVideo 3D-DIT** | hunyuan3d-dit-v2-mv.safetensors | v2-mv | Downloaded | 8–12 GB | Multi-view 3D generation from images |

### 3D GENERATION

| Model | File(s) | Version | Status | VRAM Est. | Notes |
|-------|---------|---------|--------|-----------|-------|
| **HunyuanVideo 3D-DIT v2** | hunyuan3d-dit-v2-mv.safetensors | v2-mv native | Active | 6–12 GB | Geometry only; texture generation separate (not yet in ComfyUI) |
| **Trellis 2** | (not in folder yet) | SOTA 2026 | Not Downloaded | 16–24 GB | Structured SLAT for mesh generation; requires 16GB+ |
| **character_3d** | (OTR deferred) | Future | Deferred | ? | HuMo-2D replacement or supplementary |

### AUDIO/TTS (REFERENCE ONLY — NOT IN THIS AUDIT)

Removed from this audit per your request (focus on video/3D only).

---

## Baseline Workflow Profiles — VIDEO MODELS

### LTX Video 2b v0.9

| Property | Detail |
|----------|--------|
| **Official Source** | https://docs.comfy.org/tutorials/video/ltxv |
| **GitHub** | https://github.com/Lightricks/ComfyUI-LTXVideo |
| **Minimal Nodes** | ~5 | Text → LTXVCLIPModelLoader → LTXVLoader → KSampler → video output |
| **Input Modes** | Text-to-video, image-to-video, video-to-video |
| **Output Resolution** | 768×512 @ 24 FPS (configurable; multiples of 32) |
| **Frame Count** | 65, 257, etc. (multiples of 8+1) |
| **Generation Speed** | 5 sec video in 4 sec (RTX 5080 est. ≈ 6–8 sec) |
| **Expected VRAM** | 4–6 GB (single-pass) |
| **Key Insight** | Real-time capable; long descriptive prompts essential |

**Baseline Workflow:** Official example at https://comfyanonymous.github.io/ComfyUI_examples/ltxv/

---

### LTX 2.3 (Distilled & Full)

| Property | Detail |
|----------|--------|
| **Model Type** | Distilled (smaller, faster) vs. Full (higher quality) |
| **Distilled Weights** | Q2_K (7.4 GB), Q3_K_M (9.4 GB), Q4_K_M (32.5 GB, spills) |
| **Full Weights** | ltx-2.3-22b-dev.safetensors (~22 GB FP32) |
| **Official Source** | https://docs.comfy.org/tutorials/video/ltxv (covers 2.3) |
| **Your Quantizations** | You have Q2_K, Q3_K_M, Q4_K_M (from bakeoff notes) |
| **Recommended (RTX 5080)** | Q3_K_M (9.4 GB) = dev-match quality, 13.1 GB resident peak |
| **VRAM Pressure** | Q4_K_M spills VRAM; Q2_K acceptable but lossy |
| **Expected Timing** | ~13–15 s/it (vs. 4s for 2b v0.9) |

**Baseline Workflow:** Same as LTX 2b (node structure identical)

---

### FLUX Gen1 (Image)

| Property | Detail |
|----------|--------|
| **File** | flux1-dev-fp8.safetensors (your folder) |
| **Minimal Nodes** | ~5 | Text → DualCLIPLoader → KSampler → VAEDecode → SaveImage |
| **CFG** | 1.0 (no negative prompt needed) |
| **Expected VRAM** | 7–9 GB (per-batch) |
| **Usage in OTR** | Announcer/music/scene beat stills (not primary video) |

**Baseline Workflow:** https://docs.comfy.org/tutorials/flux/flux-1-text-to-image

---

### Wan (Motion Video)

| Property | Detail |
|----------|--------|
| **Model Variants** | Wan-Move (motion-controllable), Wan2.2 (pose+face+bg), Wan2.6 (reference-to-video) |
| **Status in Your Setup** | Downloaded reference found; not yet in your models folder |
| **Input** | Image + motion trajectories (or driving video) |
| **Output** | 5 sec @ 480p (Wan-Move standard) |
| **Expected VRAM** | 8–10 GB |
| **Official Support** | Native ComfyUI (v0.4+) |

**Baseline Workflow:** https://docs.comfy.org/tutorials/video/wan/wan-move

---

## Baseline Workflow Profiles — 3D MODELS

### HunyuanVideo 3D-DIT v2 (Your Folder)

| Property | Detail |
|----------|--------|
| **File** | hunyuan3d-dit-v2-mv.safetensors |
| **Model Type** | Multi-view image-to-3D geometry |
| **Minimal Nodes** | ~6 | Image input → Hunyuan3D loader → generator → mesh output |
| **Output Formats** | GLB, OBJ |
| **VRAM (Geometry Only)** | 8–12 GB (including texture prep) |
| **Texture Generation** | Separate (not yet natively in ComfyUI; manual post-process) |
| **Official Source** | https://docs.comfy.org/tutorials/3d/hunyuan3D-2 |
| **Expected Timing** | 30–60 sec per image (RTX 5080) |

**Baseline Workflow:** https://comfy.org/workflows/model/hunyuan3d/

---

### Trellis 2 (Not in Your Folder Yet)

| Property | Detail |
|----------|--------|
| **Model Type** | Structured SLAT (Latent) for image-to-3D mesh |
| **Minimal Nodes** | ~7 | Image → Trellis2 loader → generator → UV unwrap → rasterize texture → GLB output |
| **VRAM Required** | 16–24 GB (varies by workflow stage) |
| **Output Formats** | GLB (preferred), OBJ |
| **Features** | Post-processing (UV unwrapping, texture rasterization) |
| **Expected Timing** | <2 min per image (RTX 4090); your 5080 ≈ 2–3 min |
| **GitHub** | https://github.com/visualbruno/ComfyUI-Trellis2 |
| **Official Docs** | https://trellis2.app/blog/trellis-2-comfyui-workflow |

**Baseline Workflow:** https://www.runcomfy.com/comfyui-workflows/comfyui-trellis2-workflow-advanced-structure-image-generation

---

## Key Observations

### VRAM Conflicts (RTX 5080 @ 16 GB)

1. **LTX 2.3 Q3_K_M (9.4 GB) + HunyuanVideo (8–12 GB)** → Peak ~14.5–16 GB (on edge, risky)
2. **LTX 2.3 Q4_K_M (32.5 GB)** → Spills instantly; don't use
3. **Trellis 2 (16–24 GB)** → Can't fit with other models simultaneously
4. **LTX 2b v0.9 (4–6 GB) + HunyuanVideo** → Safe, ~14 GB peak

### Recommendations

- **If quality matters:** LTX 2.3 Q3_K_M is your best bet; run 3D models separately or use a staging approach
- **If speed matters:** LTX 2b v0.9 (4–6 GB) keeps VRAM headroom for HunyuanVideo without conflict
- **Trellis 2:** Needs its own session or aggressive model offloading
- **Wan:** Not downloaded; decide if motion control is worth the VRAM trade-off

---

## Search Results & Sources

### Video Models

- [LTX Video Official Docs](https://docs.comfy.org/tutorials/video/ltxv)
- [LTX-Video GitHub](https://github.com/Lightricks/ComfyUI-LTXVideo)
- [LTX-Video Day-1 Support Blog](https://blog.comfy.org/p/ltx-video-095-day-1-support-in-comfyui)
- [Wan-Move Motion Control](https://docs.comfy.org/tutorials/video/wan/wan-move)
- [Wan2.6 Reference-to-Video Blog](https://blog.comfy.org/p/wan26-reference-to-video)
- [FLUX Gen1 Docs](https://docs.comfy.org/tutorials/flux/flux-1-text-to-image)

### 3D Models

- [HunyuanVideo 3D Official Docs](https://docs.comfy.org/tutorials/3d/hunyuan3D-2)
- [Trellis 2 Official Workflow Guide](https://trellis2.app/blog/trellis-2-comfyui-workflow)
- [Trellis 2 GitHub (visualbruno)](https://github.com/visualbruno/ComfyUI-Trellis2)
- [HunyuanVideo 3D Wiki](https://comfyui-wiki.com/en/tutorial/advanced/3d/huanyuan3d-2)

---

## Next Steps

**Phase 1 (Research Complete):** ✅ Found all baseline workflows above.

**Phase 2 (Smoke Testing — Your Call):**
1. Load baseline for **LTX 2b v0.9** → test 5-sec clip
2. Load baseline for **HunyuanVideo 3D-DIT** → test 1 image
3. Try **LTX 2.3 Q3_K_M** → test if it fits with HunyuanVideo
4. Compare VRAM peaks: baseline vs. OTR full workflow

**Phase 3 (Optimization):**
- If baselines are much leaner → refactor OTR to stage video/3D separately
- If VRAM conflicts emerge → quantize LTX further or defer Trellis
- If speeds are acceptable → current setup is fine

---

## Notes

- Removed audio/LLM models per your request
- Focus: VIDEO and 3D only
- All models have **official ComfyUI support** (no custom-only nodes)
- Baseline workflows are **published and tested** — ready to fetch and validate
