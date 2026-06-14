# Data Scientist | Vanguard — Regulatory Ops & Hyper-Personalization
**August 2024 - February 2026**


**Focus Areas:** Graph Neural Networks (GNN), Natural Language Processing (NLP), Multi-Task Deep Learning, Embedding Architectures, Regulatory Compliance Automation, Generative-AI-Powered Data Augmentation

---

### Project Portfolio

#### 1. Marketing History Graph Encoder & Aggregate Client Embeddings (Graph Neural Networks)
*Architected a dual-stage Graph Neural Network pipeline that encodes complete client marketing impression histories into dense, temporally-aware embeddings powering Vanguard's "Next Best Action" Decision Engine.*

##### ⚙️ The Challenge & Business Context
Vanguard's Marketing Technology team operates an XGBoost-driven "Next Best Action Decision Engine" trained on 2,000+ features (client personas, demographics, asset holdings, account structures) that recommends optimal marketing actions across product offers, channels, and placements. An internal audit revealed two critical structural blind spots:
* **Offer Impression Fatigue:** Clients were repeatedly exposed to identical offers because the model had zero structural awareness of their full historical interaction record.
* **Missing Temporal Context:** The decision engine had no notion of *when* a client had previously seen an offer, severely limiting timing optimization and saturation prevention.

The solution had to encode highly variable client interaction histories (single-touch first-engagers up to 500+ impression power-users) into compact, dense vectors that could be ingested by the existing XGBoost framework without breaking the production architecture.

##### 🛠️ Architectural Implementation
1. **Bipartite Graph Schema Design:** Engineered a heterogeneous graph where feature-free client nodes connect to impression nodes carrying offer attributes (offer name, theme classification, offer value, outcome: clicked/converted/ignored). Edges encode channel, placement, and temporal recency via **exponential decay scoring** based on days-since-exposure. Duplicate offer exposures persist as distinct nodes — preserving variance through edge-level temporal metadata.

2. **Model 1 — Impression Embedding Encoder (16-dim):** Constructed a 2-layer **TransformerConv** GNN backbone with 4 attention heads, LayerNorm, and residual connections. Final embeddings fuse three sub-spaces:
   * **8-dim** node embeddings from GNN message passing over offer attributes
   * **4-dim** categorical edge embeddings (channel + placement)
   * **4-dim** temporal embeddings (log-transformed days since event)

   Trained with **6 simultaneous reconstruction objectives** — categorical heads (outcome, theme, offer, channel, placement) using cross-entropy with inverse-frequency class weighting; continuous head (days-since-event) using Smooth L1 loss — across 300 epochs with early stopping driven by validation MAE on the temporal reconstruction task.

3. **Model 2 — Client Aggregation Model (128-dim):** Built a multi-head attention aggregator featuring **three specialized heads**:
   * **Recency Head** — Exponential decay weighting prioritizing the freshest impressions
   * **Frequency Head** — Learned attention over temporal interaction patterns
   * **Context Head** — Content-based attention over offer diversity

4. **Dynamic Cohort-Aware Aggregation:** Designed an adaptive pipeline that gracefully handles the full spectrum of client histories without overfitting sparse clients or losing signal in dense ones:
   * **Single-impression clients** routed through a dedicated bypass MLP to avoid degenerate attention softmax collapse
   * **Low-frequency clients (2–30 impressions)** receive full multi-head attention with temporal decay
   * **High-frequency clients (30+)** processed via **stratified temporal sampling** (10 most recent + 10 oldest + 10 random middle impressions) for attention computation, while mean-pooling **ALL** impressions for the parallel simple path — preserving both recent engagement signals AND complete historical context

5. **Learned Gated Fusion Mechanism:** Rather than hard-coded thresholds, engineered a frequency-adaptive **learned gate** that dynamically blends the attention path with the mean-pooled simple path. Through training, the network discovers that high-frequency clients benefit more from attention (gate → 1) while low-frequency clients lean on the simple path (gate → 0.5–0.7), producing a smooth cohort-aware blending policy.

6. **Decision-Engine-Aligned Multi-Task Supervision:** Trained the aggregator across **8 supervised auxiliary tasks** carefully mapped to downstream Decision Engine objectives:

    | Category | Task | Type | Loss |
    | :--- | :--- | :--- | :--- |
    | Recency | Recent impression ratio | Regression | MSE |
    | Recency | Engagement recency class (5 classes) | Classification | Weighted CE |
    | Frequency | Temporal spread (std of days) | Regression | Smooth L1 (log) |
    | Frequency | Engagement pattern (burst/periodic/sporadic) | Classification | Weighted CE |
    | Diversity | Offer diversity index (entropy-based) | Regression | MSE |
    | Diversity | Diversity pattern classification | Classification | Weighted CE |
    | Context | Most common theme | Classification | CE |
    | Context | Most recent offer/channel | Classification | CE |

