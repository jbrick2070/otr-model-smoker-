# Kibitz Final Judgment — Baseline 5080 Coverage Check (2026-06-30)

**Panel:** Claude Code (r1, arrived late), Antigravity/Gemini (r1), Codex/GPT (r2).
Arc stopped after r2: all three independent model families converged on the same
three core issues with zero disagreement, which is the arc's own stated exit
condition ("stop at convergence — don't grind passes to hear 'looks good' in
more accents"). r3 (wiring) and r4 (convergence) would add no new information
for a research/coverage audit with no code-wiring component.

## Answer to the original question: "are we missing any published baseline ComfyUI workflows/repos that fit a 5080 or less?"

**No new missing models were found.** All three panelists reviewed the same
candidate list (Wan2.2 TI2V-5B, Wan2.2 I2V-14B, Wan2.1-I2V-1.3B, LTX-2.3-distilled-1.1)
and none surfaced a video/3D model on disk that isn't already either baselined
or on that to-do list. The real problems were with what's already in `baselines/`:

## Fixed this session (all three panelists agreed, independently, on the top 3):

1. **`baseline_ltx2.3_dev_t2v.json` doesn't fit a 5080 as fetched** — the dev
   checkpoint is 46.1GB (bf16, confirmed by file size), needs ~44-46GB VRAM.
   Fixed: metadata now points at the Q3_K_M GGUF (~9.4GB per prior repo
   research) as the actual runnable variant; the fetched JSON is reference-only.

2. **Trellis2 doesn't fit either** — 16-24GB estimate, zero weights actually
   installed. Fixed: marked NOT RECOMMENDED for the runnable/smoke-test set.

3. **HunyuanWorld-Mirror baseline is hand-built, not official** — violates the
   plan's own "stock/official only" rule. Fixed: bucketed separately as
   "improvised/unverified," not counted as equivalent to the 7 real fetched baselines.

## Also fixed (single-panelist catches, verified before acting):

4. `VIDEO_3D_SUMMARY.md` said "Wan — NOT Downloaded" — stale, contradicted by
   the actual disk sweep. Corrected in place. (Codex)

5. The 5080's real safe ceiling per this repo's own prior research is
   ~14-15GB sustainable peak, not the raw 16GB card total. Re-applied that
   stricter bar to Trellis2 and HunyuanWorld-Mirror's VRAM claims. (Codex)

6. Wan2.1-I2V-1.3B is diffusers-format, not a single checkpoint — ComfyUI's
   native Wan path expects UNETLoader-style single files; diffusers folders
   need the separate (deprecated) DiffusersLoader node and may not wire
   cleanly into Wan's conditioning nodes. Flagged as verify-before-baselining,
   confirmed via web search that ComfyUI's diffusers support is real but
   secondary/deprecated. (Claude Code)

7. `RESEARCH_SUMMARY.md` recommends a paid OpenRouter roundtable ($0.50-1.00),
   which directly contradicts this project's own CLAUDE.md rule: "No cloud
   services, no API keys, no paid services. 100% local, open source,
   offline-first." Flagged for Jeffrey — not fixed automatically since it's a
   content/scope decision, not a factual error. (Claude Code)

## Not fixed (out of scope for a coverage check, noted for later):

- `COMPARATIVE_ANALYSIS.md` and `MODEL_AUDIT.md` still reference the old
  audio/TTS/LLM scope (Kokoro, IndexTTS2, Stable Audio 3, Mistral Nemo) from
  before the pivot to video/3D-only. All three panelists flagged this
  independently. Real cleanup debt, not a baseline-coverage gap — leaving for
  Jeffrey to decide whether to update or retire those files.
- Six overlapping planning docs exist (MODEL_AUDIT.md, VIDEO_3D_MODELS_AUDIT.md,
  RESEARCH_SUMMARY.md, BASELINE_HUNT_PLAN.md, VIDEO_3D_SUMMARY.md,
  KIBITZ_COVERAGE_PLAN.md) with no single source of truth. Worth consolidating
  at some point (Claude Code's observation).
