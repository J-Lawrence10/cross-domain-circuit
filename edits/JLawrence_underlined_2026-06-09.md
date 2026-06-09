**Round of comments on date:** 2026-06-09

---

### "model architecture explains roughly 14× more variance in circuit structure than knowledge domain"*

The research computed cosine similarity between ***layer energy fraction profiles*** (energy at each layer as a proportion of total circuit energy) for all pairs of circuits. 

---

### Undefined "traceback graphing"*

Traceback graphing is the core analytical method introduced in this paper. It is defined as a backward breadth-first search (BFS) that starts from the highest-contributing output nodes in an attribution graph and walks backward through the network, scoring predecessors by activation × edge weight × a geometrically decayed downstream score (decay factor 0.8). The**<u>* geometric decay*</u>** prevents score overflow across 20+ layers while preserving relative path ranking. The algorithm traces critical paths from output predictions back to input features and identifies bottleneck features where 60% or more of traced paths converge. The **<u>*traceback graph pipeline *</u>

---

### Undefined: *"bottleneck tax"* and *"bottleneck-layer activation to lower confidence"*

The**<u>* "bottleneck tax"*</u>** is the term coined for the observation that heavier activation concentration at the bottleneck layer predicts lower downstream output confidence.It is important to note that this is a correlation, not a proven mechanistic causal claim; "energy" here is shorthand for summed feature activation magnitudes, not a conserved physical quantity.

---

### Undefined: *"three-tier dissociation"*

The three-tier dissociation is the central causal finding from the 80 steering experiments: pathway topology, cross-circuit frequency, and text-level steerability each measure fundamentally different things and decouple from one another. Tier 1 (<u>***essential-pathway topology)***</u> governs distributional perturbation strength — features on the minimal viable pathways produce the strongest probability-distribution shifts, with mean KL divergence = 1.448 — but this does not reliably translate to text-level output changes. Tier 2 (***<u>cross-circuit frequency)</u>*** identifies statistically common features, but high frequency does not imply high causal influence; the most frequent feature (appearing in 11 circuits) produced zero text changes when steered. Tier 3**<u>* (output determinism by domain) *</u>** governs whether steering actually changes the output token: chemistry circuits are most resistant (0% text change rate) because outputs like Na, Fe, P





---

### Undefined: *"Per-layer activation energy profiling"*

Per-layer activation energy profiling is the quantitative method used to characterize and compare how computation is distributed across the depth of a model's layers within attribution circuits. For each circuit, the "activation energy" at each layer is computed as the summed magnitude of feature activations at that layer within the circuit, then expressed as a fraction of the **<u>*circuit's total activation energy across all layers.*</u>**

---

### Undefined: *"architecture dominance,"*

"Architecture dominance" is the term coined for the empirical observation that within-model energy profile similarity (0.978) clusters roughly 14× more tightly than between-model similarity (0.696), with knowledge domain modulating the profile by less than 2%. 

---

### Undefined: *"universal bottleneck features (features serving as bottlenecks across all three knowledge domains): six in Gemma and 15 in Qwen, with zero overlap between architectures. Their dominant categories (code, language) suggest a routing-infrastructure function rather than a knowledge-storage one."*

**<u>*Universal bottleneck*</u>** features are SAE (Sparse Autoencoder) features that serve as bottlenecks — nodes through which ≥60% of traced paths converge — across circuits from all three knowledge domains simultaneously (chemistry, geography, and history). .... **<u>*Co-activation analysis*</u>** confirms this: same-layer co-activation among universal features is 1.9× stronger than cross-layer, and hierarchical clustering identifies 5 distinct functional modules, suggesting a structured routing system rather than a random collection of individually useful features.

---

### Undefined: *"activation energy"*

In this paper, "activation energy" is used as **<u>*shorthand for the summed magnitudes of SAE feature activations within a circuit at a given layer*</u>** — it is explicitly not a conserved physical quantity in the thermodynamic sense. For any given circuit and layer, activation energy = Σ |activation(feature_i)| for all features i at that layer participating in the circuit. 



---

### Undefined: *"Causal validation via 80 steering experiments reveals a three-tier dissociation"*

Causal validation here refers to the use of **<u>*activation steering experiments *</u>**— directly amplifying or suppressing specific SAE features at specific strengths (±20) during Gemma's forward pass — to test whether structural properties identified in attribution graphs (bottleneck membership, cross-circuit frequency, essential-pathway position) actually predict behavioral causal influence. 

---

## Introduction

### 1.1 Motivation

---

### Undefined: *"When a model predicts that the chemical symbol for gold is 'Au' or that the capital of Japan is 'Tokyo,' which internal features are responsible?"*