7. **Imbalance-Robust Training Stack:** Applied **effective-number class weighting (β=0.999)** for imbalanced classification heads, **mixed-precision training (AMP) with GradScaler** for memory efficiency, and a **ReduceLROnPlateau scheduler** (factor=0.5, patience=50) across 1,750 epochs of task-weighted composite-loss optimization.

8. **Stratified Validation Diagnostics:** Tracked all metrics separately for single-impression vs. multi-impression cohorts (regression MAE / R² / Pearson; classification per-class accuracy, confusion matrices, Top-K accuracy for high-cardinality targets like offer name) and visualized learned gate values against impression counts to verify adaptive blending behavior. Aggregated all task scores into a single **RL Readiness Score** to track production fitness over training.

##### 📈 Impact & Business Value
* **Decision Engine Augmentation:** Delivered production-ready 128-dim client embeddings encoding complete marketing histories — enabling the XGBoost Decision Engine to incorporate impression fatigue signals, temporal patterns, and channel/placement context directly into Next Best Action recommendations.
* **Unlocked New Predictive Surface:** Enabled future modeling of optimal *timing* recommendations in addition to existing offer/channel/placement targets — a previously inaccessible axis of personalization.
* **Cohort-Robust Performance:** Achieved consistent embedding quality across both single-touch and high-engagement clients, validated through stratified diagnostics rather than aggregate metrics that would have hidden cohort-level failures.

##### 💻 Tech Stack
`Python` • `PyTorch` • `PyTorch Geometric` • `TransformerConv` • `torch_scatter` • `Multi-Head Attention` • `Pandas` • `NumPy` • `AWS SageMaker`

---

#### 2. FINRA Regulatory Complaint Detection (NLP & Sequential Ensemble Modeling)
*Diagnosed semantic blind spots in a production RoBERTa text classifier and architected a precision-targeted secondary XGBoost ensemble that elevated regulatory complaint recall to 100% while simultaneously reducing false positives.*

##### ⚙️ The Challenge & Business Context
The Complaints-at-Scale team relies on a binary text classifier to identify regulatory complaints within raw client feedback for mandatory logging and reporting to FINRA and other regulatory bodies. An internal audit of the production fine-tuned **RoBERTa** model exposed critical classification weaknesses on specific complaint subtypes and rare linguistic patterns — a high-stakes gap, since missed complaints carry direct regulatory exposure. The challenge was to surgically remediate the model's failure modes **without retraining or destabilizing** the production primary model.

##### 🛠️ Architectural Implementation
1. **Embedding-Space Diagnosis via PCA + Andrews Curves:** Sampled production model outputs and bucketed them into three populations: (a) high-confidence true positives, (b) low-confidence false negatives, and (c) true negatives. Extracted text embeddings, applied PCA dimensionality reduction, and rendered **Andrews curves** for visual signature analysis. The visualization revealed that false negatives carried distinct semantic waveforms unlike the consistent pattern shared by true positives — proving the model had failed to internalize long-tail variant patterns due to training data imbalance and high intra-class variance among rare complaint subtypes.

2. **Semantic Pattern Decomposition (`ComplaintFeatureExtractor`):** Engineered a comprehensive feature extraction pipeline that mathematically characterizes the false-negative subspace, capturing:
   * **Lexical Features:** word count, average word length, sentence count, repetition ratio
   * **Syntactic Features:** Full part-of-speech distribution across 19 POS tags, lexical density (content vs function words), sentence complexity, referencing density, coreference structure (via **`coreferee`**)
   * **Semantic Features:** Sentiment polarity & subjectivity (TextBlob), emotional tone scores — positive/negative/neutral (VADER), named entity density, characterization (PERSON entity ratio)
   * **Topic Distribution:** **NMF topic modeling** (8 components) over a fitted TF-IDF vectorizer trained across the full corpus
   * **Regulatory Keyword Matching:** Custom dictionary of regulatory trigger terms (FINRA, SEC, attorney, fraud, theft, stole, scam, misinform, etc.)
   * **Interaction Features:** Engineered crosses such as `polarity × tone_interaction`, `entity_density × word_count`, `keyword_match × polarity`

3. **Deep Feature Synthesis at Scale:** Leveraged the **`featuretools`** package with deep feature synthesis (`mean`, `sum`, `max`, `min`, `std`, `median` aggregation primitives + `divide_numeric`, `multiply_numeric`, `modulo_numeric` transform primitives) to combinatorially generate hundreds of interaction features. Filtered out columns with null/inf values, then collapsed the candidate space via **statistical significance testing** to the top 150 separating features.

4. **Recursive Feature Elimination (RFE):** Ran XGBoost-backed RFE across the candidate pool to crystallize the **top 50 most discriminative features** specifically optimized for the borderline classification range — where false negatives concentrate.

