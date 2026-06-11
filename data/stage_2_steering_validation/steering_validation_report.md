# Stage 2: Steering Prediction Validation Report

**Generated**: 2026-02-28T11:34:36.321881
**Experiments**: 20
**Features with graph metrics**: 0/20

---

## Summary

- **Mean logprob shift**: 0.0448
- **Mean KL divergence**: 0.0275
- **Text change rate**: 50%
- **Best predictor**: avg_convergence -> kl_divergence (r=0.387)

## Graph Metric vs Steering Effect Correlations

| Predictor | mean_lp_shift | max_lp_shift | KL_divergence |
|-----------|--------------|-------------|---------------|
| betweenness | +0.000 | +0.000 | +0.000 |
| in_degree | +0.000 | +0.000 | +0.000 |
| out_degree | +0.000 | +0.000 | +0.000 |
| activation | +0.000 | +0.000 | +0.000 |
| influence | +0.000 | +0.000 | +0.000 |
| pagerank | +0.000 | +0.000 | +0.000 |
| weighted_degree | +0.000 | +0.000 | +0.000 |
| circuits_appeared_in | +0.123 | +0.235 | +0.328 |
| avg_convergence | +0.335 | +0.243 | +0.387 |
| abs_strength | +0.000 | +0.000 | +0.000 |

## Per-Feature Effects

| Feature | Mean Shift | N Experiments |
|---------|-----------|---------------|
| L0_F1813559 | 0.0565 | 4 |
| L24_F88478228 | 0.0197 | 4 |
| L3_F5150441 | 0.0361 | 4 |
| L4_F110446948 | 0.0538 | 4 |
| L6_F2586668 | 0.0578 | 4 |

## Per-Layer Effects

| Layer | Mean Shift | N |
|-------|-----------|---|
| L0 | 0.0565 | 4 |
| L3 | 0.0361 | 4 |
| L4 | 0.0538 | 4 |
| L6 | 0.0578 | 4 |
| L24 | 0.0197 | 4 |

## Interpretation

This analysis tests whether graph-structural metrics can predict
causal intervention effects. A strong correlation between betweenness/
influence and logprob shift would validate the circuit analysis approach.
With only 20 experiments on 6 circuits,
statistical power is limited — this is an initial feasibility test.
