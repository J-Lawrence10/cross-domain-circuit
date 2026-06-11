# Stage 2: Minimal Pathway Extraction Report

**Generated**: 2026-02-28T11:32:07.038408
**Circuits extracted**: 60/60

---

## Overall Results

- **Mean reduction**: 94.1% (+/- 1.2%)
- **Mean bottleneck width**: 1.1
- **Mean path length**: 2.8
- **Mean weight retention**: 0.003 (0.3% of total weight)
- **Mean unique paths**: 300

## Per-Model Breakdown

| Model | N | Reduction% | BN Width | Path Length | Weight Retention | Median BN Layer |
|-------|---|-----------|----------|-------------|-----------------|-----------------|
| gemma-2-2b | 30 | 94.7% | 1.2 | 2.7 | 0.004 | L22 |
| qwen3-4b | 30 | 93.5% | 1.0 | 2.8 | 0.003 | L30 |

## Per-Category Breakdown

| Category | N | Mean Reduction% | Std |
|----------|---|----------------|-----|
| chemistry | 20 | 94.2% | 1.4% |
| geography | 20 | 93.3% | 0.6% |
| history | 20 | 94.8% | 0.6% |

## Individual Circuit Results

| Circuit | Model | Category | Reduction% | BN Layer | BN Width | Paths | Weight Ret |
|---------|-------|----------|-----------|----------|----------|-------|------------|
| world-war-ii-ended-in | gemma-2-2b | history | 96% | L2 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-sodium | gemma-2-2b | chemistry | 96% | L21 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-iron-i | gemma-2-2b | chemistry | 96% | L21 | 1 | 300 | 0.002 |
| america-declared-independence- | qwen3-4b | history | 96% | L33 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-tungst | gemma-2-2b | chemistry | 96% | L23 | 2 | 300 | 0.003 |
| the-chemical-symbol-for-argon- | gemma-2-2b | chemistry | 95% | L23 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-mercur | gemma-2-2b | chemistry | 95% | L25 | 3 | 300 | 0.003 |
| the-chemical-symbol-for-silver | gemma-2-2b | chemistry | 95% | L23 | 1 | 300 | 0.004 |
| the-chemical-symbol-for-copper | gemma-2-2b | chemistry | 95% | L23 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-gold-i | gemma-2-2b | chemistry | 95% | L23 | 1 | 300 | 0.004 |
| the-first-moon-landing-was-in | gemma-2-2b | history | 95% | L25 | 2 | 300 | 0.003 |
| the-chemical-symbol-for-potass | gemma-2-2b | chemistry | 95% | L23 | 2 | 300 | 0.003 |
| the-french-revolution-began-in | gemma-2-2b | history | 95% | L22 | 1 | 300 | 0.003 |
| the-titanic-sank-in | gemma-2-2b | history | 95% | L4 | 1 | 300 | 0.004 |
| world-war-i-started-in | qwen3-4b | history | 95% | L31 | 1 | 300 | 0.002 |
| nelson-mandela-was-released-fr | gemma-2-2b | history | 95% | L23 | 1 | 300 | 0.004 |
| world-war-i-started-in | gemma-2-2b | history | 95% | L4 | 1 | 300 | 0.004 |
| the-berlin-wall-fell-in | gemma-2-2b | history | 95% | L4 | 1 | 300 | 0.003 |
| world-war-ii-ended-in | qwen3-4b | history | 95% | L29 | 1 | 300 | 0.002 |
| the-chemical-symbol-for-lead-i | gemma-2-2b | chemistry | 95% | L23 | 1 | 300 | 0.005 |
| the-great-fire-of-london-occur | gemma-2-2b | history | 95% | L22 | 1 | 300 | 0.003 |
| the-titanic-sank-in | qwen3-4b | history | 95% | L30 | 1 | 300 | 0.001 |
| nelson-mandela-was-released-fr | qwen3-4b | history | 95% | L7 | 1 | 300 | 0.002 |
| the-great-fire-of-london-occur | qwen3-4b | history | 94% | L30 | 1 | 300 | 0.001 |
| the-berlin-wall-fell-in | qwen3-4b | history | 94% | L29 | 1 | 300 | 0.002 |
| columbus-reached-the-americas- | gemma-2-2b | history | 94% | L23 | 1 | 300 | 0.003 |
| the-capital-of-south-korea-is | gemma-2-2b | geography | 94% | L22 | 1 | 300 | 0.005 |
| the-chemical-symbol-for-argon- | qwen3-4b | chemistry | 94% | L4 | 1 | 300 | 0.003 |
| the-french-revolution-began-in | qwen3-4b | history | 94% | L29 | 1 | 300 | 0.001 |
| columbus-reached-the-americas- | qwen3-4b | history | 94% | L29 | 1 | 300 | 0.001 |
| the-capital-of-italy-is | qwen3-4b | geography | 94% | L33 | 1 | 300 | 0.002 |
| america-declared-independence- | gemma-2-2b | history | 94% | L3 | 1 | 300 | 0.005 |
| the-capital-of-france-is | gemma-2-2b | geography | 94% | L22 | 1 | 300 | 0.004 |
| the-chemical-symbol-for-tungst | qwen3-4b | chemistry | 94% | L35 | 1 | 300 | 0.005 |
| the-capital-of-turkey-is | gemma-2-2b | geography | 94% | L25 | 2 | 300 | 0.004 |
| the-capital-of-germany-is | gemma-2-2b | geography | 94% | L22 | 1 | 300 | 0.005 |
| the-first-moon-landing-was-in | qwen3-4b | history | 94% | L30 | 1 | 300 | 0.002 |
| the-capital-of-thailand-is | gemma-2-2b | geography | 94% | L22 | 1 | 300 | 0.005 |
| the-capital-of-egypt-is | gemma-2-2b | geography | 94% | L22 | 1 | 300 | 0.005 |
| the-chemical-symbol-for-mercur | qwen3-4b | chemistry | 94% | L30 | 1 | 300 | 0.003 |
| the-capital-of-spain-is | gemma-2-2b | geography | 93% | L22 | 1 | 300 | 0.005 |
| the-chemical-symbol-for-iron-i | qwen3-4b | chemistry | 93% | L33 | 1 | 300 | 0.003 |
| the-capital-of-japan-is | qwen3-4b | geography | 93% | L33 | 1 | 300 | 0.003 |
| the-capital-of-france-is | qwen3-4b | geography | 93% | L5 | 1 | 300 | 0.003 |
| the-capital-of-japan-is | gemma-2-2b | geography | 93% | L22 | 1 | 300 | 0.005 |
| the-chemical-symbol-for-lead-i | qwen3-4b | chemistry | 93% | L34 | 1 | 300 | 0.003 |
| the-capital-of-italy-is | gemma-2-2b | geography | 93% | L22 | 1 | 300 | 0.005 |
| the-capital-of-germany-is | qwen3-4b | geography | 93% | L33 | 1 | 300 | 0.003 |
| the-capital-of-spain-is | qwen3-4b | geography | 93% | L7 | 1 | 300 | 0.003 |
| the-capital-of-turkey-is | qwen3-4b | geography | 93% | L34 | 1 | 300 | 0.003 |
| the-capital-of-egypt-is | qwen3-4b | geography | 93% | L34 | 1 | 300 | 0.002 |
| the-capital-of-canada-is | gemma-2-2b | geography | 93% | L23 | 1 | 300 | 0.006 |
| the-capital-of-south-korea-is | qwen3-4b | geography | 93% | L33 | 1 | 300 | 0.003 |
| the-capital-of-canada-is | qwen3-4b | geography | 92% | L5 | 1 | 300 | 0.002 |
| the-chemical-symbol-for-sodium | qwen3-4b | chemistry | 92% | L35 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-gold-i | qwen3-4b | chemistry | 92% | L32 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-potass | qwen3-4b | chemistry | 92% | L29 | 1 | 300 | 0.004 |
| the-chemical-symbol-for-silver | qwen3-4b | chemistry | 92% | L33 | 1 | 300 | 0.003 |
| the-chemical-symbol-for-copper | qwen3-4b | chemistry | 92% | L30 | 1 | 300 | 0.004 |
| the-capital-of-thailand-is | qwen3-4b | geography | 92% | L7 | 1 | 300 | 0.004 |
