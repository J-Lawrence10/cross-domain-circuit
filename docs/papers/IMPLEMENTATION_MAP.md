# Implementation Map: Paper Claims to Code

**Companion document to:** `CROSS_DOMAIN_CIRCUIT_PAPER.md`
**Scope:** every method, algorithm, statistical test, and figure described in the paper, mapped to its implementation.
**Last updated:** 2026-06-11

This repository was extracted from the larger lab repository (`autocircuit`, directory `neuronpedia_pipeline/`). The analysis pipeline that produced the raw results did not travel here in full: this repo contains the core traceback algorithm, the figure generators, and the result JSONs that the figures read. After the initial extraction, five result datasets were copied in from upstream (`data/stage_2_enhanced_analysis/`, `data/stage_3_steering/`, `data/stage_2_minimal_pathways/`, `data/stage_2_steering_validation/`, `data/stage_2_polysemanticity/`), along with `scripts/path_manager.py`. The rows below reflect that state. Each paper claim is classified as one of:

- **IN THIS REPO**: implemented in `scripts/` here. File, function, and line number given. Line numbers were assigned at the initial commit (`1ad748f`); the post-extraction fixes preserved line numbering, and the numbers cited here were re-checked against the current tree.
- **OUTPUT ONLY**: the computed result is in `data/` here, but the script that computed it lives in the upstream autocircuit repo (`neuronpedia_pipeline/scripts/`). The upstream script name is given where it can be inferred from docstrings or `DATA_AVAILABILITY_AND_EXPERIMENTS.md`.
- **EXTERNAL**: the computation ran on Neuronpedia's servers via their API. Endpoint and parameters given.

A fourth label is used where honesty requires it:

- **NOT IN THIS REPO**: neither the implementing script nor the result JSON travelled into this repository. The claim is traceable only through the upstream repo. These rows are also collected in Section 9 (red flags).

All upstream script names below refer to `neuronpedia_pipeline/scripts/` in the autocircuit repo unless stated otherwise.

**A note on naming.** The paper's prose says "activation magnitude" where earlier drafts said "activation energy", and "bottleneck penalty" where they said "bottleneck tax". Internal names were not renamed: JSON keys (`total_energy`, `layer_energy_fraction`, `cumulative_energy`), directory names (`data/stage_2_layer_energy/`), and most script and figure filenames (`stage_2_layer_energy_figures.py`, `fig19_cumulative_energy`) keep the energy vocabulary. The one filename that did change is the hero figure script, formerly `fig_bottleneck_tax_hero.py`, now `fig_bottleneck_penalty_hero.py` (functions and line numbers unchanged). This map uses the prose terms when quoting paper claims and the key names when pointing at data.

---

## 1. Section 3.1: Attribution Graph Generation

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| Graph generation, 60 circuits (`maxFeatureNodes=3000`, `desiredLogitProb=0.95`, `nodeThreshold=0.8`, `edgeThreshold=0.85`) | EXTERNAL: Neuronpedia `POST /api/graph/generate` | n/a (upstream runner: `run_full_pipeline.py` / script 1 of the per-circuit pipeline) | 30 prompts x 2 models (Appendix A) | Raw graph JSON, upstream `data/prompts/<circuit>/1_generation/` (not in this repo) |
| Graph conversion to pipeline format (nodes with layer, activation, influence, feature_id; weighted edges) | NOT IN THIS REPO (upstream "script 2"; the expected format is documented in `scripts/3b_traceback_paths.py` docstring and `load_converted_graph`, line 175) | n/a | Raw graph JSON | `*_converted_graph.json`, upstream `2_conversion/` (not in this repo) |
| Feature ID convention `L{layer}_F{feature}`; Neuronpedia ID = circuit_tracer_id mod 16384 (Gemma) | IN THIS REPO | `scripts/build_neuronpedia_urls.py`: `build_gemma_url` (40), `classify` (34); constant `GEMMA_SAE_DICT_SIZE` in `scripts/pipeline_constants.py` (14) | Feature IDs scraped from `docs/cross-domain-circuits.html` | `data/neuronpedia_feature_urls.json` (written on demand) |

## 2. Section 3.2: Traceback Graphing Algorithm

