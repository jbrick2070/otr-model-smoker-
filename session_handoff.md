# Session Handoff -- otr-model-smoker -- 2026-06-30

## Core goal
Build stock/minimal ("baseline") ComfyUI workflow JSONs for every video/3D generative
model actually installed under `C:\ComfyUI-Models`, so OTR's production workflow
complexity can be compared against minimal official pipelines and overhead/optimization
opportunities identified. Per project intent: same concept as the OldTimeRadio project's
model testing, but standalone -- never write into `ComfyUI-OldTimeRadio` from here.

## Tech stack & constraints
- Folder: `C:\Users\jeffr\Documents\ComfyUI\custom_nodes\otr-model-smoker`. Now a real
  git repo (was NOT one at session start -- this blocked Codex's kibitz lane, which
  refuses to run outside a trusted git dir). Remote:
  `https://github.com/jbrick2070/otr-model-smoker-.git`, branch `main`. Local HEAD
  matches origin as of commit `11bc3c0` -- pushed and confirmed.
- Hardware ceiling: single RTX 5080, 16GB VRAM. **The real safe bar, per this repo's own
  prior research (`VIDEO_3D_SUMMARY.md`), is ~14-15GB sustainable peak before spillage --
  not the raw 16GB card total.** Judge every VRAM estimate against 14-15GB, not 16GB.
- Baselines must be stock/official minimal workflows only. Custom-node-wrapper workflows
  are acceptable ONLY when officially documented as the sole path (Trellis2 and
  HunyuanWorld-Mirror are the two exceptions). HunyuanWorld-Mirror's JSON is hand-built
  (no official example exists anywhere) and is explicitly NOT counted as equivalent to
  the 7 real fetched-official baselines -- keep that distinction in any future tally.
- Global rules (from the user's `~/.claude/CLAUDE.md`, auto-loaded every session, do not
  repeat in code): UTF-8 no BOM, never "dummy" (use "placeholder"/"stub"), SFW, no
  profanity, ASCII quotes + `cd` first in any PowerShell handoff block.
- **Desktop Commander runs real PowerShell on the actual Windows box** -- use it for git,
  python execution, and file verification. **The `mcp__workspace__bash` Linux sandbox
  LAGS real file-tool writes** and gave phantom "Extra data" JSON parse errors on 2 files
  this session that were actually fine (confirmed by re-reading the real files and
  re-validating via Desktop Commander). Never trust the Linux bash mount for validation
  in this repo -- always verify Windows-side.
- `kibitz` skill (3-CLI panel: Claude Code, Codex, Antigravity -- all installed, confirmed
  via `scripts/doctor.py`) is available for hardening plans. Codex needs a git repo
  (now satisfied). Claude Code and Antigravity can take 10+ minutes and LOOK hung with
  zero stdout -- check the run folder's file timestamps/sizes before assuming a stuck
  process and killing it; both finished successfully this session, just later than the
  first check suggested. If the user says to abandon `agy` (API credit concerns), drop
  it and continue with Codex + Claude-as-judge only.

## What's done & decided

**8 baseline workflow JSONs exist in `baselines/`**, each with a companion
`_metadata.txt`: `baseline_flux_gen1`, `baseline_ltx_2b_v0.9`, `baseline_hunyuan3d_v2_mv`,
`baseline_wan_move`, `baseline_trellis2`, `baseline_humo_17b`, `baseline_ltx2.3_dev_t2v`,
`baseline_hunyuanworldmirror`. All 8 validated this session (valid JSON, zero dangling
links) via a Python script run through Desktop Commander against the real Windows files.

Ran kibitz **r1** (Antigravity + Claude Code, both finished late) and **r2** (Codex +
Antigravity, again late) against `baselines/KIBITZ_COVERAGE_PLAN.md`. Stopped the arc
after r2 -- all three independent model families converged on the same core issues with
no disagreement, which is the arc's own stated exit condition. Did not run r3/r4
(judged unnecessary for a research/coverage audit with no code-wiring component).

