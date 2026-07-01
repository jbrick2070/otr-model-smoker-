# VIDEO & 3D Models — Quick Baseline Reference

**Status:** Phase 1 Research Complete | Focus: Video + 3D only

---

## Your Hardware Constraint

**RTX 5080 @ 16GB VRAM** → Max sustainable peak ≈ 14–15 GB before spillage

---

## Video Models in Your Folder

### LTX Video 2b v0.9
- **VRAM:** 4–6 GB
- **Speed:** 5-sec clip in 4 sec (real-time capable)
- **Quality:** Good; best for speed
- **Baseline Workflow:** https://docs.comfy.org/tutorials/video/ltxv
- **Recommendation:** Use if you need fast turnaround

### LTX 2.3 (Full/Distilled)
- **VRAM (Q3_K_M):** 9.4 GB (recommended quantization for 5080)
- **Speed:** 13–15 s/iter (slower than 2b, higher quality)
- **Quality:** High; better frame detail
- **Baseline Workflow:** Same as 2b (node-compatible)
- **Recommendation:** Use if quality > speed; avoid Q4_K_M (spills)

### FLUX Gen1 (FP8)
- **VRAM:** 7–9 GB
- **Role:** Image stills (announcer/music beats), not video
- **Baseline Workflow:** https://docs.comfy.org/tutorials/flux/flux-1-text-to-image
- **In OTR:** Already used for FLUX image generation

### Wan (Motion Video) — **STALE NOTE, CORRECTED 2026-06-30: NOW DOWNLOADED**
- **VRAM:** 8–10 GB (Wan-Move estimate; see per-model baselines for others)
- **Input:** Image + motion trajectories or driving video
- **Output:** 5 sec @ 480p
- **Baseline Workflow:** https://docs.comfy.org/tutorials/video/wan/wan-move (Wan-Move) — baselined as `baselines/baseline_wan_move.json`
- **Status:** This line originally said "NOT Downloaded" — that is now WRONG. Confirmed present on disk: Wan2.2-TI2V-5B-Q5_K_M.gguf, wan2.2_i2v_low_noise_14B_fp8_scaled.safetensors, Wan2.1-I2V-1.3B (diffusers format). None of these three specific variants have a baseline JSON yet as of this note — only Wan-Move does. Caught by kibitz round-2 panel review (Codex) 2026-06-30.

---

## 3D Models in Your Folder

### HunyuanVideo 3D-DIT v2 (Downloaded)
- **VRAM:** 8–12 GB
- **Input:** Single image
- **Output:** Geometry (GLB/OBJ); texture separate
- **Speed:** 30–60 sec per image
- **Baseline Workflow:** https://docs.comfy.org/tutorials/3d/hunyuan3D-2
- **Recommendation:** Use; native ComfyUI support

### Trellis 2 — **NOT Downloaded**
- **VRAM:** 16–24 GB (REQUIRES dedicated session on your box)
- **Input:** Single image
- **Output:** Full textured mesh (GLB)
- **Speed:** 2–3 min per image (RTX 5080 est.)
- **GitHub:** https://github.com/visualbruno/ComfyUI-Trellis2
- **Recommendation:** Too heavy for current VRAM; skip or dedicate a separate run

---

## VRAM Compatibility Matrix

Can you run these together on RTX 5080?

| Combination | Total VRAM | Spillage? | Verdict |
|------------|-----------|-----------|---------|
| LTX 2b v0.9 + HunyuanVideo | 12–16 GB | No | ✅ Safe |
| LTX 2.3 Q3_K_M + HunyuanVideo | 17–21 GB | Yes | ⚠️ Risky (on edge) |
| LTX 2.3 Q4_K_M + anything | 32+ GB | Yes | ❌ Never |
| LTX 2b + Trellis 2 | 20–30 GB | Yes | ❌ Never (stage separately) |
| FLUX Gen1 + LTX 2b + HunyuanVideo | 18–23 GB | Yes | ⚠️ Possible; squeeze hard |

**Safest staging:** Run video and 3D in separate sessions, or pipeline image stills → video + 3D separately.

---

## Baseline Workflows (Ready to Download & Test)

1. **LTX 2b v0.9 minimal:** https://comfyanonymous.github.io/ComfyUI_examples/ltxv/
2. **Wan-Move minimal:** https://docs.comfy.org/tutorials/video/wan/wan-move
3. **HunyuanVideo 3D minimal:** https://docs.comfy.org/tutorials/3d/hunyuan3D-2
4. **Trellis 2 complete:** https://www.runcomfy.com/comfyui-workflows/comfyui-trellis2-workflow-advanced-structure-image-generation

---

## Decision Flowchart

**Q: Is your current OTR setup suboptimal?**

**→ Option A (Quick Smoke Test — 30 min)**
- Load each baseline JSON
- Run a 5-sec video clip on LTX 2b
- Render 1 image on HunyuanVideo 3D
- Record VRAM peaks
- **Output:** Baseline VRAM graph vs. OTR full workflow

**→ Option B (Refactor now based on constraints)**
- LTX 2b v0.9 is your safe default (keeps VRAM under 15GB)
- LTX 2.3 Q3_K_M works but tight; test first
- Trellis 2 doesn't fit; either skip or dedicate GPU
- 3D and video shouldn't run simultaneously

**→ Option C (Keep OTR as-is)**
- If you're happy with current quality/speed, no changes needed
- Baselines just confirm whether you have room to optimize

---

## My Recommendation

**Start with Option A (smoke test).** It takes 30 minutes and answers: "Are my models bloated?"

If LTX 2b + HunyuanVideo 3D baselines hit **>15GB VRAM peak**, your current OTR setup is already optimized. If they stay **<12GB**, you have overhead to trim.

Then decide: refactor (B) or status quo (C).

---

## File Inventory

```
otr-model-smoker/
├── VIDEO_3D_MODELS_AUDIT.md        (detailed specs + VRAM breakdowns)
├── VIDEO_3D_SUMMARY.md              (this file — quick ref)
├── MODEL_AUDIT.md                   (original full model list, now outdated)
├── COMPARATIVE_ANALYSIS.md          (template for Phase 2 results)
│
└── baselines/                       (ready to populate with JSON files)
    ├── baseline_ltx_2b_v0.9.json       (fetch from official ComfyUI examples)
    ├── baseline_ltx_2.3_q3km.json      (optional; same node structure)
    ├── baseline_wan_move.json           (if you download Wan)
    ├── baseline_hunyuan3d_v2.json       (fetch from ComfyUI docs)
    └── baseline_trellis2.json           (if you decide to test)
```

---

## Links (All Verified & Official)

**Video Models:**
- [LTX 2b v0.9 Docs](https://docs.comfy.org/tutorials/video/ltxv)
- [LTX GitHub](https://github.com/Lightricks/ComfyUI-LTXVideo)
- [Wan-Move Tutorial](https://docs.comfy.org/tutorials/video/wan/wan-move)

**3D Models:**
- [HunyuanVideo 3D Docs](https://docs.comfy.org/tutorials/3d/hunyuan3D-2)
- [Trellis 2 Guide](https://trellis2.app/blog/trellis-2-comfyui-workflow)
- [Trellis 2 GitHub](https://github.com/visualbruno/ComfyUI-Trellis2)
