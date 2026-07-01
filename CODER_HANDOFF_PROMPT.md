# Coder Handoff: Build Baseline Workflow JSONs

**Status:** Fresh window — no prior context needed.

**Deliverable:** Individual minimal ComfyUI workflow JSON files for each video/3D model.

---

## Context

You're building baseline (stock, minimal) ComfyUI workflows for 5 models currently in use or planned:

1. **LTX Video 2b v0.9** (video generation)
2. **HunyuanVideo 3D-DIT v2** (3D mesh generation)
3. **Wan-Move** (motion-controllable video) — optional if time
4. **FLUX Gen1** (image generation; already in OTR but baseline needed)
5. **Trellis 2** (advanced 3D) — optional, lower priority

**Why:** Compare OTR's complexity vs. these minimal baselines to identify overhead/optimization opportunities.

**Folder:** `C:\Users\jeffr\Documents\ComfyUI\custom_nodes\otr-model-smoker\baselines\`

---

## Task Breakdown

### Phase 1: Fetch Official Baseline Workflows

For each model below, find and save the **official minimal workflow JSON** from the documented sources.

#### Model 1: LTX Video 2b v0.9

**Official Source:** https://comfyanonymous.github.io/ComfyUI_examples/ltxv/

**What to fetch:**
- Official example workflow image or JSON from the ComfyUI_examples repo
- Look for the simplest text-to-video example (no LoRA, no advanced nodes)

**Save as:** `baseline_ltx_2b_v0.9.json`

**Minimal expected structure:**
- 1 text input node
- 1 LTXVCLIPModelLoader
- 1 LTXVLoader (loading ltx-video-2b-v0.9.safetensors)
- 1 KSampler or LTXVSampler
- 1 video output node
- ~5–7 nodes total

---

#### Model 2: HunyuanVideo 3D-DIT v2

**Official Source:** https://docs.comfy.org/tutorials/3d/hunyuan3D-2

**What to fetch:**
- ComfyUI native HunyuanVideo 3D workflow from official docs
- Simplest image-to-mesh example (no advanced refinement)

**Save as:** `baseline_hunyuan3d_v2_mv.json`

**Minimal expected structure:**
- 1 image input node
- 1 Hunyuan3D loader
- 1 generator node
- 1 GLB output node
- ~5–6 nodes total

---

#### Model 3: Wan-Move (Optional, Lower Priority)

**Official Source:** https://docs.comfy.org/tutorials/video/wan/wan-move

**What to fetch:**
- Minimal motion-controllable image-to-video workflow
- No advanced trajectory visualization; just input image + output video

**Save as:** `baseline_wan_move.json`

**Minimal expected structure:**
- 1 image input
- 1 Wan loader
- 1 motion trajectory generator (simple)
- 1 video output
- ~6–7 nodes total

---

#### Model 4: FLUX Gen1 (Image)

**Official Source:** https://docs.comfy.org/tutorials/flux/flux-1-text-to-image

**What to fetch:**
- Official minimal text-to-image workflow
- No LoRA, no upscaling, no negatives (FLUX doesn't need them)

**Save as:** `baseline_flux_gen1.json`

**Minimal expected structure:**
- 1 text input node
- 1 DualCLIPLoader
- 1 KSampler
- 1 VAEDecode
- 1 SaveImage
- ~5 nodes total

---

#### Model 5: Trellis 2 (Optional, Lowest Priority)

**Official Source:** https://www.runcomfy.com/comfyui-workflows/comfyui-trellis2-workflow-advanced-structure-image-generation

**GitHub:** https://github.com/visualbruno/ComfyUI-Trellis2

**What to fetch:**
- Minimal image-to-GLB workflow (UV unwrapping + texture rasterization only; no advanced refinement)

**Save as:** `baseline_trellis2.json`

**Minimal expected structure:**
- 1 image input
- 1 Trellis2 loader
- 1 mesh generator
- 1 UV unwrap node
- 1 texture rasterize node
- 1 GLB output
- ~7–8 nodes total

---

### Phase 2: Metadata Documentation

For **each JSON saved**, create a companion `.txt` metadata file:

**Example:** `baseline_ltx_2b_v0.9_metadata.txt`

```
Model: LTX Video 2b v0.9
Version: 0.9.5
Official Source: https://comfyanonymous.github.io/ComfyUI_examples/ltxv/
Fetched: [DATE]
Node Count: 5
Link Count: 4
Last Node ID: 5
Workflow Type: Text-to-Video
Input Nodes: ["text", "resolution", "frame_count"]
Output Nodes: ["video"]
VRAM Estimate (RTX 5080): 4–6 GB
Quality Notes: Real-time capable; descriptive prompts essential.
Known Limitations: None observed.
Verified: [YES/NO — after loading in ComfyUI]
```

---

### Phase 3: Verification Checklist (Per JSON)

After fetching/creating each baseline:

- [ ] Valid JSON syntax (no parse errors)
- [ ] All node types exist in stock ComfyUI (no custom node deps unless documented)
- [ ] All links reference valid node IDs
- [ ] Widget values match expected types
- [ ] No dangling or circular wiring
- [ ] Output nodes are connected (not orphaned)

---

## Execution Steps

1. **Folder structure ready?**
   - Check: `C:\Users\jeffr\Documents\ComfyUI\custom_nodes\otr-model-smoker\baselines\` exists

2. **Phase 1: Fetch baseline JSONs**
   - Open each official source link above (in order of priority: 1, 2, 4, 3, 5)
   - Use WebSearch if docs are behind a login
   - Look for example workflow images (can be dragged into ComfyUI to extract JSON)
   - Or find direct JSON file in GitHub repos (raw.githubusercontent.com)
   - Save to `baselines/` folder with names as specified

3. **Phase 2: Create metadata**
   - For each JSON, write a companion `.txt` with the template above
   - Fill in actual values from the JSON (node count, links, etc.)
   - Mark "Verified: NO" initially

4. **Phase 3: Validate (Before Moving to Next Window)**
   - Load each JSON in a running ComfyUI instance
   - Confirm no "node not found" errors
   - Check that wiring is intact (all links valid)
   - Update metadata "Verified: YES" if clean
   - If errors found: log them in a `FETCH_ISSUES.md` file

---

## Deliverables (End of Phase)

```
baselines/
├── baseline_ltx_2b_v0.9.json
├── baseline_ltx_2b_v0.9_metadata.txt
│
├── baseline_hunyuan3d_v2_mv.json
├── baseline_hunyuan3d_v2_mv_metadata.txt
│
├── baseline_flux_gen1.json
├── baseline_flux_gen1_metadata.txt
│
├── baseline_wan_move.json (optional)
├── baseline_wan_move_metadata.txt (optional)
│
├── baseline_trellis2.json (optional)
├── baseline_trellis2_metadata.txt (optional)
│
└── FETCH_ISSUES.md (if any errors found during validation)
```

---

## Tools You'll Need

- **WebSearch** — for finding official docs/GitHub raw JSONs
- **Read** — to inspect official workflow examples
- **Write** — to save JSONs and metadata files
- **Glob** — to verify files exist in baselines/ folder

---

## Failure Modes & Workarounds

**"Can't find official JSON"**
- Check GitHub repos for `examples/` or `workflows/` directories
- Look for `.json` files in the model's official repo
- If only images provided, ask Jeffrey to drag-and-drop into ComfyUI and screenshot the JSON export

**"Node type not found"**
- Verify the node is built-in to ComfyUI (not a custom node)
- Check ComfyUI version compatibility (v0.4+)
- Log in FETCH_ISSUES.md with the missing node name

**"Links reference missing node IDs"**
- This means the exported workflow is corrupt or from a different ComfyUI version
- Try fetching from a different source (GitHub vs. docs site)
- Log in FETCH_ISSUES.md

---

## Priority Order

1. **LTX 2b v0.9** — Primary (most used in OTR)
2. **HunyuanVideo 3D-DIT v2** — Primary (3D baseline needed)
3. **FLUX Gen1** — Secondary (already in OTR; baseline for comparison)
4. **Wan-Move** — Optional (if time; motion control variant)
5. **Trellis 2** — Optional (high VRAM; lower priority for RTX 5080)

---

## Done Condition

- [ ] All 5 models have baseline JSONs in `baselines/`
- [ ] Each has metadata `.txt` file filled out
- [ ] All JSONs validated (no parse errors, links intact)
- [ ] Verified flags set to YES for clean baselines
- [ ] FETCH_ISSUES.md created (if any issues found)
- [ ] Folder ready for Phase 2: Smoke Testing (in next window)

---

## Handoff to Next Window

Once baselines are ready, the **next window** will:
1. Load each baseline in ComfyUI
2. Run a short smoke test (5-sec video clip, 1 image render)
3. Capture VRAM peaks + timing
4. Compare to OTR full workflow VRAM
5. Generate comparative analysis report

---

## Quick Reference: Model Details

| Model | VRAM Est. | Nodes | Output | Priority |
|-------|-----------|-------|--------|----------|
| LTX 2b v0.9 | 4–6 GB | ~5 | Video (24fps, 768×512) | 🔴 HIGH |
| HunyuanVideo 3D | 8–12 GB | ~6 | Mesh (GLB) | 🔴 HIGH |
| FLUX Gen1 | 7–9 GB | ~5 | Image | 🟡 MEDIUM |
| Wan-Move | 8–10 GB | ~7 | Video (480p, motion) | 🟡 MEDIUM |
| Trellis 2 | 16–24 GB | ~8 | Textured Mesh (GLB) | 🟢 LOW |

---

## Notes

- **No OTR nodes** in baselines — pure stock ComfyUI only
- **No custom nodes** unless documented in official sources
- **Minimal wiring** — input → model → output
- All sources are **official** (docs.comfy.org, GitHub official repos)
- UTF-8, no BOM for all files
