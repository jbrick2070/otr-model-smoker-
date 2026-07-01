# Baseline Workflow Research Summary

**Phase 1 Complete:** 2026-06-30

---

## What Was Found

All target models have **published, official minimal workflows** available. No deep research was needed — they're readily discoverable.

---

## Model Baseline Workflow Profiles

### FLUX Gen1 (Image Generation)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **Official Source** | ComfyUI docs + GitHub Comfy-Org | Drag-and-drop example workflow |
| **Minimal Nodes** | ~5 | Text → DualCLIPLoader → KSampler → VAEDecode → SaveImage |
| **CFG** | 1.0 | No negative prompt needed |
| **Variants** | dev (quality), schnell (4-step, 4GB) | Schnell ≈ half the VRAM |
| **Expected VRAM** | 7–9 GB (dev); 4–5 GB (schnell) | Per-batch, no offload |

**Action:** Fetch official example from https://docs.comfy.org/tutorials/flux/flux-1-text-to-image

---

### HuMo 1.7B (Character Video)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **Official Support** | Native ComfyUI (v0.4+) | No custom node required |
| **Minimal Nodes** | ~7 | Loader + generator + audio input + output |
| **Input** | Reference image + audio sync | Audio-driven motion |
| **Baseline Render Time** | ~8 min @ 32GB GPU | Your 16GB ≈ 15–20 min (estimated) |
| **Output Resolution** | 480p default (HuMo-2D) | 480×832 aspect locked |
| **Expected VRAM** | 8–10 GB in-process | Bundled with audio in OTR |

**Community Example:** https://openart.ai/workflows/faych/humo/TdPpCLPGH1xxb2rnlabq

---

### IndextTS2 (Character TTS)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **GitHub** | https://github.com/snicolast/ComfyUI-IndexTTS2 | Faithful to original |
| **Minimal Nodes** | ~5–6 | Simple: speaker_audio + text → audio |
| **Inputs** | Speaker reference + text (+ optional emotion audio) | Emotion = expressiveness |
| **Node Variants** | Simple, Advanced | Advanced has seed/CFG/speed controls |
| **Expected VRAM** | 2–3 GB | Lightweight |
| **Key Feature** | Reference-conditioned voice cloning | Your setup: 4 CC0 LibriVox refs banked |

**Action:** Fetch from RunComfy: https://www.runcomfy.com/comfyui-workflows/indextts2-comfyui-workflow-emotional-voice-cloning-speech-synthesis

---

### Kokoro (Announcer TTS)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **GitHub** | Multiple (recommended: benjiyaya) | https://github.com/benjiyaya/ComfyUI-KokoroTTS |
| **Minimal Nodes** | ~4 | Text + voice selector → audio |
| **Voice Options** | 54+ voices, 9 languages | Variant-dependent (some 8L, 150V) |
| **Model Type** | ONNX-based, fast | Auto-download on first use |
| **Expected VRAM** | 0.5–1 GB | Minimal overhead |
| **Key Difference vs. IndextTS2** | No reference audio; discrete presets only | Faster, simpler |

**Baseline:** Simple 3-node workflow (no examples needed — very straightforward)

---

### Stable Audio 3 (Music)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **Official Source** | ComfyUI docs + workflow templates | https://docs.comfy.org/tutorials/audio/stable-audio/stable-audio-3 |
| **Template JSON** | https://github.com/Comfy-Org/workflow_templates | audio_stable_audio_3_medium_base.json |
| **Minimal Nodes** | ~5–6 | Text prompt + category + generator + output |
| **Required Models** | Stable Audio checkpoint + text encoders | qwen3.5_2b_bf16.safetensors + t5gemma_b_ul2.safetensors |
| **Variants** | Small-SFX (2m), Small-Music (2m), Medium (6m) | Length ↔ quality tradeoff |
| **Prompt Expansion** | Qwen 3.5 2B BF16 category-aware | Music/Instrument/SFX/One-shot templates |
| **Expected VRAM** | 5–7 GB (in-process with expansion) | Text encoder runs first |

