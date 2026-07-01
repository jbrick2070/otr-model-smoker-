# OTR Model Audit & Baseline Workflow Hunt

**Date:** 2026-06-30  
**Source Workflow:** `otr_scifi_16gb_full.json`  
**VRAM Ceiling:** 14 GB  
**Hardware:** RTX 5080, Torch 2.10+, CUDA 13  
**Purpose:** Identify models in use and find official minimal ComfyUI workflows for comparative testing

---

## Active Models in Workflow

### Video Generation

| Model | Role | Version/Variant | Source | Status | Notes |
|-------|------|-----------------|--------|--------|-------|
| **FLUX Gen1** | Image generation (announcer/music/scene stills) | Gen1 | Diffusers | Active | Base image model for all beat image generation |
| **HuMo 14B** | Character motion (169 aspect mode) | 14B variant | ? | Active | Optional character video path (currently visualizer) |
| **HuMo 1.7B** | Character motion (primary) | 1.7B variant | ? | Active | Default engine for OTR_VideoRenderBatch |
| **Visualizer** | Fallback/placeholder video | Built-in | ComfyUI | Active fallback | Current announcer/music/other-beats video engine |
| **character_3D** | Character 3D mesh rendering | TBD | Deferred | Deferred | Not currently wired; opt-in future |

### Audio / Voice Generation