The pseudocode in Section 3.2 has two parts: path tracing (steps 1-2) and bottleneck identification (step 3). Only the path tracing is implemented in this repo.

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| Backward BFS with priority queue from top-K output nodes | IN THIS REPO | `scripts/3b_traceback_paths.py`: `backward_bfs_weighted` (335), `extract_critical_paths` (542) | `*_converted_graph.json` (upstream; none shipped here) | `traceback_paths.json` per circuit (upstream `3_analysis/`) |
| Final-layer node ranking by contribution = activation x influence | IN THIS REPO | `compute_final_layer_contributions` (288), `find_final_layer` (266) | same | same |
| Geometric decay: `score(n_p) = activation(n_p) * weight(n_p->n) * score(n)^d`, d = 0.8 | IN THIS REPO | `backward_bfs_weighted`, decay applied at line 533: `new_score = (acc_score ** decay_factor) * base_contribution` | same | same |
| Defaults K=5, max depth 20 | IN THIS REPO | argparse defaults (946, 952); also `max_nodes=30` per path (958), which the paper pseudocode does not mention but which is the de facto stopping criterion | same | same |
| Top-K and bottom-K tracing (validation mode) | IN THIS REPO | `extract_critical_paths`, `bottom_k` branch (646-650); CLI flag `--bottom-k` (960) | same | `traceback_paths_bottom.json` |
| Bottleneck identification: convergence(f) = paths containing f / total paths, threshold >= 0.6 | NOT IN THIS REPO as executable code. The threshold constant is here (`pipeline_constants.py`: `BOTTLENECK_CONVERGENCE_THRESHOLD = 0.6`, line 10) and the computation is shown as a docstring example in `3b_traceback_paths.py` (618-629), but the extraction that produced the bottleneck lists ran upstream (per-circuit analysis script and `stage_2_cross_category_analysis.py`) | n/a | Bottleneck lists inside `data/stage_2_analysis/cross_category_results.json` |

`scripts/path_manager.py` now ships in this repo, so the module-level import at line 172 resolves and `3b_traceback_paths.py` launches cleanly (it previously crashed on import even in `--file` mode).

Known issues with `3b_traceback_paths.py` that remain:

1. The module docstring (line 37) says bottlenecks are features appearing in "80%+ of paths". The paper, `pipeline_constants.py`, and all shipped results use 60%. The docstring is stale.
2. The paper pseudocode says "Add n_p to Q if score exceeds threshold". The code has no score threshold; every unvisited predecessor is enqueued, and path size is bounded by `max_nodes` and `max_depth` instead.

## 3. Section 3.3: Cross-Circuit Comparison

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| Pairwise Jaccard similarity over traceback path features | OUTPUT ONLY (upstream `stage_2_cross_category_analysis.py`) | n/a | Per-circuit `traceback_paths.json` (upstream) | `data/stage_2_analysis/cross_category_results.json`: `within_category[*].jaccard_matrix`, `.avg_jaccard_similarity`, `model_comparison.jaccard_comparison` |
| Shared bottleneck analysis (features bottlenecked in multiple circuits; e.g. 9/10 chemistry) | OUTPUT ONLY (same script) | n/a | same | same file: `within_category[*].shared_bottlenecks`, `.top_bottleneck`, `.consistency_score` |
| Layer distribution of bottlenecks per category | OUTPUT ONLY (same script) | n/a | same | same file: `within_category[*].layer_distribution`, `model_comparison.layer_comparison` |

## 4. Section 3.4: Statistical Framework

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| Permutation tests (N=10,000) for within-vs-cross-category Jaccard | OUTPUT ONLY (upstream enhanced analysis script; not named in shipped docs) | n/a | upstream traceback outputs | `data/stage_2_enhanced_analysis/enhanced_results.json`: `statistical_significance.permutation_tests` |
| Bootstrap 95% CIs (N=10,000) for within-category Jaccard means | OUTPUT ONLY (same upstream script); plotting code is here | `fig9_bootstrap_ci` (514) in `stage_2_paper_figures.py` reads `enhanced.statistical_significance.bootstrap_ci` | `enhanced_results.json` (present) | same file: `statistical_significance.bootstrap_ci` |
| Chi-square tests for layer distribution vs category | OUTPUT ONLY (same upstream script); plotting code is here | `fig15_layer_distribution` (831) reads `enhanced.statistical_significance.chi_square` | same (present) | same file: `statistical_significance.chi_square` |
| ANOVA (Shapiro-Wilk pre-test) / Kruskal-Wallis, 18 tests, alpha = 0.003 | OUTPUT ONLY (upstream `stage_2_statistical_deepdive.py`, inferred name) | plotted by `fig8_anova_effects` (460) in `stage_2_paper_figures.py` | `data/stage_2_statistical_deepdive/data_table.json` | `data/stage_2_statistical_deepdive/statistical_deepdive_results.json`: `anova_kruskal` |
| Mann-Whitney U with rank-biserial correlation, 16 tests | OUTPUT ONLY (same upstream script) | plotted by `fig7_cross_model` (406) | same | same file: `cross_model_comparison.metrics` |
| OLS regression (numpy-based), full and reduced models | OUTPUT ONLY (same upstream script) | plotted by `fig14_regression` (776) | same | same file: `multiple_regression` |
| Cohen's d pairwise category effect sizes | OUTPUT ONLY (same upstream script) | plotted by `fig12_cohens_d` (656) | same | same file: `effect_sizes.pairwise_cohens_d` |
| Spearman correlations of 16 metrics vs confidence, Bonferroni | OUTPUT ONLY (same upstream script) | plotted by `fig6_correlation_heatmap` (342) | same | same file: `expanded_correlation_matrix` |