**Fixed this session** (every claim verified against real disk/source before acting,
nothing applied blindly):
- `baseline_ltx2.3_dev_t2v` flagged NOT runnable on the 5080 as fetched -- the dev
  checkpoint is 46.1GB bf16 (confirmed via file size), needs ~44-46GB VRAM. Correct 5080
  path is `unet/ltx-2.3-22b-dev-Q3_K_M.gguf` (~9.4GB per prior repo research); the
  fetched JSON is reference-only until rebuilt with a GGUF loader node.
- `baseline_trellis2` flagged NOT RECOMMENDED for the runnable set (16-24GB estimate,
  zero Trellis2/dinov3 weights actually installed).
- `baseline_hunyuanworldmirror.json` had 3 real schema bugs, found by reading the
  ACTUAL installed node source at
  `C:\Users\jeffr\Documents\ComfyUI\custom_nodes\ComfyUI-HunyuanWorld-Mirror\nodes.py`
  (not just its README): wrong link type (`MODEL` -> `HWMIRROR_MODEL`), two nodes missing
  required widget values (`force_reload=false`, `subsample_factor=1`), and a model path
  that would've triggered an unwanted 5GB HF download instead of using the local file
  (`C:/ComfyUI-Models/WorldMirror-V2/HY-WorldMirror-2.0/model.safetensors`). All fixed.
- `baseline_flux_gen1`: checkpoint filename fixed `flux1-schnell-fp8` -> `flux1-dev-fp8`
  (only Dev is on disk). Caveat left in metadata: KSampler widget values were tuned for
  Schnell (few steps, no CFG) -- adjust for Dev if actually generating output, not just
  comparing node-graph structure.
- `baseline_ltx_2b_v0.9`: checkpoint filename fixed `ltx-video-2b-v0.9.5` -> `v0.9`
  (no ".5" file exists on disk).
- `VIDEO_3D_SUMMARY.md`: corrected a stale "Wan -- NOT Downloaded" line (Wan2.2/Wan2.1
  weights are confirmed present on disk now).

**Explicitly left as open decisions, NOT auto-fixed:**
- `baseline_hunyuan3d_v2_mv.json` points at `hunyuan3d-dit-v2.safetensors` (single-view/
  non-MV checkpoint), but disk only has `hunyuan3d-dit-v2-mv.safetensors`
  (MULTIVIEW-trained). This is a real model-variant mismatch, not a typo -- renaming
  would load MV-trained weights into a non-MV conditioning graph
  (`Hunyuan3Dv2Conditioning`, not `Hunyuan3Dv2ConditioningMultiView`). Two options
  documented in the metadata file: keep as the "official minimal non-mv reference"
  (needs the non-mv checkpoint downloaded separately to actually run), or rebuild from
  the real Hunyuan3D-2mv multiview example workflow to match what's installed. Pick one
  next session.
- `baseline_wan_move.json` needs `Wan21-WanMove_fp8_scaled_e4m3fn_KJ.safetensors` and
  `clip_vision_h.safetensors` -- neither exists on disk (`clip_vision/` folder confirmed
  empty). Blocked on download, not a code fix.

**Production-workflow cross-check** (done this session, drove adding `baseline_humo_17b`):
`otr_scifi_16gb_full.json` (the real production workflow, in the separate
`ComfyUI-OldTimeRadio` repo) actually uses **HuMo** (14B/1.7B variants) as its video
model and FLUX for images -- despite its own description string calling itself a
"FLUX -> HuMo -> LTX cascade," LTX isn't actually wired to anything live. Don't trust
that description string; trust the node widget values.

## State of the art
- `baselines/` (8 JSON + 8 `_metadata.txt` + `FETCH_ISSUES.md` + `KIBITZ_COVERAGE_PLAN.md`)
  is the working set. `FETCH_ISSUES.md` and every metadata file now carry dated
  correction notes for everything fixed this session -- read those before touching any
  JSON again, they explain the "why" behind each value.
