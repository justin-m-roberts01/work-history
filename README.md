# Justin Roberts | Senior AI/ML Engineer

Welcome to my work portfolio. This repository documents my professional work building data-driven systems that solve real business problems—from financial services to civil infrastructure optimization.

---

## 🎯 What You'll Find Here

This repository contains detailed documentation of projects I've led across my current and previous positions:

1. **[Senior AI/ML Engineer at Vanguard](./Senior%20AI-ML%20Engineer%20(Vanguard).md)** — Building enterprise AI systems for HR and workforce analytics
2. **[Data Scientist at Vanguard](./Data%20Scientist%20(Vanguard).md)** — Designing neural networks and NLP systems for marketing personalization and compliance
3. **[AI/ML Engineer at Halff Associates](./AI-ML%20Engineer%20(Halff%20Associates).md)** — End-to-end ML systems for infrastructure, real estate, and land-use surveying operations

Each document is a technical narrative—covering the challenge, the architecture, and the measurable business impact.

---

## 📋 Project Highlights

<details>
<summary><strong>Analyst Assistant</strong> — Multi-Intent Generative-AI Copilot for HR Analytics</summary>

| | |
| :--- | :--- |
| **What** | OIDC-authenticated LLM copilot that classifies analyst intent and routes to specialized RAG pipelines grounded in Vanguard's semantic data layer |
| **Why** | HR analysts spent 40%+ of time on repetitive translation work: converting ambiguous questions into valid SQL and discovering the right data tables |
| **How** | Strategy-pattern dispatch layer + Bedrock RAG with category-scoped retrieval + dynamic semantic/metric layer injection + adaptive prompt-length enforcement |
| **Innovation** | Semantic layer-aware prompt composition baking in business rules, grade dictionaries, and organizational hierarchies to ensure generated SQL respects governance |
| **Impact** | Collapsed discovery/authoring workflows into a single conversational surface; eliminated context-switching; achieved ~40% operational time savings |

</details>

<details>
<summary><strong>Agentic Analyst Platform</strong> — Governed GenAI Analytics System (Technical Lead + Product Owner)</summary>

| | |
| :--- | :--- |
| **What** | Metadata-driven NL → SQL → insight pipeline that turns natural-language business questions into governed, auditable analytics without exposing raw data |
| **Why** | Recruiting and HR teams faced bottlenecks in analytics delivery; questions like "time-to-fill by division?" or "workforce movement trends?" required weeks of ad-hoc reporting |
| **How** | Multi-step prompt chaining (intent/metric interpretation → SQL generation → validation) + three-layer data protection (abstraction/constraints/aggregation-only) + manifest-driven multi-LOB reuse |
| **Innovation** | Manifest-driven configuration engine: onboard a new business unit via S3 metadata rather than re-engineering; cleared enterprise AI governance (AIDP/SAR/PRA) by baking controls into architecture |
| **Impact** | ~$1.0M NPV (5-year); 846–1,487 annual analyst hours saved; scaled from HR to recruiting with minimal re-engineering |

</details>

<details>
<summary><strong>Marketing History Graph Neural Network</strong> — Client Interaction Encoding (GNN)</summary>

| | |
| :--- | :--- |
| **What** | Two-stage GNN pipeline encoding 500+ client marketing impressions into dense 128-dim embeddings powering offer personalization |
| **Why** | XGBoost decision engine had no awareness of impression fatigue or temporal patterns; clients saw repeated offers, throttling engagement |
| **How** | Bipartite graph (client ↔ impression nodes) + TransformerConv backbone (4-head attention) + cohort-aware adaptive aggregation (recency/frequency/diversity heads) + 8-task auxiliary supervision |
| **Innovation** | Learned gated fusion mechanism dynamically blends attention with mean-pooling; stratified temporal sampling for high-engagement clients avoiding degenerate softmax collapse |
| **Impact** | Unlocked new personalization dimension; improved decision engine feature space; validated across single-touch and 500+ impression cohorts |

</details>

<details>
<summary><strong>FINRA Regulatory Complaint Detector</strong> — NLP Ensemble for Perfect Compliance</summary>

| | |
| :--- | :--- |
| **What** | RoBERTa-based primary classifier + XGBoost secondary ensemble targeting false negatives, achieving 100% recall on regulatory complaints |
| **Why** | Production classifier had 97% recall; missing even 1 complaint per month creates FINRA reporting gaps and regulatory risk |
| **How** | PCA + Andrews Curves diagnosis of false-negative patterns → 50-feature semantic extraction (syntactic/semantic/topic/keyword signals) → recursive feature elimination → confidence-gated secondary routing |
| **Innovation** | Generative data augmentation (LLM + synthetic oversampling + adversarial perturbation suite); long-text remediation (split-and-max scoring for 70+ word texts) |
| **Impact** | Elevated recall to 100%; reduced false positives by 3%; eliminated ~$600K in annual manual review labor; zero retraining risk (secondary model only) |

</details>

<details>
<summary><strong>Utility Pipe Failure Prediction</strong> — Survival Analysis for Infrastructure Maintenance</summary>