## 5. Section 3.5: Pipeline Architecture (7 stages)

| Stage | Where implemented | Notes |
|---|---|---|
| 1. Graph generation | EXTERNAL (Neuronpedia `/api/graph/generate`) | see Section 1 above |
| 2. Graph conversion | NOT IN THIS REPO (upstream script 2) | format consumed by `3b_traceback_paths.py:load_converted_graph` (175) |
| 3. Louvain community detection and betweenness centrality | NOT IN THIS REPO (upstream per-circuit script 3; multi-algorithm validation in upstream `stage_2_multi_algorithm_validation.py`) | `networkx` is in `requirements.txt` for this reason, but no shipped script calls the community or centrality APIs. Community metrics survive as columns in `data_table.json` (`num_supernodes`, `size_entropy`) |
| 4. Traceback analysis | IN THIS REPO: `scripts/3b_traceback_paths.py` | see Section 2 above |
| 5. Cross-circuit comparison | OUTPUT ONLY: upstream `stage_2_cross_category_analysis.py` to `data/stage_2_analysis/` | see Section 3 above |
| 6. Statistical analysis (60-row table, ~25 metrics) | OUTPUT ONLY: `data/stage_2_statistical_deepdive/data_table.json` (60 rows, 32 columns including `model`, `category`, `prompt_slug`) plus `statistical_deepdive_results.json` | upstream `stage_2_statistical_deepdive.py` |
| 7. Visualization | IN THIS REPO: `scripts/stage_2_paper_figures.py`, `stage_2_layer_energy_figures.py`, `stage_2_expansion_figures.py`, `stage_2_d5d7_figures.py`, `fig_*_hero.py` | see Section 8 for status; all of these scripts now run end to end |