**Action:** Fetch official template from GitHub Comfy-Org

---

### Mistral Nemo (Local LLM)

| Metric | Baseline | Notes |
|--------|----------|-------|
| **Model** | Mistral Nemo 12B | 128k context window |
| **Runner** | Ollama (local, offline) | `ollama pull mistral-nemo` |
| **ComfyUI Integration** | IF_AI_tools or Ollama-Describer | Two wrapper options |
| **Minimal Nodes** | ~4–5 | System prompt + user input + LLM + text output |
| **Ollama Endpoint** | http://127.0.0.1:11434 (default) | Your current setup |
| **Expected VRAM** | GPU: 7–9 GB; CPU offload: ~24 GB | Quantized GGUF available |

**Action:** Fetch from GitHub https://github.com/if-ai/ComfyUI-IF_AI_tools

---

## Next Steps: Phase 2 (Your Decision)

### Option A: Quick Smoke Testing (Recommended)

**What:** Load each baseline workflow in ComfyUI; run a short test (10 frames video, 5 sec audio).

**Why:** See if OTR's current setup has obvious overhead (extra nodes, VRAM conflicts).

**Effort:** ~30 min (one test per model).

**Output:** Baseline VRAM peaks + timings; raw comparison to OTR full workflow.

### Option B: Deep Research via Roundtable

**What:** Fan out to GPT + Gemini + DeepSeek for independent reviews of OTR's architecture vs. these baselines.

**Why:** Get a 3-model consensus on whether OTR is suboptimal, and where to refactor.

**Effort:** ~$0.50–1.00 (live, no dry-run).

**Output:** Panel judgment + Claude grounded synthesis = concrete refactoring roadmap.

### Option C: Skip Baselines, Audit OTR Directly

**What:** Assume OTR is fine; do a surgical code audit to identify dead nodes or redundant wiring.

**Why:** Faster than testing; if OTR is already optimized, baselines won't change anything.

**Effort:** ~20 min (read workflow, grep for unused outputs, check wiring).

**Output:** List of nodes to consider pruning.

---

## My Recommendation

Start with **Option A (Quick Smoke)** because:
1. It's fastest (30 min).
2. It gives you concrete data (VRAM side-by-side).
3. If baselines are much smaller, you'll know immediately that OTR has overhead to cut.
4. If baselines are similar in VRAM, OTR's complexity buys features worth keeping.

Then, **only if Option A reveals clear overhead**, move to Option B (roundtable consensus) to design the refactor.

---

## File Structure Ready

```
C:\Users\jeffr\Documents\ComfyUI\custom_nodes\otr-model-smoker\
├── MODEL_AUDIT.md                 (current models + specs)
├── BASELINE_HUNT_PLAN.md          (this research log + sources)
├── COMPARATIVE_ANALYSIS.md        (template for Phase 2 results)
├── RESEARCH_SUMMARY.md            (this file — quick reference)
│
└── baselines/                     (empty, ready for JSON files)
```

---

## Sources

- [ComfyUI FLUX Official Docs](https://docs.comfy.org/tutorials/flux/flux-1-text-to-image)
- [HuMo Official GitHub](https://github.com/Phantom-video/HuMo)
- [ComfyUI-IndexTTS2 GitHub](https://github.com/snicolast/ComfyUI-IndexTTS2)
- [Kokoro TTS GitHub (benjiyaya)](https://github.com/benjiyaya/ComfyUI-KokoroTTS)
- [ComfyUI Stable Audio 3 Official Docs](https://docs.comfy.org/tutorials/audio/stable-audio/stable-audio-3)
- [Stable Audio 3 Workflow Templates](https://github.com/Comfy-Org/workflow_templates/blob/main/templates/audio_stable_audio_3_medium_base.json)
- [Ollama Mistral Nemo Library](https://ollama.com/library/mistral-nemo)
- [ComfyUI-IF_AI_tools GitHub](https://github.com/if-ai/ComfyUI-IF_AI_tools)
