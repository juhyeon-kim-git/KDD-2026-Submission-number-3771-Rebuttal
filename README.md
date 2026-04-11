# KDD-2026-Submission-number-3771-Rebuttal


# Supplementary Results for Rebuttal

## 1. Real Data: Per-Edge Recovery on Expert-Validated Edges

| Category | Edge | Ours | All Baselines |
|---|---|---|---|
| C1 (within) | event_25 → event_11 | ✓ Recovered | ✗ Not recovered |
| C1 (within) | event_25 → event_18 | ✓ Recovered | ✗ Not recovered |
| C1 (within) | event_25 → event_24 | ✓ Recovered | ✗ Not recovered |
| C1 (within) | event_20 → event_23 | ✓ Recovered | ✗ Not recovered |
| C1→C3 | event_23 → event_19 | ✓ Recovered | ✗ Not recovered |
| C1→C3 | event_23 → event_21 | ✓ Recovered | ✗ Not recovered |
| C1→C3 | event_23 → event_22 | ✓ Recovered | ✗ Not recovered |
| C1→C3 | event_18 → event_19 | ✓ Recovered | ✗ Not recovered |
| C3 (within) | event_6 → event_10 | △ Indirect path | ✗ Not recovered |
| C3 (within) | event_8 → event_6 | △ Indirect path | ✗ Not recovered |
| C3 (operational) | event_6 → event_12 | ✓ Recovered | ✗ Not recovered |
| C3 (operational) | event_10 → event_12 | ✓ Recovered | ✗ Not recovered |
| C4 | event_1 ↔ event_2 | ✓ Recovered | ✗ Not recovered |

**Summary:**

| Method | Edges Recovered | Recall |
|---|---|---|
| **Ours** | **11 direct + 2 indirect** | **84.6% (11/13)** |
| CASCADE | 0 | 0% |
| DiffAN | 0 | 0% |
| Simple Granger | 0 | 0% |
| Point Process | 0 | 0% |
| THP | 0 | 0% |

## 2. Baseline Failure Analysis

All baselines produced coefficients below 0.01 on our data. The failures are structural, not due to insufficient tuning.

| Baseline | Assumption | Why It Fails on Our Data |
|---|---|---|
| CASCADE [16] | Parametric delay distribution | Bimodal delays (sub-second bursts + multi-minute gaps) violate assumed delay patterns |
| DiffAN [75] | i.i.d. tabular data (n×D matrix) | Cannot model temporal dependencies in sequential event data |
| Simple Granger [79] | Fixed-lag, linear, regular sampling | Variable stochastic delays with irregular timestamps |
| Point Process [92] | Parametric Hawkes kernels, linear superposition | Complex non-linear triggering patterns exceed kernel capacity |
| Informer [102] | Discretization into fixed-width bins | Merges causal signals (large bins) or creates sparse matrices (small bins) |

**Additional tuning attempted:**
- CASCADE: tested with multiple distribution families — all near-zero coefficients
- Informer: tested with bin sizes of 1s, 10s, 60s, 300s — all near-zero coefficients

## 3. Neural Granger Causality (NGC) [Tank et al., 2021]

NGC is originally designed for regularly sampled multivariate time series (n×D format), which is structurally incompatible with our asynchronous n×3 event stream. To enable comparison, we adapted NGC to our setting by discretizing the event stream into fixed-width bins. Even with this adaptation, NGC achieved substantially lower performance than our method, confirming that the n×D assumption fundamentally limits performance on event sequence data.


| Size | Precision | Recall | F1 | SHD | FPR |
|---:|---:|---:|---:|---:|---:|
| 10 | 0.3306 ± 0.1011 | 0.2479 ± 0.0758 | 0.2833 ± 0.0867 | 30.1 ± 3.64 | 0.334 ± 0.040 |
| 20 | 0.3151 ± 0.0408 | 0.2419 ± 0.0313 | 0.2737 ± 0.0355 | 127.1 ± 6.21 | 0.334 ± 0.016 |
| 30 | 0.2532 ± 0.0281 | 0.2030 ± 0.0226 | 0.2253 ± 0.0250 | 302.9 ± 9.79 | 0.348 ± 0.011 |



## 4. Data Format Comparison: n×3 vs. n×D

| Aspect | n×D (DYNOTEARS, NGC, etc.) | n×3 (Our Setting) |
|---|---|---|
| Sampling | Regular, synchronized | Irregular, asynchronous |
| Variables | D columns observed simultaneously | Event type encoded in single field |
| Timestamps | Fixed interval | Sub-second to multi-minute range |
| Data structure | Multivariate time series matrix | Single interleaved event stream |

## 5. Training Budget Comparison

| Configuration | Epochs | Batch Size | Training Budget |
|---|---|---|---|
| **Ours** | **100** | **64** | **Smallest** |
| DiffAN / THP / Informer / iTransformer | 2,000 | 512 | 20× more epochs, 8× larger batch |
| CASCADE | 2,000 | — | 20× more epochs |

## 6. Expert Evaluation Protocol

| Dimension | Scale | Acceptance Threshold |
|---|---|---|
| Causal Consistency | 1–5 (1 = not causal, 5 = correct direction) | Average ≥ 4 |
| Issue Relevance | 1–5 (1 = no relation, 5 = directly relevant) | Average ≥ 4 |

- Evaluated by 3 independent experts (network specialist, application specialist, event data administrator)
- Accepted when ≥ 2/3 experts meet threshold on both dimensions
- Cross-validated against real incident cases
- Scoring conducted independently without inter-expert communication

## 7. Empirical Support for Assumption 2 (Ablation)

| Model Variant | vs. Vanilla THP | Implication |
|---|---|---|
| THP + Our Encoder | Consistently higher F1 | Attention encoder captures more predictive information |
| iTransformer | Non-trivial F1 across all sizes | Encoding is sufficient across different likelihood models |
| **Full Model (Ours)** | **Highest F1** | Diffusion best exploits the sufficient encoding |

Each component contributes individually, but their combination produces clear synergy — achieving the highest F1 across all synthetic graph sizes (D ∈ {5, 10, 20, 30}).