## 6. Results Sections 5.1-5.6

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| 5.1 Within-category Jaccard table (0.289/0.395/0.108 etc.) | OUTPUT ONLY | values are in `model_comparison.jaccard_comparison`; heatmaps re-rendered by `fig2_jaccard_heatmaps` (125) in `stage_2_paper_figures.py` | `data/stage_2_analysis/cross_category_results.json` | figure `fig2_jaccard_heatmaps` |
| 5.1 Top bottleneck consistency (L0_F1813559 in 10/10, etc.) | OUTPUT ONLY | n/a | same | same file: `within_category[*].top_bottleneck` |
| 5.2 Bottleneck depth by category (Gemma L5.5-6.6, Qwen L22.0-24.6) | IN THIS REPO (recomputable from shipped data) | `fig3_bottleneck_depth` (202) in `stage_2_paper_figures.py` computes the category means from `avg_bottleneck_layer`; `fig_bottleneck_density_hero.py`: `load_avg_bottleneck_layers` (87), `histogram_by_layer` (96) recomputes the per-model distribution and prints means/medians | `data/stage_2_statistical_deepdive/data_table.json` | figures `fig3_bottleneck_depth`, `fig_bottleneck_density_hero` |
| 5.3 Universal bottleneck features (6 Gemma, 15 Qwen, zero overlap) | OUTPUT ONLY | n/a | upstream traceback outputs | `cross_category_results.json`: `cross_category[*].universal_bottlenecks`, `model_comparison.gemma_only_universal`, `.qwen_only_universal`, `.shared_universal_count` (= 0) |
| 5.4 Output vs path Jaccard (4.41x ratio etc.) | OUTPUT ONLY (upstream enhanced analysis script); plotting code is here | `fig10_output_path` (553) reads `enhanced.output_node_sharing` | `data/stage_2_enhanced_analysis/enhanced_results.json` (present) | same file: `output_node_sharing.{gemma-2-2b,qwen3-4b}`; rendered `docs/figures/fig10_output_path.{svg,png}` |
| 5.5.1 Cosine similarity of energy profiles (within 0.978, between 0.696, 14x gap) | OUTPUT ONLY plus an independent in-repo recomputation: `fig_architecture_dominance_hero.py` recomputes all 60x60 pairwise cosines from shipped per-circuit profiles and reproduces the block means | `resample_to_common_depth` (125), `build_similarity_matrix` (133), `block_means` (171); 20-bin depth grid constant `NORM_DEPTH_BINS` (81) mirrors upstream `compute_profile_similarity` in `stage_2_layer_energy_analysis.py` | `data/stage_2_layer_energy/per_circuit_layer_energy.json` | figure `fig_architecture_dominance_hero`; canonical values in `layer_energy_results.json`: `profile_similarity.cross_model.overall` |
| 5.5.1 KS test within vs cross-category; Mann-Whitney on between-model cosines | OUTPUT ONLY (upstream `stage_2_layer_energy_analysis.py`) | n/a | upstream per-circuit graphs | `layer_energy_results.json`: `profile_similarity.{model}.ks_within_vs_cross`, `profile_similarity.cross_model.overall.mann_whitney_p` |
| 5.5.2 Front-loading vs back-loading (54% vs 31%; 50% by L11 vs L28) | OUTPUT ONLY; plotted in-repo | `fig19_cumulative_energy` (255) in `stage_2_layer_energy_figures.py`; `model_cumulative_curve` (98) in `fig_architecture_dominance_hero.py`; early/late split plotted in `fig23_evidence_accumulation_summary` (573) from the `drift_diffusion` key | `layer_energy_results.json`: `cumulative_energy`, `drift_diffusion` | figures `fig19`, hero F2 left panel |
| 5.5.3 Bottleneck penalty: per-layer Spearman r vs confidence, Bonferroni (L6 r = -0.684 etc.) | OUTPUT ONLY for the full per-layer table (upstream `stage_2_layer_energy_analysis.py`); the headline L6 correlation is independently recomputed in this repo | `fig18_layer_confidence_correlation` (190) plots the stored table; `fig_bottleneck_penalty_hero.py`: `load_l6_scatter` (100) and `draw_right_panel` (193) recompute Spearman r for L6 from per-circuit data via `scipy.stats.spearmanr` (195) | `layer_energy_results.json`: `layer_confidence_correlation`; `per_circuit_layer_energy.json` | figures `fig18`, `fig_bottleneck_penalty_hero` |
| 5.5.3 Qwen 0 significant layers | OUTPUT ONLY; visible in `fig18` right panel | `fig18_layer_confidence_correlation` (190) | same | same |
| 5.6.1 OLS regression (R2 = 0.489 reduced model etc.) | OUTPUT ONLY | plotted by `fig14_regression` (776) | `data_table.json` | `statistical_deepdive_results.json`: `multiple_regression.{model}.{full_model,reduced_model}` |
| 5.6.2 Mann-Whitney 13/16 metrics, perfect rank separation | OUTPUT ONLY | plotted by `fig7_cross_model` (406) | same | same file: `cross_model_comparison` |
| 5.6.3 Null results: convergence vs confidence; concentration index | OUTPUT ONLY | n/a | same | same file: `bottleneck_convergence`, `concentration_index`; raw columns `mean_bottleneck_convergence`, `concentration_index` in `data_table.json` |

## 7. Results Sections 5.7-5.9 and Discussion-Only Analyses

