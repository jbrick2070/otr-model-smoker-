# r2 Synthesis — Coding-Plan Review (Codex panelist + Claude anchor/judge; Antigravity dropped — no response within budget, per operator instruction to not burn its credit)

## Judgment log

**ACCEPTED (grounded, fixed):**
1. LTX 2.3 dev bf16 checkpoint (46.1GB confirmed via file size) does not fit the 5080 — fixed metadata to point at the Q3_K_M GGUF (~9.4GB, per this repo's own prior research) as the actual runnable variant, and flagged the fetched JSON as reference-only. (Codex + Antigravity both independently caught the VRAM mismatch in r1/r2 — high-confidence.)
2. `VIDEO_3D_SUMMARY.md` "Wan — NOT Downloaded" is stale — Wan2.2 TI2V-5B, Wan2.2 I2V-14B (low-noise), and Wan2.1-I2V-1.3B are all confirmed present on disk now. Corrected in place with a dated note.
3. The repo's own established safe-VRAM bar is ~14-15GB sustainable peak, not the raw 16GB card total (this was already written in VIDEO_3D_SUMMARY.md, just not consistently applied). Re-classified Trellis2 (16-24GB, no weights installed) as NOT RECOMMENDED for the runnable set, and HunyuanWorld-Mirror as borderline-only-at-low-frame-counts.
4. HunyuanWorld-Mirror hand-built JSON violates the plan's own "must be stock/official" constraint. Kept the file for reference but explicitly moved it to a separate "improvised/unverified" bucket rather than counting it as equivalent to the 7 fetched-official baselines.

**REJECTED / DOWNGRADED TO VERIFY-AT-BUILD:**
1. Codex's suggestion to rewrite `COMPARATIVE_ANALYSIS.md`'s tables (still audio/TTS/LLM-focused per Antigravity's r1 note) — real, but out of scope for this coverage-gap check; that file predates the video/3D pivot and is a separate cleanup task, not a baseline-coverage gap. Noted here, not actioned.
2. "Add explicit generation parameters (resolution/frames/steps) to every baseline" (Antigravity r1, SHOULD-FIX) — correct for Phase 3 (smoke testing) but out of scope for Phase 1 (fetch baselines), which is what this plan covers. Deferred to the next window per the original handoff doc's own phase split.
3. "Programmatic ComfyUI API runner instead of manual UI testing" (Antigravity r1, SHOULD-FIX) — reasonable but a Phase 3 build decision, not a coverage gap. Deferred.

**VERIFY-AT-BUILD (flagged, not yet checked):**
- Codex's point that node counts aren't comparable across baselines because LTX 2.3 hides 51 nodes in a subgraph vs. others' flat counts — true and already noted in that file's metadata; a full recursive-count pass across all 8 baselines would need a small script (optional, not blocking).
- Dependency-completeness preflight (LoRA/VAE/text-encoder/audio-encoder manifests per baseline) — every metadata file already lists its "Key Nodes" and known limitations, which covers this at a documentation level; an automated preflight script is a nice-to-have, not required to answer "are we missing any published baselines."

## Answer to the actual open question (from KIBITZ_COVERAGE_PLAN.md)

Given everything grounded so far across r1+r2:

- **No additional published/native official baseline was found for any video/3D model that isn't already either (a) baselined, or (b) explicitly listed as a to-do candidate in the plan** (Wan2.2 TI2V-5B, Wan2.2 I2V-14B, Wan2.1-I2V-1.3B, LTX-2.3-distilled-1.1). Both panelists reviewed the candidate list and neither surfaced a model we'd missed entirely — their pushback was about the RUNNABILITY of what's already fetched (LTX 2.3 dev bf16, Trellis2), not about missing coverage.
- The real gap isn't "undiscovered workflows" — it's that 2 of the 8 existing baselines (LTX 2.3 dev, Trellis2) don't actually fit the 5080 as fetched, and 1 (HunyuanWorld-Mirror) isn't a real official baseline at all. Fixed all three in metadata this round.
- The 4 candidates (Wan2.2 TI2V-5B, Wan2.2 I2V-14B, Wan2.1-I2V-1.3B, LTX-2.3-distilled-1.1) remain unbaselined and are the actual next-step work, not a missing-research problem.