| | |
| :--- | :--- |
| **What** | Multi-modal predictive system combining asset telemetry, environmental variables, and fault history to forecast structural failures in subsurface utility pipes |
| **Why** | Municipal utilities relied on reactive fixes or arbitrary schedules; every unplanned failure is expensive excavation + downtime |
| **How** | Survival analysis (Random Survival Forest) for censored data handling + LightGBM regression for time-to-failure windows + back-dated target labeling for historical reconstruction |
| **Innovation** | Integrated censored-data modeling (pipes that hadn't yet failed); stratified feature engineering across material/soil/environmental domains |
| **Impact** | 93% accuracy within 30-month prediction window; $436K annual savings in targeted maintenance; optimized excavation scheduling |

</details>

<details>
<summary><strong>Property Type Attribute Extraction</strong> — Multi-Task Sequential Deep Learning with Computer Vision</summary>

| | |
| :--- | :--- |
| **What** | Multi-task neural network extracting structural metadata (stories, foundation type, finished floor height) from Google Street View imagery |
| **Why** | Municipalities need spatial property attributes for land-use profiling and flood-risk assessment; manual surveying is expensive and time-consuming |
| **How** | Sequential architecture: image + metadata → Story classifier → Foundation classifier → Floor Height regressor; each task feeds embeddings to downstream tasks for hierarchical feature reuse |
| **Innovation** | Information gain loop: downstream tasks benefit from semantic abstractions learned by upstream classifiers, optimizing predictive power for high-value targets |
| **Impact** | 60% reduction in field surveying costs; enabled automated flood-risk mapping and rapid real-estate categorization at scale |

</details>

<details>
<summary><strong>Material Cost Classification & Database</strong> — NLP Pipeline for Unstructured Data Standardization</summary>

| | |
| :--- | :--- |
| **What** | Automated pipeline extracting, cleaning, and classifying a decade of unstructured contractor bid/cost data into a centralized, queryable database |
| **Why** | Engineering teams wasted time parsing inconsistent PDFs/CSVs/Excel sheets; cost estimates lacked institutional historical context |
| **How** | OCR ingestion (PDF/CSV/Excel parsing) → schema normalization → zero-shot semantic classification (Hugging Face transformers) → confidence-gated fallback for ambiguous items |
| **Innovation** | Confidence-gated human-in-the-loop loop for low-confidence predictions, preserving data fidelity while scaling automation |
| **Impact** | 3x faster cost estimate generation; centralized institutional cost intelligence; enabled engineers to leverage historical trends |

</details>

---

## 📊 Impact Snapshot

**Enterprise-Grade AI & Analytics**
- Built production LLM systems serving HR analysts, reducing workflows from hours to minutes
- Designed specialized neural networks processing millions of client interactions for personalized financial recommendations
- Deployed NLP systems achieving 100% regulatory compliance detection while cutting manual review costs by ~$600K annually

**Infrastructure & Risk Intelligence**
- Created predictive systems for municipal utility maintenance, identifying failures before they occur (93% accuracy, $436K in annual savings)
- Automated property attribute extraction from imagery, reducing surveying costs by 60%
- Built cost-prediction systems accelerating engineering estimates 3x through historical data synthesis

---

## 🛠️ Technical Span

| Domain | Representative Work |
| :--- | :--- |
| **Deep Learning & Neural Networks** | Graph Neural Networks (TransformerConv, multi-head attention), multi-task sequential architectures, mixed-precision training at scale |
| **NLP & LLMs** | RoBERTa fine-tuning, semantic search, intent routing, prompt chaining, RAG systems (Bedrock Knowledge Bases), generative data augmentation |
| **Classical & Ensemble ML** | XGBoost ensembling, survival analysis (Random Survival Forests), recursive feature elimination, deep feature synthesis |
| **Cloud & Production** | AWS (SageMaker, Bedrock, Lambda, ECS Fargate, Athena), containerization, OIDC/enterprise auth, KMS encryption, structured telemetry |
| **Data Engineering & Feature Work** | Handling imbalanced classes, stratified sampling, adversarial robustness testing, multi-modal feature synthesis, long-text remediation |
| **Product & Leadership** | Use-case strategy, value modeling, cross-functional stakeholder translation, AI governance (AIDP/SAR), platform scalability design |

---

## 🔑 What Stands Out

**1. End-to-End Ownership**
Each project moves from problem definition through measurement. I don't just build models—I quantify the business impact, from cost savings to operational hours recovered to regulatory risk elimination.

**2. Production Rigor**
These aren't prototypes. Systems are deployed behind enterprise authentication, running on encrypted infrastructure, instrumented with feedback loops, and designed for cross-functional reuse.

**3. Complexity Without Jargon**
I move fluidly between deep technical work (multi-head attention, survival curves, adversarial perturbation suites) and clear communication. The details matter, but so does impact.

**4. Strategic Thinking**
Increasingly, my work bridges engineering and business—designing systems that are not just technically sound but strategically aligned and governable at enterprise scale.

---

## 🤝 Let's Talk

If you'd like to discuss any of these projects, dive into specific technical decisions, or explore how these approaches might apply to your challenges, I'm open to conversation.

---

**Last updated:** June 2026  
**Repository:** [justin-m-roberts01/work-history](https://github.com/justin-m-roberts01/work-history)