**<u>*This question motivates the entire research program of mechanistic interpretability as applied to factual knowledge retrieval. *</u>**

---

### 1.2 Research Questions

---

### Undefined: *"Within-domain convergence."* (Research Question 1)

The within-domain convergence question asks: do different prompts querying the same type of knowledge share circuit architecture?

---

### Undefined: *"Cross-domain divergence."* → annotation: *"Universal features"* (Research Question 2)

The cross-domain divergence question asks whether different knowledge types use different circuit features or layer depths. T

---

### Undefined: *"Universal bottlenecks."*

The **<u>*universal bottlenecks question asks whether there are features that participate in circuits across all knowledge domains*</u>**. 

--

---

### Undefined: *"statistical validation via permutation tests (p < 0.0001)"* (Contribution 1: Systematic cross-domain circuit analysis)

The permutation test validation addresses the concern that observed within-domain circuit similarities<u>*** (Jaccard scores of 0.108–0.395) ***</u>might arise by chance from large attribution graphs containing 1,000–1,500 nodes. In permutation testing, circuit membership labels are randomly shuffled thousands of times and Jaccard similarities are recomputed each time, generating a null distribution of similarities expected under the hypothesis of no real structure. The p < 0.0001 result means the observed within-domain similarities exceed the 99.99th percentile of the null distribution, confirming that the circuit sharing is statistically real and not an artifact of graph size or feature set overlap. This makes the systematic cross-domain circuit analysis the first large-scale statistically validated comparison of attribution circuits across three knowledge domains and two model architectures (60 circuits total).

-

---

### Undefined: *"Cross-model Mann–Whitney tests confirm 13/16 structural metrics differ significantly (Bonferroni-corrected)"* (Contribution 2 continued)

The<u>*** Mann–Whitney test ***</u>is a non-parametric test that compares the distributions of a metric across two independent groups without assuming normality. Here, 16 structural circuit metrics (including bottleneck layer depth, energy profile shape, node counts, edge density, etc.) were each compared between the Gemma circuit distribution (n=30) and the Qwen circuit distribution (n=30). After Bonferroni correction for multiple comparisons (adjusting the significance threshold to account for 16 simultaneous tests), **<u>*13 of the 16 metrics *</u>**differ significantly between architectures. Bonferroni correction is conservative — it divides the α threshold by the number of tests — so finding 13/16 metrics significant 

### Undefined: *"Confidence–energetics model."* (Contribution 5)

The confidence–energetics model is a multiple regression model that predicts Gemma's per-circuit output prediction confidence from three circuit-level structural predictors: (1) total activation energy in the circuit, (2) edge weight kurtosis (a measure of how "spiky" or concentrated the edge weight distribution is), and (3) circuit size (number of nodes). Together, these three predictors explain 49% of the variance in prediction confidence (F(3,26) = 8.30, p < 0.001). 

---

### Undefined: *"A three-predictor regression model "*

This result establishes the **<u>*confidence–energetics model *</u>**quantitatively.....The non-significance of bottleneck position and path convergence as additional predictors is a substantive negative result: it rules out the intuitive hypothesis that circuits whose bottleneck features are in "better" positions (or where paths converge more tightly) produce more confident outputs. Instead, confident predictions arise from overall energetic richness (high total activation energy),<u>*** concentrated edge weight topology (high kurtosis)***</u>, and sufficient circuit complexity — consistent with the interpretation that confidence reflects the breadth and richness of evidence accumulation across the circuit.

---

### Undefined : *"Three-tier causal dissociation."*

### Undefined: *"produce strong distributional perturbations (mean KL = 1.448) , and output determinism governs steering susceptibility"*

This is the paper's capstone causal finding. The KL divergence of 1.448 for essential-pathway features is substantially higher than for other feature categories, confirming that position on the minimal viable circuit pathway predicts how strongly a feature can perturb the model's output probability distribution. However, this strong distributional perturbation does not translate into text-level output changes because 94.1% of circuit nodes are non-essential: the model has so many redundant parallel pathways to the same answer that even significant perturbations to the essential path are compensated before the **<u>*argmax token changes. **</u>*

---

## Methodology

---

### *"breadth-first search from the top-K output nodes that traces critical paths through intermediate layers and identifies bottleneck features where 60%+ of paths converge"*

The backward breadth-first search (BFS) is the algorithmic engine of the ***<u>traceback graphing method</u>***. "Backward" means it traverses edges in reverse — from output toward input — rather than the forward direction of inference. "From the top-K output nodes" means the search seeds from the K *<u>**highest-activation**</u>* features at the final layer of the attribution graph, which are the features most directly responsible for the predicted token. At each backward step, predecessors are <u>***scored and queued for expansion***</u>. ---**
