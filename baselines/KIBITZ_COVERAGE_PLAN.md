# Baseline Coverage Plan: Are We Missing Any Published Simplified/Native ComfyUI Workflows That Fit a 5080 (16GB VRAM) or Less?

## Goal

Build a folder of minimal, official/native baseline ComfyUI workflow JSONs for every
video/3D generative model actually installed under `C:\ComfyUI-Models`, so we can
compare OTR's production complexity against stock minimal pipelines. Hardware
ceiling: single RTX 5080, 16GB VRAM. We want to know if we've missed any published
baseline/example workflow that would fit that budget (or a smaller footprint),
for a model that's already downloaded on disk.

## What's in `baselines/` right now

| File | Model | Node count | Source type |
|---|---|---|---|
| baseline_flux_gen1.json | FLUX.1 Schnell (fp8 checkpoint) | 10 | Official (PNG tEXt metadata, comfy.org docs) |
| baseline_ltx_2b_v0.9.json | LTX-Video 2b v0.9.5 | 13 | Official direct JSON (comfyanonymous.github.io) |
| baseline_hunyuan3d_v2_mv.json | Hunyuan3D-2 (non-multiview, mesh only) | 11 | Official (WebP EXIF metadata, docs.comfy.org) |
| baseline_wan_move.json | Wan-Move (motion-controllable I2V) | 16 | Official direct JSON (docs.comfy.org) |
| baseline_trellis2.json | TRELLIS.2 (MeshOnly variant) | 18 | Official example, REQUIRES custom node wrapper (visualbruno/ComfyUI-Trellis2) |
| baseline_humo_17b.json | HuMo 17B (audio+image+text -> video) | 20 | Official native ComfyUI template (docs.comfy.org / Comfy-Org/workflow_templates) |
| baseline_ltx2.3_dev_t2v.json | LTX 2.3 22B dev | 3 top-level (51 nested in subgraph) | Official native ComfyUI template |
| baseline_hunyuanworldmirror.json | HunyuanWorld-Mirror (image-seq -> 3D point cloud/Gaussians) | 5 | HAND-BUILT (no official JSON exists) from community wrapper's README (cedarconnor/ComfyUI-HunyuanWorld-Mirror) |

## What's actually installed on disk (`C:\ComfyUI-Models`)

Confirmed via `find` on checkpoints/, diffusion_models/, unet/, vae/, text_encoders/,
and top-level model folders:

- **checkpoints/**: flux1-dev-fp8.safetensors, hunyuan3d-dit-v2-mv.safetensors,
  ltx-2.3-22b-dev.safetensors, ltx-video-2b-v0.9.safetensors,
  stable-audio-open-1.0.safetensors, stable_audio_3_small_music.safetensors
- **diffusion_models/**: flux-2-klein-4b-Q4_K_M.gguf, HuMo-17b-Q3_K_M.gguf,
  humo_1.7B_fp16.safetensors, humo_17B_fp8_e4m3fn.safetensors,
  ltx-2.3-22b-distilled-1.1_transformer_only_fp8_scaled.safetensors,
  lumina_2_model_bf16.safetensors, Wan2.2-TI2V-5B-Q5_K_M.gguf,
  wan2.2_i2v_low_noise_14B_fp8_scaled.safetensors,
  Wan2_1-HuMo-14B_fp8_e4m3fn_scaled_KJ.safetensors,
  Wan2_1-HuMo-17B_Q5_K_M.gguf, z_image_turbo_nvfp4.safetensors
- **unet/**: ltx-2.3-22b-dev-Q3_K_M.gguf, ltx-2.3-22b-dev-Q4_K_S.gguf,
  distilled-1.1/ltx-2.3-22b-distilled-1.1-{Q2_K,Q3_K_M,Q4_K_M}.gguf
- **diffusers/**: LTX-Video (0.9.0 diffusers format), Wan2.1-I2V-1.3B,
  FLUX.1-dev-torchao-fp8, stable-diffusion-xl-1.0-inpainting-0.1
- **WorldMirror-V2/HY-WorldMirror-2.0/**: model.safetensors (HunyuanWorld-Mirror)
- **CogVideo/**: folder exists but EMPTY (no weights downloaded - not active)
- **huggingface hub cache**: also shows Lumina_Image_2.0_Repackaged, z_image_turbo,
  Kijai/LTX2.3_comfy snapshot, various LLM/TTS models (out of scope - video/3D only)
- No Trellis2/dinov3 weights found anywhere on disk (Trellis2 baseline built anyway
  per original spec, but nothing is actually installed to run it yet)

## Models loaded on disk WITHOUT a baseline yet (candidates to check)

1. **Wan2.2 TI2V-5B** (`Wan2.2-TI2V-5B-Q5_K_M.gguf`) - hybrid T2V/I2V, official docs
   say "should fit well on 8GB VRAM" - the single cheapest video model on disk.
   NOT baselined.
2. **Wan2.2 I2V 14B low-noise** (`wan2.2_i2v_low_noise_14B_fp8_scaled.safetensors`)
   - only the low-noise expert is present (no high-noise counterpart found), which
   may mean this is a partial/incomplete MoE pair, or a single-expert distilled
   variant. NOT baselined. Need to check if a matching high-noise file exists
   elsewhere or if this workflow needs adjusting for single-expert use.
3. **Wan2.1-I2V-1.3B** (diffusers format folder) - smallest Wan I2V variant,
   likely the cheapest VRAM footprint of any video model on disk. NOT baselined.
4. **LTX 2.3 distilled-1.1** (GGUF quantized: Q2_K/Q3_K_M/Q4_K_M, plus
   `ltx-2.3-22b-distilled-1.1_transformer_only_fp8_scaled.safetensors`, plus a
   matching LoRA `ltx-2.3-22b-distilled-lora-384-1.1.safetensors`) - this is a
   DIFFERENT (distilled, faster, likely lower-VRAM) workflow variant from the
   `ltx-2.3-22b-dev` baseline already built. The distilled variant is specifically
   the one docs.comfy.org flags as needing fewer steps. NOT separately baselined.
5. **flux-2-klein-4b** (`flux-2-klein-4b-Q4_K_M.gguf`) - this is FLUX.2, a newer/smaller
   FLUX variant (4B vs the 12B baselined already as flux1-dev-fp8) - image, not video/3D,
   but flagged in case scope should include it as a lighter FLUX alternative.
6. **z_image_turbo** (`z_image_turbo_nvfp4.safetensors`) - fast turbo image model,
   also out of video/3D scope but present.
7. **lumina_2_model** (`lumina_2_model_bf16.safetensors`) - Lumina Image 2.0, also
   image not video/3D.

## Open question for the panel

Given a single RTX 5080 (16GB VRAM) as the hard ceiling, and given everything
listed above is what Jeffrey has actually downloaded:

1. Are there any of the 7 "candidates" above (items 1-4 are video/3D and in
   scope; 5-7 are image and likely out of scope but flagged) that we should
   build a baseline for, given they're already on disk and may be the
   MOST 5080-appropriate (lowest VRAM) option for their model family - even
   more so than the ones already baselined?
2. For each of Wan2.2 TI2V-5B, Wan2.2 I2V-14B (single expert available),
   Wan2.1-I2V-1.3B, and LTX-2.3-distilled-1.1: does an official/native minimal
   ComfyUI workflow actually exist and get published (docs.comfy.org, GitHub,
   or the model's own HF repo), or would building one require improvisation
   like the HunyuanWorld-Mirror baseline did?
3. Is there any published, native, ComfyUI-official baseline for ANY video or
   3D generation model that would run in under ~16GB VRAM that isn't covered by
   the 8 files already in `baselines/` and isn't in the "candidates" list above?
   (I.e., did the models-on-disk sweep itself miss something - is there a
   smaller/cheaper published workflow worth adding even for a model NOT yet
   downloaded, if it's a natural fit for a 5080?)
4. Sanity-check the VRAM estimates already written into the existing metadata
   .txt files in `baselines/` - flag any that look wrong for a 16GB card.

## Constraints / non-negotiables

- Baselines must be STOCK/OFFICIAL minimal workflows - no OTR nodes, no custom
  wiring beyond what the model's own publisher ships (custom node wrapper
  workflows are acceptable ONLY when documented as the official/only path,
  as with Trellis2 and HunyuanWorld-Mirror).
- Do not recommend anything that clearly exceeds 16GB VRAM as "fits a 5080."
- Flag (don't silently skip) any model where no official example workflow
  exists at all.
