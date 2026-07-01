# Comparative Analysis: OTR vs. Baseline Workflows

**Status:** [DRAFT — pending Phase 2 smoke test results]

---

## Executive Summary

[TBD after baselines are tested]

### Questions to Answer

1. **Is OTR's setup suboptimal?**  
   - Are there unused nodes that add VRAM overhead?
   - Are models being combined unnecessarily (could be staged separately)?

2. **Where are the bottlenecks?**  
   - VRAM peak vs. average utilization
   - Which models contribute most to overhead
   - Serialization points (i.e., where multiple models must coexist in memory)

3. **Optimization opportunities:**
   - Can nodes be pruned without affecting output quality?
   - Should certain models be offloaded to a separate pass?
   - Are there widget/input redundancies that bloat the workflow?

---

## Baseline Workflow Metrics

(To be populated after Phase 2 smoke tests.)

| Model | Baseline Nodes | Baseline Links | Baseline Widgets | Baseline VRAM Peak | Baseline Timing |
|-------|----------------|-----------------|------------------|--------------------|-----------------|
| FLUX Gen1 | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |
| HuMo 1.7B | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |
| IndextTS2 | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |
| Kokoro | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |
| Stable Audio 3 | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |
| Mistral Nemo | [TBD] | [TBD] | [TBD] | [TBD] | [TBD] |

---

## OTR Workflow Metrics (Current)

| Section | Node Count | Link Count | Widget Count | Notes |
|---------|-----------|-----------|--------------|-------|
| Story Writer (1) | 1 | 0 | 30+ | Heavy LLM config |
| Ledger Freeze (1b) | 1 | 6 | 7 | Cascading config |
| Cast Lock (1c) | 1 | 1 | 4 | Registry authority |
| Character Voices (3a) | 1 | 2 | 2 | IndextTS2 dispatch |
| Announcer Voice (3b) | 1 | 2 | 2 | Kokoro dispatch |
| Theme Music (3c) | 1 | 2 | 2 | Stable Audio 3 dispatch |
| Scene Sequencer (4) | 1 | 3 | 2 | Audio layout |
| Audio Enhance (5) | 1 | 1 | 7 | Spatial + EQ |
| Episode Assembler (6) | 1 | 3 | 4 | Mux authority |
| Signal Lost Video (8) | 1 | 2 | 4 | CRT procgen |
| Video Director | 1 | 1 | 20+ | Model policy dispatch |
| Image Director | 1 | 1 | 8 | Granularity control |
| Meta Brief Image Gen (9) | 1 | 3 | 1 | Prompt builder |
| Shot Lock (9a) | 1 | 5 | 1 | Video sync lock |
| Image Dispatcher (9b) | 1 | 3 | 0 | Image routing |
| Video Render Batch | 1 | 3 | 9 | HuMo + engine select |
| Caption Burn (9c) | 1 | 2 | 7 | SDH overlay |
| Silent Composite (10) | 1 | 1 | 8 | FFmpeg mux |
| Master Audio Mux (11) | 1 | 3 | 5 | Final mux |
| Post-Upscale Procgen Blend | 1 | 3 | 13 | Video blending |
| Scene Aware Scopes | 1 | 2 | 5 | Monitoring video |
| Validator | 1 | 1 | 6 | Integrity check |
| **TOTAL** | **22** | **57** | **~180** | |

---

## Overhead Analysis

(To be populated after comparative metrics are collected.)

### VRAM Overhead per Model

```
[Graph will be inserted here]
Y-axis: VRAM (GB)
X-axis: Model
Bars: Baseline vs. OTR
```

### Widget Complexity

| Model | Baseline Widget Count | OTR Widget Count | Delta | Impact |
|-------|----------------------|------------------|-------|--------|
| FLUX Gen1 | [TBD] | [TBD] | [TBD] | [TBD] |
| HuMo 1.7B | [TBD] | [TBD] | [TBD] | [TBD] |
| IndextTS2 | [TBD] | [TBD] | [TBD] | [TBD] |
| Kokoro | [TBD] | [TBD] | [TBD] | [TBD] |
| Stable Audio 3 | [TBD] | [TBD] | [TBD] | [TBD] |
| Mistral Nemo | [TBD] | [TBD] | [TBD] | [TBD] |

---

## Findings & Recommendations

[TBD after analysis complete]

### Top 3 Optimization Opportunities (Pending)

1. [TBD]
2. [TBD]
3. [TBD]

### Nodes to Consider Pruning

[TBD]

### Models to Stage Separately (if testing reveals high VRAM conflicts)

[TBD]

---

## Conclusion

[TBD]

### Is the OTR setup suboptimal?

**Answer:** [TBD]

### Recommended Next Steps

[TBD]

---

## Appendix: Raw Smoke Test Data

(Smoke test logs and full VRAM traces will be appended here.)