| Model | Role | Version | Source | VRAM Est. | Status | Notes |
|-------|------|---------|--------|-----------|--------|-------|
| **IndextTS2** | Character voice synthesis | Latest | ComfyUI hub | ~2–3 GB | Active primary | Per-character voice rendering; 4 CC0 LibriVox refs banked |
| **Kokoro** | Announcer voice | Latest | ComfyUI hub | ~0.5 GB | Active | Fast, lightweight announcer TTS |
| **Bark** | Character voice fallback | Latest | OpenAI | ~2–3 GB | Fallback | Per-line discrete presets (v2/*) |
| **Stable Audio 3** | Music/theme generation | eng_stable_audio_3 | StabilityAI | ~5–7 GB | Active | Theme music, openings, closings |

### Language Models (Writing)

| Model | Role | Version | Source | Deployment | Status | Notes |
|-------|------|---------|--------|------------|--------|-------|
| **Mistral Nemo Instruct 2407** | Creative + technical writing | Latest | Hugging Face | OpenRouter (opt-in) / Comfy Credits | Active | DEFAULT_LLM for both writer slots |
| **Gemma-4-12B** | Writer local fallback | 12B | Google/Ollama | Ollama (127.0.0.1:11434) | Fallback | Character/style invention work; known GBNF constraints |

### Utility / Procedural

| Model | Role | Status | Notes |
|-------|------|--------|-------|
| OTR_WorkflowValidator | Workflow integrity | Built-in | Hardened v1 |
| OTR_VideoDirector | Policy dispatch | Built-in | Video model selector |
| OTR_ImageDirector | Image granularity control | Built-in | Per-beat image routing |
| OTR_ShotLock | Audio-to-video sync lock | Built-in | Ledger patch authority |
| Signal Lost Video (procgen) | Fallback video (1920×1080 CRT) | Built-in | 1080p scopes + background |

---

## Known Constraints & Observations

### VRAM Pressure Points (RTX 5080 @ 16GB VRAM)
- **IndextTS2 + Stable Audio 3 + HuMo 1.7B in-process** → peak ~13.5–15.1 GB (safe)
- **HuMo 14B variant** → rarely used; adds pressure
- **FLUX image gen** → ~7–9 GB per batch
- **Headless UTF-8 boot** requires `PYTHONUTF8=1` or Emoji crashes server at boot (~13s)

### Known Issues / Workarounds
1. **HuMo-2D pillarbox** (480×832) → compositor pads to ~1472×832 (by design)
2. **BUG-291 NORMAL_VRAM** → removed `--highvram` flag; FLUX 1.1–1.3s/it, HuMo 14–18s/it
3. **Comfy Desktop restart needed** → new code/deps don't load without relaunch
4. **Ledger drift** → OTR_WorkflowValidator + validator guard gates widget mismatches
5. **Sage Attention (BUG-070)** → gated behind `sage_attention_enabled` for LTX branching

### NVIDIA Studio Driver
- **Version:** 610.47 (2026-05-26)
- **Impact:** Improved ComfyUI model load/offload perf; all perf baselines are **pre-driver**, need re-capture

---

## Baseline Workflow Hunt Plan

### Objective
Locate **official, minimal ComfyUI workflows** for each model to establish:
1. **Out-of-the-box performance** (no OTR tuning)
2. **Dependency count** (how much baggage each adds)
3. **VRAM footprint** (clean vs. OTR-optimized)
4. **Configuration surface** (how many widgets/inputs?)
5. **Known issues** at the model level

### Search Strategy (Per Model)

For each model, search for:
- **Official GitHub repo** (if it publishes examples)
- **ComfyUI Manager / hub entries** (README, example workflows)
- **Hugging Face model card** (if HF hosted)
- **Civitai** (if SDXL/checkpoint hosted)
- **Community forum posts** (Reddit, Discord, GitHub Issues)

### Workflow Collection Rules
- **No OTR nodes** — pure stock ComfyUI
- **Minimal wiring** — just input → model → output
- **Record metadata:** input node types, widget counts, link count, last_node_id
- **Save as JSON** per model; name = `baseline_<MODEL>_<variant>.json`

### Execution Plan

**Phase 1 — Research & Collection (WebSearch)**
- FLUX Gen1 baseline
- HuMo 1.7B baseline
- IndextTS2 baseline
- Kokoro baseline
- Stable Audio 3 baseline
- Mistral Nemo baseline (Ollama / local)

**Phase 2 — Validation (Quick Smoke)**
- Load each baseline JSON in ComfyUI
- Confirm no missing nodes / broken links
- Run a 10-frame / 5-second smoke render
- Capture VRAM peak + timing

**Phase 3 — Comparative Analysis**
- Graph VRAM: OTR full vs. each baseline
- Calculate overhead per model
- Identify bottlenecks (widget count, batch size, etc.)

**Phase 4 — Report**
- Summary: Is OTR setup suboptimal? Where?
- Recommendations: Prune, reorder, or refactor nodes?

---

## Directory Structure

```
otr-model-smoker/
├── MODEL_AUDIT.md                           (this file)
├── BASELINE_HUNT_PLAN.md                    (detailed search log)
├── COMPARATIVE_ANALYSIS.md                  (findings + recommendations)
│
├── baselines/
│   ├── baseline_flux_gen1.json
│   ├── baseline_flux_gen1_metadata.txt
│   │
│   ├── baseline_humo_1.7b.json
│   ├── baseline_humo_1.7b_metadata.txt
│   │
│   ├── baseline_indextts2.json
│   ├── baseline_indextts2_metadata.txt
│   │
│   ├── baseline_kokoro.json
│   ├── baseline_kokoro_metadata.txt
│   │
│   ├── baseline_stable_audio_3.json
│   ├── baseline_stable_audio_3_metadata.txt
│   │
│   ├── baseline_mistral_nemo.json
│   ├── baseline_mistral_nemo_metadata.txt
│
└── smoke_results/
    ├── smoke_<model>_YYYYMMDD_HHMM.json     (VRAM + timing per run)
    └── summary_comparative_vram.csv          (side-by-side table)
```

---

## Research Log

### Model: FLUX Gen1
- **Target:** Official minimal text-to-image workflow
- **Search terms:** "FLUX Gen1 comfyui workflow", "FLUX minimal example"
- **Status:** [PENDING]

### Model: HuMo 1.7B
- **Target:** Official character motion capture workflow
- **Search terms:** "HuMo comfyui workflow", "HuMo-2D minimal"
- **Status:** [PENDING]

### Model: IndextTS2
- **Target:** Official text-to-speech workflow
- **Search terms:** "IndextTS2 comfyui", "ComfyUI TTS minimal"
- **Status:** [PENDING]

### Model: Kokoro
- **Target:** Official fast TTS workflow
- **Search terms:** "Kokoro TTS comfyui", "Kokoro minimal workflow"
- **Status:** [PENDING]

### Model: Stable Audio 3
- **Target:** Official music generation workflow
- **Search terms:** "Stable Audio 3 comfyui", "Stability AI music workflow"
- **Status:** [PENDING]

### Model: Mistral Nemo (Local)
- **Target:** Official Ollama/local LLM workflow
- **Search terms:** "Mistral Nemo comfyui local", "Ollama text generation workflow"
- **Status:** [PENDING]

---

## Notes

- **No stored assets:** This hunt uses clean, published workflows only (no OTR ledgers, prompts, or stills).
- **Deep research:** Consider if a formal deep-research pass is warranted (fan-out, multi-source verification, adversarial fact-check). For now, a rapid WebSearch + GitHub crawl is sufficient.
- **Output folder:** All results → `C:\Users\jeffr\Documents\ComfyUI\custom_nodes\otr-model-smoker\`
- **Next step:** Begin Phase 1 research; update research log as sources are found.
