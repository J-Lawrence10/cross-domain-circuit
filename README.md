# Cross-Domain Circuit Analysis of Factual Knowledge Retrieval in LLMs

**Authors:** Joseph Lawrence, Konstantinos Krampis

A circuit-level study of how two open language models, Gemma-2-2B and Qwen3-4B, organize factual knowledge across three domains (chemistry, geography, history). We analyze 60 attribution graphs, introduce traceback graphing as the core analytical method, and validate findings with 80 causal steering experiments.

## Links

- **Paper (PDF):** [docs/papers/pdf/CROSS_DOMAIN_CIRCUIT_PAPER.pdf](docs/papers/pdf/CROSS_DOMAIN_CIRCUIT_PAPER.pdf)
- **Project website:** [https://j-lawrence10.github.io/cross-domain-circuit/](https://j-lawrence10.github.io/cross-domain-circuit/) (after GitHub Pages is enabled)
- **Glossary:** [docs/papers/GLOSSARY.md](docs/papers/GLOSSARY.md)
- **Supplementary materials:** [docs/papers/SUPPLEMENTARY_MATERIALS.md](docs/papers/SUPPLEMENTARY_MATERIALS.md)

## Key findings

1. **Architecture dominates domain.** Within-model layer activation profile similarity is 0.978; between-model is 0.696. Architecture produces approximately 14x the mean cosine gap that knowledge domain does.
2. **Bottleneck depth is architecture-determined.** Gemma bottlenecks sit at L5-7 (about 22% depth); Qwen at L22-25 (about 65%). Both produce comparable factual recall.
3. **The bottleneck penalty.** In Gemma, activation magnitude at the bottleneck layer L6 negatively predicts output confidence (Spearman r = -0.684, Bonferroni-significant). Post-bottleneck layers L13 and L16 positively predict it.
4. **Universal bottleneck features are architecture-specific routing infrastructure.** Six in Gemma, 15 in Qwen, zero overlap. CODE (20%) and LANGUAGE (20%) categories dominate, suggesting routing rather than knowledge storage.
5. **Three-tier causal dissociation.** 80 steering experiments show essential-pathway features produce the strongest distributional perturbations (mean KL = 1.448), but neither pathway topology nor cross-circuit frequency predicts text-level output changes. Circuit redundancy (94.1%) absorbs perturbations; output determinism governs text-level susceptibility.

## Repository layout

```
docs/                                  GitHub Pages source
  index.html                           main paper website
  glossary.html                        glossary with anchor-linked entries
  figures/                             paper figures (SVG and PNG)
  papers/
    CROSS_DOMAIN_CIRCUIT_PAPER.md      paper source
    GLOSSARY.md                        glossary source
    SUPPLEMENTARY_MATERIALS.md
    DATA_AVAILABILITY_AND_EXPERIMENTS.md
    pdf/                               rendered PDFs (regenerable)
scripts/                               Python code
  _hero_style.py                       shared matplotlib style for hero figures
  pipeline_constants.py                model layer counts and shared constants
  3b_traceback_paths.py                core traceback graphing algorithm
  fig_*_hero.py                        the 3 hero figures (bottleneck penalty,
                                       architecture dominance, bottleneck density)
  stage_2_*.py                         per-paper-section figure generators
  generate_pdfs.py                     rebuild the PDFs from the markdown sources
  build_glossary_html.py               rebuild glossary.html from GLOSSARY.md
  build_neuronpedia_urls.py            derive Neuronpedia URLs for feature IDs
  apply_neuronpedia_links.py           hyperlink feature IDs in the HTML
data/                                  analysis result JSONs (small, tracked)
  stage_2_analysis/                    cross-category bottleneck features
  stage_2_layer_energy/                per-layer activation profiles
  stage_2_statistical_deepdive/        the 60-row circuit metric table
plans/                                 development history for the website build
```

## Reproducing the figures

The 3 hero figures and 4 stage-2 figures are reproducible from the analysis JSONs in `data/`:

```bash
pip install -r requirements.txt
cd scripts
python fig_bottleneck_penalty_hero.py
python fig_architecture_dominance_hero.py
python fig_bottleneck_density_hero.py
python stage_2_paper_figures.py
python stage_2_layer_energy_figures.py
python stage_2_expansion_figures.py
python stage_2_d5d7_figures.py
```

Each writes to `docs/figures/` and overwrites the committed SVG/PNG files.

## Reproducing the PDFs

```bash
cd scripts
python generate_pdfs.py
```

Writes to `docs/papers/pdf/`. The website's Paper button links there.

## Reproducing the raw analysis

The raw attribution graphs (60 circuits, ~700 MB) are not included in this repo. They were generated through the Neuronpedia public API. To regenerate from scratch:

1. Obtain a Neuronpedia API key from [https://neuronpedia.org](https://neuronpedia.org).
2. Generate attribution graphs for each prompt in `docs/papers/CROSS_DOMAIN_CIRCUIT_PAPER.md` Appendix A using the `/api/graph/generate` endpoint with parameters described in section 3.1 of the paper.
3. Run `scripts/3b_traceback_paths.py` over each circuit to extract traceback paths and bottleneck features.
4. The downstream analysis JSONs in `data/` are then produced by the pipeline described in section 3.5 of the paper.

## Citation

```bibtex
@article{lawrence2026crossdomain,
  title   = {Cross-Domain Circuit Analysis of Factual Knowledge Retrieval
             in Large Language Models},
  author  = {Lawrence, Joseph and Krampis, Konstantinos},
  year    = {2026}
}
```

## License

MIT. See [LICENSE](LICENSE).
