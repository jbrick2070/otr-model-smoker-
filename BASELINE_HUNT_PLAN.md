# Baseline Workflow Hunt — Phase 1 Research Plan

**Objective:** Locate official, minimal, stock ComfyUI workflows for each active model.

**Methodology:** WebSearch + GitHub repo crawl for published examples.

**Timeline:** Rapid hunt (no deep adversarial verification; just published sources).

---

## Target Models & Search Strategy

### 1. FLUX Gen1 (Image)

**Goal:** Minimal text-to-image workflow (no LoRA, no upscaling, just prompt → image).

**Search Avenues:**
- Black Forest Labs official GitHub / docs
- ComfyUI Manager hub entries (FLUX nodes)
- Hugging Face model card examples
- Community workflows (Reddit, Discord)

**Expected Output:**
- 1 text input node
- 1 FLUX checkpoint loader
- 1 KSampler or equivalent
- 1 VAE Decode
- 1 image output
- ~5 nodes, minimal wiring

---

### 2. HuMo 1.7B (Character Video)

**Goal:** Minimal character animation workflow (portrait → video).

**Search Avenues:**
- HuMo official repo (if public)
- ComfyUI custom nodes for HuMo
- Hugging Face model cards / demos
- Civitai (if checkpoint/LoRA hosted)

**Expected Output:**
- 1 reference image input
- 1 HuMo model loader
- 1 audio input (for sync)
- 1 generator node
- 1 video output
- ~5–7 nodes

---

### 3. IndextTS2 (Character TTS)

**Goal:** Minimal text-to-speech workflow (text + voice ref → audio).

**Search Avenues:**
- ComfyUI custom nodes repo (IndextTS2 node)
- Kyutai official repo (model authors)
- ComfyUI Manager examples
- Voice bank reference (CC0 samples)

**Expected Output:**
- 1 text input
- 1 voice ref image/audio input
- 1 IndextTS2 loader
- 1 generator
- 1 audio output
- ~5–6 nodes

---

### 4. Kokoro (Fast TTS)

**Goal:** Minimal text-to-speech workflow (text → audio, no refs).

**Search Avenues:**
- Kokoro official GitHub
- ComfyUI custom nodes (if a wrapper exists)
- Hugging Face model card
- ONNX / inference examples

**Expected Output:**
- 1 text input
- 1 Kokoro loader
- 1 generator
- 1 audio output
- ~4 nodes

---

### 5. Stable Audio 3 (Music)

**Goal:** Minimal music generation workflow (text prompt → audio).

**Search Avenues:**
- Stability AI official docs / repo
- ComfyUI Stable Audio node
- Hugging Face examples
- Community music workflows

**Expected Output:**
- 1 text prompt input
- 1 Stable Audio 3 loader
- 1 generator
- 1 audio output
- ~4–5 nodes

---

### 6. Mistral Nemo (Local LLM)

**Goal:** Minimal text generation workflow (prompt → text, local Ollama).

**Search Avenues:**
- Mistral official docs
- Ollama model library
- ComfyUI TextGenerate / LLM nodes
- Ollama API examples

**Expected Output:**
- 1 text input (system prompt)
- 1 text input (user message)
- 1 Ollama client / loader
- 1 generator
- 1 text output
- ~5 nodes

---

## Search Execution Log

**Phase 1 Complete — 2026-06-30**

### FLUX Gen1

**Official Source:** https://docs.comfy.org/tutorials/flux/flux-1-text-to-image

**Key Findings:**
- Minimal workflow: Text → DualCLIPLoader → KSampler → VAEDecode → SaveImage
- ~5 core nodes; CFG = 1.0, no negative prompt needed (FLUX is strong at prompt following)
- Flux.1 [dev] = higher quality; Flux.1 [schnell] = 4-step, 4GB VRAM variant available
- VAE loader required (distinct from CLIP)
- Simple, standard litegraph structure

**Baseline Workflow:** Official example available at GitHub/Comfy-Org (drag-and-drop image in ComfyUI)

---

### HuMo 1.7B

**Official Source:** https://github.com/Phantom-video/HuMo (native ComfyUI support confirmed)