| Paper claim | Where implemented | Function (line) | Input data | Output |
|---|---|---|---|---|
| 5.7 Minimal pathway extraction (94.1% redundancy, width 1.1, 0.3% weight) | OUTPUT ONLY (upstream `stage_2_minimal_pathways.py`); plotting code is here: `fig41_reduction_breakdown` (274) and `fig42_bn_vs_retention` (338) in `stage_2_expansion_figures.py` (input path defined at 43) | as listed | `data/stage_2_minimal_pathways/minimal_pathway_results.json` (present, plus `minimal_pathway_report.md`) | same file: `overall.{mean_reduction,mean_bn_width,mean_weight_retention}`; rendered `docs/figures/fig41_reduction_breakdown.{svg,png}` |
| 5.8 Steering experiments (80 total, clamp +/-20, `temperature=0`, `seed=42`, `n_tokens=10`) | EXTERNAL: Neuronpedia `POST /api/steer`. Upstream runners: `5_steering_validation.py` (D4), `stage_2_expanded_steering.py` (D5), `stage_2_essential_pathway_steering.py` (D6), per `DATA_AVAILABILITY_AND_EXPERIMENTS.md` section 6 | n/a | feature lists from the Stage 1.5 bottleneck library and the essential-pathway pool (both upstream) | D4+D5 raw records (50 of the 80 experiments) shipped here: `data/stage_3_steering/steering_results.json` (`metadata.total_experiments` = 50), plus `steering_analysis.json`, `steering_baselines.json`, `steering_checkpoint.json`; the 30 D6 records also ship here (`data/stage_2_essential_pathway_steering/essential_pathway_steering_results.json`), so all 80 experiment records are present |
| 5.8 KL divergence between steered and baseline distributions | OUTPUT ONLY for the published numbers: mean KL = 1.448 is from D6, and the shipped `data/stage_2_essential_pathway_steering/essential_pathway_steering_results.json` carries it directly (`analysis.essential_pathway.mean_kl` = 1.4476, change_rate = 8/30). A truncated re-derivation exists in this repo: `fig45_steering_heatmap` (45) in `stage_2_d5d7_figures.py` recomputes KL from the stored D4+D5 logprobs but only over the top 5 tokens (94-104), so it is an approximation of the paper metric | `fig45_steering_heatmap` (45) | `data/stage_3_steering/steering_results.json` (present) | figure `fig45_d5_steering_heatmap` (PNG shipped, regenerable) |
| 5.8.1 D4 effects (mean logprob shift 0.045, 50% change rate) | OUTPUT ONLY (upstream analysis); plotting code: `fig43_steering_effects` (403), `fig44_convergence_vs_effect` (464) in `stage_2_expansion_figures.py` (input path at 44) | as listed | `data/stage_2_steering_validation/steering_validation_results.json` (present; 20 records in `enriched_results`) | same file: `summary`, `correlations`, `per_layer`, `per_feature`; rendered `docs/figures/fig43_steering_effects.png` |
| 5.8.2 D5 domain susceptibility and direction asymmetry | OUTPUT ONLY for the raw records; `fig46_steering_by_domain` (141) in `stage_2_d5d7_figures.py` recomputes change rates by domain and direction from them | `fig46_steering_by_domain` (141) | `data/stage_3_steering/steering_results.json` (present) | upstream for the published summary numbers; raw records recomputable here |
| 5.8.3 D6 essential-pathway steering, three-tier dissociation | OUTPUT ONLY. Producing script (`stage_2_essential_pathway_steering.py`) is upstream and no D6 figure code ships here, but the full results JSON does | n/a | `data/stage_2_essential_pathway_steering/essential_pathway_steering_results.json` (30 records; `analysis` block holds the three-tier comparison: essential_pathway mean KL 1.4476 and 26.7% change rate vs d5_on_pathway and d5_off_pathway) | shipped |
| 5.9 Format-variation pilot (gold x 3 formats, template vs fact Jaccard) | NOT IN THIS REPO. Graph generation was EXTERNAL (same `/api/graph/generate` endpoint); the Jaccard comparison script and its results did not travel and are not named in the shipped docs | n/a | missing | upstream only |
| 6.1 Edge flow analysis (0.995 similarity, 80-85% skip connections) | NOT IN THIS REPO. Upstream `stage_2_edge_flow/`; no script or data here | n/a | missing | upstream only |
| 6.2 Co-activation modules (1.9x within-layer, 5 clusters) | NOT IN THIS REPO. Upstream `stage_2_coactivation/` | n/a | missing | upstream only |
| 6.2 Polysemanticity classification (12.3% rate, CODE 20%, LANGUAGE 20%) | OUTPUT ONLY (upstream `stage_2_polysemanticity_analysis.py`); plotting code is here: `fig47_semantic_categories` (208), `fig48_layer_polysemanticity` (263) in `stage_2_d5d7_figures.py` | as listed | `data/stage_2_polysemanticity/polysemanticity_results.json` (present, plus `polysemanticity_report.md`) | same file: `overall.{polysemantic_rate,category_counts}`, `layer_analysis`, `feature_classifications` |
| Limitation 7: multi-algorithm community validation (4 methods, Jaccard 0.363) | NOT IN THIS REPO (data missing). Upstream `stage_2_multi_algorithm_validation.py`. Plotting code: `fig39_jaccard_distribution` (161), `fig40_method_pair_matrix` (227) in `stage_2_expansion_figures.py` | as listed | missing | upstream only |

