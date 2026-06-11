# Stage 3: Steering Validation Report

**Date:** 2026-02-26
**Features Tested:** 5
**Total Experiments:** 20
**Universal Features:** 5
**Circuit-Specific Features:** 0

---

## 1. Executive Summary

Cross-circuit frequency shows a **weak negative correlation** (r=-0.292) with steering disruption score.

- Mean amplification change rate: 40.0%
- Mean suppression change rate: 60.0%
- Early layers (L0-L10) mean change: 50.0%
- Late layers (L11+) mean change: 50.0%

## 2. Feature Steering Results

| Feature | Layer | Circuits | Convergence | Amplify Rate | Suppress Rate | Overall | Prob Shift | Universal |
|---------|-------|----------|-------------|-------------|--------------|---------|-----------|-----------|
| L6_F2586668 | L6 | 16 | 84% | 50% | 100% | 75% | +0.0130 | Yes |
| L3_F5150441 | L3 | 23 | 85% | 50% | 50% | 50% | +0.0086 | Yes |
| L0_F1813559 | L0 | 17 | 84% | 50% | 50% | 50% | +0.0252 | Yes |
| L24_F88478228 | L24 | 13 | 72% | 50% | 50% | 50% | +0.0318 | Yes |
| L4_F110446948 | L4 | 22 | 85% | 0% | 50% | 25% | -0.0214 | Yes |

## 3. Detailed Results (Top 10 Features)

### L6_F2586668 (L6, 16 circuits)

| Circuit | Strength | Default Token | Steered Token | Prob Shift | Output Changed |
|---------|----------|---------------|---------------|-----------|----------------|
| the-titanic-sank-in | -20 | `` (46.5%) | `` (48.2%) | +0.0166 | YES |
| the-titanic-sank-in | +20 | `` (46.5%) | `` (40.6%) | -0.0594 | YES |
| the-longest-river-in-africa-is | -20 | `the` (72.7%) | `the` (81.1%) | +0.0833 | YES |
| the-longest-river-in-africa-is | +20 | `the` (72.7%) | `the` (73.9%) | +0.0115 | no |

### L3_F5150441 (L3, 23 circuits)

| Circuit | Strength | Default Token | Steered Token | Prob Shift | Output Changed |
|---------|----------|---------------|---------------|-----------|----------------|
| the-longest-river-in-africa-is | -20 | `the` (72.7%) | `the` (74.8%) | +0.0202 | YES |
| the-longest-river-in-africa-is | +20 | `the` (72.7%) | `the` (73.6%) | +0.0086 | no |
| the-great-fire-of-london-occur | -20 | `` (47.1%) | `` (53.3%) | +0.0626 | no |
| the-great-fire-of-london-occur | +20 | `` (47.1%) | `` (41.4%) | -0.0569 | YES |

### L0_F1813559 (L0, 17 circuits)

| Circuit | Strength | Default Token | Steered Token | Prob Shift | Output Changed |
|---------|----------|---------------|---------------|-----------|----------------|
| the-titanic-sank-in | -20 | `` (46.5%) | `` (46.7%) | +0.0018 | YES |
| the-titanic-sank-in | +20 | `` (46.5%) | `` (46.1%) | -0.0036 | YES |
| the-chemical-symbol-for-sodium | -20 | `Na` (39.2%) | `Na` (44.2%) | +0.0504 | no |
| the-chemical-symbol-for-sodium | +20 | `Na` (39.2%) | `Na` (44.4%) | +0.0521 | no |

### L24_F88478228 (L24, 13 circuits)

| Circuit | Strength | Default Token | Steered Token | Prob Shift | Output Changed |
|---------|----------|---------------|---------------|-----------|----------------|
| world-war-ii-ended-in | -20 | `` (47.2%) | `` (46.7%) | -0.0055 | YES |
| world-war-ii-ended-in | +20 | `` (47.2%) | `` (57.6%) | +0.1041 | YES |
| world-war-i-started-in | -20 | `` (56.1%) | `` (52.7%) | -0.0340 | no |
| world-war-i-started-in | +20 | `` (56.1%) | `` (62.3%) | +0.0624 | no |

### L4_F110446948 (L4, 22 circuits)

| Circuit | Strength | Default Token | Steered Token | Prob Shift | Output Changed |
|---------|----------|---------------|---------------|-----------|----------------|
| the-longest-river-in-africa-is | -20 | `the` (72.7%) | `the` (71.6%) | -0.0113 | no |
| the-longest-river-in-africa-is | +20 | `the` (72.7%) | `the` (73.0%) | +0.0028 | no |
| the-great-fire-of-london-occur | -20 | `` (47.1%) | `` (33.5%) | -0.1356 | YES |
| the-great-fire-of-london-occur | +20 | `` (47.1%) | `` (52.9%) | +0.0585 | no |

## 4. Key Findings

- **1 features** changed output in >50% of experiments
- **0 features** had no steering effect at all