- `kibitz-runs/2026-06-30-baseline-5080-coverage/FINAL_JUDGMENT.md` is the authoritative,
  denser technical summary of every panel finding, fix, and open item. Read this first.
- Known stale/overlapping docs, NOT cleaned up this session (flagged by all 3 kibitz
  panelists independently, real confusion risk but out of scope for the coverage check):
  `COMPARATIVE_ANALYSIS.md` and `MODEL_AUDIT.md` still reference the old audio/TTS/LLM
  scope (Kokoro, IndexTTS2, Stable Audio 3, Mistral Nemo) from before the pivot to
  video/3D-only. `RESEARCH_SUMMARY.md` recommends a paid OpenRouter roundtable, which
  contradicts this machine's own "no paid services, 100% local" rule -- flagged, not
  fixed (content decision, not a factual error).

## Immediate next steps
1. Build baselines for the 4 remaining on-disk candidates that still have none:
   - **Wan2.2 TI2V-5B** (`Wan2.2-TI2V-5B-Q5_K_M.gguf`) -- cheapest video model on disk,
     docs claim ~8GB. Official workflow exists at docs.comfy.org/tutorials/video/wan/wan2_2.
   - **Wan2.2 I2V-14B** (`wan2.2_i2v_low_noise_14B_fp8_scaled.safetensors`) -- only the
     low-noise MoE expert is present. Verify whether a single expert produces usable
     output or whether the standard workflow requires both experts (open question below).
   - **Wan2.1-I2V-1.3B** (diffusers-format folder, not a single checkpoint) -- verify
     ComfyUI's (deprecated) DiffusersLoader node can actually wire into Wan's
     conditioning/video nodes before treating this as baseline-ready.
   - **LTX-2.3-distilled-1.1** (GGUF quantized, on disk under `unet/distilled-1.1/`) --
     the actual 5080-appropriate LTX 2.3 variant; partial VRAM numbers already exist in
     `VIDEO_3D_SUMMARY.md` (Q3_K_M ~9.4GB).
2. Resolve the Hunyuan3D-2 checkpoint-mismatch decision (see above) and update that
   baseline accordingly.
3. Once 1-2 are done, move to Phase 3 per the original `CODER_HANDOFF_PROMPT.md`: load
   each baseline in a running ComfyUI instance, confirm no "node not found" errors,
   verify wiring live (not just JSON-level), and flip each metadata file's
   `Verified: NO` to `YES`/specifics.
4. Consider consolidating the 6 overlapping planning docs (`MODEL_AUDIT.md`,
   `VIDEO_3D_MODELS_AUDIT.md`, `RESEARCH_SUMMARY.md`, `BASELINE_HUNT_PLAN.md`,
   `VIDEO_3D_SUMMARY.md`, `KIBITZ_COVERAGE_PLAN.md`) into one source of truth -- flagged
   independently by all 3 kibitz panelists as a real confusion risk, not yet actioned.

## Open questions
- Does the Wan2.2 I2V-14B low-noise-only checkpoint produce usable output alone, or does
  the standard Wan2.2 I2V workflow require both MoE experts (high-noise + low-noise)?
- Can ComfyUI's native/deprecated DiffusersLoader actually load Wan2.1-I2V-1.3B's
  diffusers-format folder and wire into Wan's video-generation node chain, or does it
  need conversion to a single safetensors file first?
- Hunyuan3D-2: keep the non-MV single-view baseline as-is (needs a separate checkpoint
  download to run correctly) or rebuild against the MV multiview example workflow to
  match the checkpoint that's actually installed?

---
## Resume instructions
Open a fresh window, attach this file, and say:
"Read this handoff file and prepare to execute the immediate next steps.
Acknowledge when you're ready to start."