## 8. Figures

The paper embeds 11 figures. The markdown references them at `../figures/*.png` (relative to `docs/papers/`), which resolves to `docs/figures/`; all 11 referenced PNGs exist there, and most figures also ship as SVG. `scripts/generate_pdfs.py` points at the same directory (`FIGURES_DIR`, line 29). All 11 paper figures are regenerable from shipped data.

| Paper figure | Internal name | Generator | Input data | Rendered file in `docs/figures/` | Regenerable here? |
|---|---|---|---|---|---|
| Fig 1 (Jaccard heatmaps) | fig2 | `stage_2_paper_figures.py`: `fig2_jaccard_heatmaps` (125) | `cross_category_results.json` (present) | `fig2_jaccard_heatmaps.{svg,png}` | Yes |
| Fig 2 (bottleneck depth) | fig3 | same file: `fig3_bottleneck_depth` (202) | `data_table.json` (present) | `fig3_bottleneck_depth.{svg,png}` | Yes |
| Fig 3 (output vs path) | fig10 | same file: `fig10_output_path` (553) | `enhanced_results.json` (present) | `fig10_output_path.{svg,png}` | Yes |
| Fig 4 (layer-confidence correlation) | fig18 | `stage_2_layer_energy_figures.py`: `fig18_layer_confidence_correlation` (190) | `layer_energy_results.json` (present) | `fig18_layer_confidence_correlation.{svg,png}` | Yes |
| Fig 5 (cumulative energy) | fig19 | same file: `fig19_cumulative_energy` (255) | same (present) | `fig19_cumulative_energy.{svg,png}` | Yes |
| Fig 6 (profile similarity) | fig20 | same file: `fig20_profile_similarity` (314) | same (present) | `fig20_profile_similarity.{svg,png}` | Yes |
| Fig 7 (cross-model Mann-Whitney) | fig7 | `stage_2_paper_figures.py`: `fig7_cross_model` (406) | `statistical_deepdive_results.json` (present) | `fig7_cross_model.png` | Yes |
| Fig 8 (regression summary) | fig14 | same file: `fig14_regression` (776) | same (present) | `fig14_regression.png` | Yes |
| Fig 9 (minimal pathway reduction) | fig41 | `stage_2_expansion_figures.py`: `fig41_reduction_breakdown` (274) | `minimal_pathway_results.json` (present) | `fig41_reduction_breakdown.{svg,png}` | Yes |
| Fig 10 (steering effects) | fig43 | same file: `fig43_steering_effects` (403) | `steering_validation_results.json` (present) | `fig43_steering_effects.png` | Yes |
| Fig 11 (D5 steering heatmap) | fig45 | `stage_2_d5d7_figures.py`: `fig45_steering_heatmap` (45) | `stage_3_steering/steering_results.json` (present) | `fig45_d5_steering_heatmap.png` | Yes (KL panel is the top-5-token approximation, see Section 7) |

Hero figures (website and PDF, not numbered in the paper body) are fully regenerable from shipped data:

| Figure | Generator | Input data |
|---|---|---|
| Bottleneck penalty hero (`fig_bottleneck_penalty_hero.{svg,png}`) | `fig_bottleneck_penalty_hero.py`: `draw_left_panel` (129), `draw_right_panel` (193), `main` (229) | `layer_energy_results.json`, `per_circuit_layer_energy.json` |
| Architecture dominance hero | `fig_architecture_dominance_hero.py`: `main` (329) | same two files |
| Bottleneck density hero | `fig_bottleneck_density_hero.py`: `main` (197) | `data_table.json` |

Shared figure style: `scripts/_hero_style.py` (`apply_hero_rcparams`, 77; `save_hero_figure`, 109). Other figure functions in `stage_2_paper_figures.py` (fig1, fig4, fig5, fig6, fig8, fig9, fig11, fig12, fig13, fig15) and `stage_2_layer_energy_figures.py` (fig16, fig17, fig21, fig22, fig23) generate supplementary or website figures not embedded in the paper body.

