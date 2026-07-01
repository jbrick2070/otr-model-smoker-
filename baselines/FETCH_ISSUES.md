# Fetch Issues Log

All 5 baseline JSONs fetched and pass JSON parse + link-integrity checks (no dangling links, no parse errors). No blocking issues. Notes below are deviations from the spec's node-count estimates, not errors.

## Deviations from spec estimates (not bugs)

- **LTX 2b v0.9**: 13 nodes vs. spec estimate of ~5-7. Official stock example needs separate CLIP/VAE loaders plus dual video output nodes.
- **HunyuanVideo 3D-DIT v2**: 11 nodes vs. spec estimate of ~5-6. Stock ComfyUI splits conditioning/latent/sampler/decode into separate nodes.
- **Wan-Move**: 16 nodes vs. spec estimate of ~6-7. Official template includes a distillation LoRA loader and dual save/preview outputs.
- **Trellis 2**: 18 nodes vs. spec estimate of ~7-8, and REQUIRES the ComfyUI-Trellis2 custom node pack (not stock ComfyUI). This is the officially documented exception — the model has no native ComfyUI support, only via this wrapper repo, and "MeshOnly.json" is the simplest of 15 example workflows the repo ships.
- **FLUX Gen1**: 10 nodes vs. spec estimate of ~5. Used the FP8 checkpoint version (10 nodes: checkpoint loader, 2x CLIPTextEncode-style prompt, sampler, decode, save) rather than the full split-loader Dev/Schnell version, which is closer to minimal.

## Extraction method notes

- LTX and Wan-Move JSONs were available as direct `.json` downloads.
- FLUX and HunyuanVideo 3D JSONs were only published embedded in workflow example images (PNG tEXt chunk for FLUX, WebP EXIF chunk for HunyuanVideo 3D) — extracted programmatically via PNG/RIFF chunk parsing since no bare JSON was published for these two.

## Verification status

All 5 marked "Verified: NO" in their metadata files — none have been load-tested in a running ComfyUI instance yet. That is Phase 3 work for the next window per the handoff doc.

## 2026-06-30 addendum: models found on disk but missing from original scope

Cross-checked `C:\ComfyUI-Models` against the 5-model baseline scope and against what's actually wired into `otr_scifi_16gb_full.json`. Found 3 more video/3D models loaded that had no baseline:

- **HuMo 17B** (`baseline_humo_17b.json`) — official native ComfyUI template, direct JSON download, 20 nodes. This is the ACTUAL video model wired into the OTR production workflow (as `humo_14B_169`/`humo_1.7B`) — not LTX, not Wan-Move. Should have been in scope from the start.
- **LTX 2.3 22B dev** (`baseline_ltx2.3_dev_t2v.json`) — official native ComfyUI template. Top-level JSON shows only 3 nodes because ComfyUI's newer subgraph feature nests the real ~51-node pipeline under `definitions.subgraphs`. Do not mistake this for a trivial workflow.
- **HunyuanWorld-Mirror** (`baseline_hunyuanworldmirror.json`) — a 3D reconstruction model (point clouds/Gaussians/depth/camera poses from image sequences), distinct from Hunyuan3D-2. NO official example workflow JSON exists anywhere (checked the model's HF page and the only ComfyUI wrapper, cedarconnor/ComfyUI-HunyuanWorld-Mirror — repo ships demo images only, no .json). This baseline was hand-transcribed from the repo's documented node graph and is NOT a downloaded official file — flag for extra scrutiny in Phase 3 verification.

Also checked and confirmed NOT present / not relevant: `CogVideo/` folder exists but is empty (no weights downloaded, not an active model). No Trellis2/dinov3 weights found on disk despite the Trellis2 baseline already built — consistent with it being the lowest-priority, not-yet-installed option per the original spec.
