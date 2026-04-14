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


## 4. Causal Discovery Performance Comparison

### Size = 5

| Method | F1 ↑ | Precision ↑ | TPR (Recall) ↑ | FDR ↓ |
|:-------|:----:|:----------:|:-------------:|:----:|
| THP | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | - |
| iTransformer | 0.133 ± 0.199 | 0.200 ± 0.299 | 0.133 ± 0.150 | 0.800 |
| THP + Our Encoder | 0.154 ± 0.149 | 0.122 ± 0.120 | 0.213 ± 0.203 | 0.878 |
| **Ours** | **0.283 ± 0.107** | **0.183 ± 0.068** | **0.650 ± 0.286** | **0.817** |

### Size = 10

| Method | F1 ↑ | Precision ↑ | TPR (Recall) ↑ | FDR ↓ |
|:-------|:----:|:----------:|:-------------:|:----:|
| THP | 0.131 ± 0.014 | 0.337 ± 0.043 | 0.081 ± 0.009 | 0.663 |
| iTransformer | 0.173 ± 0.082 | 0.317 ± 0.150 | 0.173 ± 0.056 | 0.683 |
| THP + Our Encoder | 0.199 ± 0.079 | 0.199 ± 0.076 | 0.202 ± 0.090 | 0.801 |
| **Ours** | **0.319 ± 0.073** | **0.239 ± 0.051** | **0.490 ± 0.151** | **0.761** |

### Size = 20

| Method | F1 ↑ | Precision ↑ | TPR (Recall) ↑ | FDR ↓ |
|:-------|:----:|:----------:|:-------------:|:----:|
| THP | 0.080 ± 0.012 | 0.351 ± 0.036 | 0.045 ± 0.007 | 0.649 |
| iTransformer | 0.162 ± 0.078 | 0.292 ± 0.141 | 0.162 ± 0.054 | 0.708 |
| THP + Our Encoder | 0.247 ± 0.074 | 0.240 ± 0.041 | 0.270 ± 0.106 | 0.760 |
| **Ours** | **0.329 ± 0.033** | **0.240 ± 0.023** | **0.533 ± 0.086** | **0.760** |

### Size = 30

| Method | F1 ↑ | Precision ↑ | TPR (Recall) ↑ | FDR ↓ |
|:-------|:----:|:----------:|:-------------:|:----:|
| THP | 0.072 ± 0.000 | 0.284 ± 0.004 | 0.041 ± 0.000 | 0.716 |
| iTransformer | 0.117 ± 0.051 | 0.204 ± 0.089 | 0.117 ± 0.036 | 0.796 |
| THP + Our Encoder | 0.287 ± 0.048 | 0.238 ± 0.022 | 0.373 ± 0.077 | 0.762 |
| **Ours** | **0.337 ± 0.037** | **0.245 ± 0.025** | **0.546 ± 0.086** | **0.755** |

### Summary (All Sizes)

| Size | Method | F1 | Precision | TPR | FDR |
|:----:|:-------|:--:|:---------:|:---:|:---:|
| 5 | THP | 0.000 | 0.000 | 0.000 | - |
| 5 | iTransformer | 0.133 | 0.200 | 0.133 | 0.800 |
| 5 | THP + Our Encoder | 0.154 | 0.122 | 0.213 | 0.878 |
| 5 | **Ours** | **0.283** | 0.183 | **0.650** | 0.817 |
| 10 | THP | 0.131 | **0.337** | 0.081 | **0.663** |
| 10 | iTransformer | 0.173 | 0.317 | 0.173 | 0.683 |
| 10 | THP + Our Encoder | 0.199 | 0.199 | 0.202 | 0.801 |
| 10 | **Ours** | **0.319** | 0.239 | **0.490** | 0.761 |
| 20 | THP | 0.080 | **0.351** | 0.045 | **0.649** |
| 20 | iTransformer | 0.162 | 0.292 | 0.162 | 0.708 |
| 20 | THP + Our Encoder | 0.247 | 0.240 | 0.270 | 0.760 |
| 20 | **Ours** | **0.329** | 0.240 | **0.533** | 0.760 |
| 30 | THP | 0.072 | **0.284** | 0.041 | **0.716** |
| 30 | iTransformer | 0.117 | 0.204 | 0.117 | 0.796 |
| 30 | THP + Our Encoder | 0.287 | 0.238 | 0.373 | 0.762 |
| 30 | **Ours** | **0.337** | 0.245 | **0.546** | 0.755 |

> **Bold** = best per size for F1 and TPR; best (lowest) FDR and highest Precision also bolded.  
> FDR = 1 − Precision. ↑ = higher is better, ↓ = lower is better.


## 5. Data Format Comparison: n×3 vs. n×D

| Aspect | n×D (DYNOTEARS, NGC, etc.) | n×3 (Our Setting) |
|---|---|---|
| Sampling | Regular, synchronized | Irregular, asynchronous |
| Variables | D columns observed simultaneously | Event type encoded in single field |
| Timestamps | Fixed interval | Sub-second to multi-minute range |
| Data structure | Multivariate time series matrix | Single interleaved event stream |

## 6. Training Budget Comparison

| Configuration | Epochs | Batch Size | Training Budget |
|---|---|---|---|
| **Ours** | **100** | **64** | **Smallest** |
| DiffAN / THP / Informer / iTransformer | 2,000 | 512 | 20× more epochs, 8× larger batch |
| CASCADE | 2,000 | — | 20× more epochs |

## 7. Expert Evaluation Protocol

| Dimension | Scale | Acceptance Threshold |
|---|---|---|
| Causal Consistency | 1–5 (1 = not causal, 5 = correct direction) | Average ≥ 4 |
| Issue Relevance | 1–5 (1 = no relation, 5 = directly relevant) | Average ≥ 4 |

- Evaluated by 3 independent experts (network specialist, application specialist, event data administrator)
- Accepted when ≥ 2/3 experts meet threshold on both dimensions
- Cross-validated against real incident cases
- Scoring conducted independently without inter-expert communication

## 8. Empirical Support for Assumption 2 (Ablation)

| Model Variant | vs. Vanilla THP | Implication |
|---|---|---|
| THP + Our Encoder | Consistently higher F1 | Attention encoder captures more predictive information |
| iTransformer | Non-trivial F1 across all sizes | Encoding is sufficient across different likelihood models |
| **Full Model (Ours)** | **Highest F1** | Diffusion best exploits the sufficient encoding |

Each component contributes individually, but their combination produces clear synergy — achieving the highest F1 across all synthetic graph sizes (D ∈ {5, 10, 20, 30}).