**Key Findings:**
- ComfyUI natively supports HuMo 1.7B
- 1.7B = 480p video generation (~8 min on 32GB GPU; your 16GB ≈ 15–20 min)
- Takes: reference image + audio input → generates synchronized video
- HuMo Spark workflow available (OpenArt): reference image + audio + prompts → 480p/720p @ 25fps
- Optional: LightX2V LoRA support for additional motion enhancement
- ~7 nodes for minimal (loader + generator + audio input + video output)

**Baseline Workflow:** OpenArt has working example: https://openart.ai/workflows/faych/humo/TdPpCLPGH1xxb2rnlabq

---

### IndextTS2

**Official Source:** https://github.com/snicolast/ComfyUI-IndexTTS2 (faithful to original repo)

**Key Findings:**
- Reference-conditioned voice cloning (text + speaker audio ref → speech)
- Optional emotion audio for expressiveness control
- IndexTTS2 Simple node: speaker_audio + text → audio output
- IndexTTS2 Advanced node: adds CFG, seed, FP16, output gain, speech speed controls
- Emotion vector input available (8 sliders, sum ≤ 1.5)
- ~5–6 nodes for minimal workflow

**Baseline Workflow:** Community workflows available (RunComfy): https://www.runcomfy.com/comfyui-workflows/indextts2-comfyui-workflow-emotional-voice-cloning-speech-synthesis

---

### Kokoro

**Official Source:** Multiple implementations; recommended: https://github.com/benjiyaya/ComfyUI-KokoroTTS

**Key Findings:**
- Fast, lightweight TTS (ONNX-based)
- Minimal setup: text input + voice selector → audio output
- 54+ voices across 9 languages (variant-dependent: some 8 languages, 150 voices)
- Auto-downloads weights on first use (~1–2 min)
- ~4 nodes for minimal workflow
- No reference audio needed (unlike IndextTS2); discrete voice presets only

**Baseline Workflow:** Simple: TextInput → KokoroTTS → SaveAudio (~3 nodes)

---

### Stable Audio 3

**Official Source:** https://docs.comfy.org/tutorials/audio/stable-audio/stable-audio-3

**Key Findings:**
- Official ComfyUI support (Comfy-Org workflow templates)
- Three variants: Small-SFX, Small-Music, Medium
- Requires: Stable Audio checkpoint + text encoders (qwen3.5_2b_bf16, t5gemma_b_ul2)
- Uses Qwen 3.5 2B BF16 for prompt expansion (category-aware templates)
- Variable-length output: 2 min (Small), ~6 min (Medium)
- Reprompt categories: Music, Instrument, SFX, One-shot
- ~5–6 nodes for complete workflow (text input + category selector + generator + SaveAudio)

**Baseline Workflow:** Official template: https://github.com/Comfy-Org/workflow_templates/blob/main/templates/audio_stable_audio_3_medium_base.json

---

### Mistral Nemo (Local Ollama)

**Official Source:** https://ollama.com/library/mistral-nemo

**Key Findings:**
- Mistral Nemo = 12B model, 128k context window
- Ollama pulls and runs it locally: `ollama pull mistral-nemo`
- ComfyUI integration via: ComfyUI-IF_AI_tools or ComfyUI-Ollama-Describer
- IF_AI_tools workflow: system prompt + user prompt → LLM → text output
- Ollama endpoint: http://127.0.0.1:11434 (default)
- ~4–5 nodes for minimal workflow (system prompt + user input + Ollama LLM + text output)

**Baseline Workflow:** ComfyUI-IF_AI_tools example: https://github.com/if-ai/ComfyUI-IF_AI_tools

---

## Validation Checklist (Phase 2)

Once workflows are collected:

- [ ] Load in ComfyUI (valid JSON, no parse errors)
- [ ] All nodes present (no missing custom node deps)
- [ ] All links valid (no dangling references)
- [ ] Smoke run: 10-frame or 5-sec audio render
- [ ] VRAM peak captured (nvidia-smi snapshot)
- [ ] Timing recorded (start → completion)
- [ ] Output asset verified (file exists, size > 0)

---

## Next Steps

1. Execute Phase 1 searches
2. Collect valid workflow JSONs → `baselines/`
3. Document metadata for each
4. Run Phase 2 smoke tests
5. Generate comparative analysis report