5. **Confidence-Range-Targeted Secondary Classifier:** Rather than retraining the production RoBERTa primary model, identified the precise quantile prediction range `[0.12 → 0.393]` where false negatives concentrate, and deployed a specialized **XGBoost** secondary classifier exclusively over this borderline window. Predictions outside this range continue to be served directly by the primary model — preserving its proven performance on high-confidence cases and eliminating deployment risk.

6. **Generative Data Augmentation via Amazon Bedrock:** Invoked generative LLMs through **AWS Bedrock**, prompting them with extracted semantic fingerprints of the false-negative cohort (word count distributions, sentiment ranges, lexical diversity profiles, POS structure) to synthetically generate statistically representative training samples — effectively closing the long-tail data imbalance that originally crippled the primary model.

7. **Multi-Strategy In-House Augmentation Engine:** Built a parallel augmentation engine applying six independent transformation strategies per positive sample (each run twice):
   * Sentence shuffling
   * **WordNet-based synonym replacement**
   * Random word insertion
   * Random word deletion
   * **Negative-sample sentence injection** (interleaving non-complaint sentences into positive samples to teach the model robustness against benign distractor content)
   * Identity preservation of the original
   
   Result: a ~12x amplification of the positive class to combat severe imbalance, with each augmented sample flagged for downstream sample weighting.

8. **Class-Aware Sample Weighting & Stratified Resampling:** Implemented oversampling the minority class to 2.5x its native size, calibrated downsampling of the majority class to 0.8375x the oversampled minority, plus weighted training where positives received **2.25x** weight and augmented positives received **3.5x** weight — sculpting the loss surface directly toward the borderline cohort.

9. **Long-Text Remediation:** Diagnosed that texts exceeding **70 words** systematically degraded primary model performance due to context dilution. Implemented a **split-and-max scoring strategy**: long inputs are bisected, scored independently through the primary model, and the *maximum* quantile is retained as the canonical score — preserving complaint signal even when buried inside long, mixed-content prose.

10. **Comprehensive Robustness Validation Suite:** Stress-tested the ensemble against **six adversarial perturbation suites** (typos, misspellings, punctuation corruption, irrelevancy injection, complaint/non-complaint concatenations, synonym substitutions) to verify generalization beyond the clean training distribution — quantifying false-negative reductions per perturbation class.

11. **Operating-Point Grid Search:** Performed exhaustive grid search across (`lower_limit`, `upper_limit`, `xgb_threshold`) triples on a withheld validation set, optimizing for maximum recall under a precision-preserving constraint on total positive inferences. Final production operating point: lower=0.12, upper=0.393, XGB threshold=0.35.

##### 📈 Impact & Business Value
* **Perfect Recall Achievement:** Elevated regulatory complaint recall from **97% → 100%** — eliminating the risk of unreported FINRA-actionable client feedback.
* **Simultaneous Precision Improvement:** Reduced false positives by **~3%** — an inverse correlation that is architecturally difficult to achieve and normally requires a precision/recall trade-off.
* **Operational Cost Savings:** Estimated **~$600K in annual savings** by eliminating manual complaint review labor previously consumed by full-time staff investigating false positives.
* **Architectural Preservation:** Achieved all improvements *without* retraining the production RoBERTa model — zero deployment risk, zero re-validation overhead, and immediate production deployability as a bolt-on ensemble layer.

##### 💻 Tech Stack
`Python` • `PyTorch` • `Hugging Face Transformers (RoBERTa)` • `XGBoost` • `spaCy` • `coreferee` • `TextBlob` • `VADER Sentiment` • `Scikit-Learn` • `NMF` • `TF-IDF` • `Featuretools` • `WordNet` • `AWS Bedrock` • `Pandas` • `NumPy` • `Matplotlib`

---

## 🛠️ Summary Technical Proficiency Map

| Category | Technologies & Tooling |
| :--- | :--- |
| **Graph Neural Networks** | PyTorch Geometric, TransformerConv, Multi-Head Attention, torch_scatter, Bipartite Graph Design, Message Passing |
| **Deep Learning / Multi-Task** | PyTorch, Mixed Precision (AMP), GradScaler, ReduceLROnPlateau, Gated Fusion, Multi-Task Auxiliary Heads, Effective Number Weighting |
| **NLP & Embeddings** | RoBERTa, Hugging Face Transformers, NMF Topic Modeling, TF-IDF, spaCy, coreferee, WordNet, TextBlob, VADER, PCA + Andrews Curves Diagnostics |
| **Classical ML / Ensemble** | XGBoost, Recursive Feature Elimination (RFE), GridSearchCV, Featuretools (Deep Feature Synthesis), Sequential Ensembling |
| **Generative AI / Augmentation** | AWS Bedrock (LLM Invocation), LLM-Driven Synthetic Data Generation, Synonym Replacement, Adversarial Robustness Testing |
| **Cloud & Infrastructure** | AWS SageMaker, AWS Bedrock |
| **Data Engineering** | Pandas, NumPy, Stratified Sampling, Class Balancing, Sample Weighting, Feature Engineering at Scale, Long-Text Splitting Strategies |