A `NameError` bug from the repo extraction (an undefined `BASE` in the `FIG_DIR` definitions of `stage_2_paper_figures.py`, `stage_2_expansion_figures.py`, and `stage_2_d5d7_figures.py`) has been fixed; all three scripts now define their figure directory from a proper base path and run end to end. `stage_2_expansion_figures.py` skips figures whose input JSON is absent (D1 `stage_2_unused_columns` and D2 `stage_2_multi_algorithm` inputs are still missing, so fig37-fig40 are not generated here).

## 9. Red Flags: Claims Not Traceable Inside This Repo

The following paper claims have neither an implementing script nor a result JSON in this repository. Verifying them requires the upstream autocircuit repo:

1. (Resolved) All 80 steering records now ship: 50 D4+D5 (`data/stage_3_steering/`, `data/stage_2_steering_validation/`) and 30 D6 (`data/stage_2_essential_pathway_steering/`), including the published mean KL = 1.448 and the three-tier comparison statistics.
2. The format-variation pilot, including the Jaccard table in Section 5.9 (no script, no data, no figure).
3. Edge flow analysis (6.1).
4. Co-activation clustering (6.2).
5. Multi-algorithm community validation (limitation 7).
6. Louvain community detection and betweenness centrality (pipeline stage 3); only derived columns survive in `data_table.json`.

Items resolved since the first version of this map (result JSONs copied in from upstream; producing scripts remain upstream, so these are now OUTPUT ONLY): permutation tests, bootstrap CIs, and chi-square tests (3.4, via `enhanced_results.json`); output vs path Jaccard (5.4, same file); minimal pathway extraction (5.7); D4 and D5 steering records, text change rates, and recomputable approximate KL values (5.8.1-5.8.2); polysemanticity classification counts (6.2).

Additional discrepancies noticed between paper and code:

- The paper's traceback pseudocode includes a score threshold for queue admission; the implementation uses node and depth caps instead (Section 2 above).
- The `3b_traceback_paths.py` docstring states an 80% bottleneck threshold; everything else uses 60%.
- The in-repo KL computation (`fig45_steering_heatmap`) truncates to top-5 tokens and is not the exact published KL.
- `data/stage_3_steering/steering_results.json` holds 50 records (20 D4 + 30 D5); the remaining 30 D6 records are in `data/stage_2_essential_pathway_steering/essential_pathway_steering_results.json`, completing the 80.

## 10. What Full Reproduction Requires

To reproduce the paper from scratch, a reader needs, beyond this repo:

1. **Neuronpedia API access** (free key): `POST /api/graph/generate` for the 60 graphs plus 3 format-variation graphs (params in Section 3.1 of the paper), `POST /api/steer` for the 80 steering experiments (`+/-20`, `temperature=0`, `seed=42`, `n_tokens=10`), and the feature explanation API for Gemma annotations.
2. **From the upstream autocircuit repo** (`neuronpedia_pipeline/`): the per-circuit pipeline scripts 1-4 (`run_full_pipeline.py`), `stage_2_cross_category_analysis.py`, `stage_2_statistical_deepdive.py` (inferred name), `stage_2_layer_energy_analysis.py`, `stage_2_minimal_pathways.py`, `stage_2_multi_algorithm_validation.py`, `stage_2_polysemanticity_analysis.py`, the enhanced analysis script behind `enhanced_results.json`, the edge flow, co-activation, and output decomposition scripts, the three steering runners (`5_steering_validation.py`, `stage_2_expanded_steering.py`, `stage_2_essential_pathway_steering.py`), the format-variation pilot script, and the Stage 1.5 bottleneck library (`stage_1_5_bottleneck_library.json`). Note that the outputs of several of these scripts now ship in this repo (Sections 4, 7, 9), so they are needed only for recomputation from scratch, not for inspecting the published numbers. The D6 producing script remains upstream; its results JSON ships here.
3. **About 2 GB of disk** for regenerated per-circuit data (`data/prompts/`), which is gitignored upstream and absent here.

With only this repo and its shipped JSONs, the reader can: run the traceback algorithm on any converted graph, recompute the architecture-dominance cosine matrix and the bottleneck-penalty L6 correlation from per-circuit data, recompute bottleneck depth distributions from `data_table.json`, recompute D4+D5 steering change rates and approximate KL values from the raw records, and regenerate all 11 paper figures plus the three hero figures.
