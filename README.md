# Justin Roberts | Senior AI/ML Engineer

Welcome to my work portfolio! This repository documents my professional work experience building data-driven systems that solve real business problems across sectors-from financial services to civil infrastructure optimization.

---

## 🎯 What You'll Find Here

This repository contains detailed documentation of projects I've led across my current and previous roles:

1. **[Senior AI/ML Engineer @ Vanguard](./Senior%20AI-ML%20Engineer%20(Vanguard).md)** (2026–Present) — Building enterprise AI systems for HR and workforce analytics
2. **[Data Scientist @ Vanguard](./Data%20Scientist%20(Vanguard).md)** (2024–2026) — Designing neural networks and NLP systems for marketing personalization and compliance
3. **[AI/ML Engineer @ Halff Associates](./AI-ML%20Engineer%20(Halff%20Associates).md)** (2023–2024) — End-to-end ML systems for infrastructure, real estate, and land-use surveying operations

Each document is a technical narrative—covering challenges, architecture, and the measurable business impact.

---

## 📋 Project Summaries and Highlights

*Use the dropdown buttons below to see a brief synopsis of each project. For the full scope of work for each role/project, click through to the detailed role documentation above.*

<details>
<summary><strong>Analyst Assistant</strong> — Multi-Intent Generative-AI Copilot for HR Analytics</summary>

| | |
| :--- | :--- |
| **What** | Gen AI copilot that classifies users (HR data analysts) prompt intent and routes to specialized RAG pipelines grounded in contextual business data. Performs code discovery, code modification, data discovery, and sql generation |
| **Why** | HR data analysts spent 40%+ of their time on repetitive translation work: converting ambiguous questions into valid SQL, finding/modifying relevant code resources, discovering appropriate data tables, and strategizing how to leverage data tables |
| **How** | LLM routes user intent to the appropriate handler (data discovery/SQL/code generation) + handler retrieves relevant context from knowledge base + dynamically injects business rules and data definitions into prompts |
| **Innovation** | Embeds organizational hierarchies and data definitions directly into prompts, ensuring generated responses automatically respect governance without manual rule-writing |
| **Impact** | Collapsed data discovery, code discovery, and code generation worfklows into a single conversational surface; achieved ~27% operational time savings |

</details>

<details>
<summary><strong>Agentic Analyst Platform</strong> — Governed GenAI Analytics System (Technical Lead + Product Owner)</summary>

| | |
| :--- | :--- |
| **What** | Enterprise analytics platform that turns natural-language business questions into governed SQL queries, data insights, and visualizations. As a strategic system, this tool traces stakeholder questions to predefined proprietary metric definitions, generates auditable SQL with explanations, surfaces raw data results, generates business-friendly summaries, and creates visuals that enable non-technical business leaders (recruiters, HR partners, finance stakeholders) to self-serve complex analytics |
| **Why** | Talent Acquisition, HR, and Finance teams faced analytics bottlenecks; recurring questions like "time-to-fill by division?" or "workforce movement trends?" required weeks of ad-hoc analyst work, delaying business decisions |
| **How** | Multi-step prompt chaining (intent interpretation → metric mapping → SQL generation → validation) + four-layer data protection (AWS Guardrails/metadata abstraction/aggregation constraints/row-level filtering) |
| **Innovation** | Translated decentralized business knowledge into a reusable semantic information (metric definitions, business rules, organizational hierarchies) that persists across queries; manifest-driven configuration lets new business units onboard via S3 metadata without re-engineering; governance controls baked into architecture|
| **Impact** | ~$1.0M NPV (5-year); 846 annual analyst hours saved; reusable architecture enabling rapid expansion to new lines of business with minimal engineering overhead |

</details>

<details>
<summary><strong>Marketing Campaign Graph Neural Network & Embedding Generation</strong> — Marketing Campaign Touch-point Encoding</summary>

| | |
| :--- | :--- |
| **What** | Two-stage graph neural network pipeline that encodes complete marketing campaign histories (product offers, recency, frequency, outcomes) into dense 128-dimension embeddings for each client. Supplying Vanguard product recommendation engines with additional reasoning capability for offer personalization, impression fatigue awareness, and temporally aware reccomndations |
| **Why** | Existing product recommendation engines had no awareness of individual clients marketing campaign history (frequency, recency, or temporal patterns); clients saw repeated offers, throttling engagement and wasting marketing spend |
| **How** | Built a bipartite graph connecting clients to their marketing campaign histories. First stage uses TransformerConv to learn encodings of individual campaign event sequences → Second stage adaptively aggregates campaign sequences into a single embedding for each client |
| **Innovation** | Trained a gated fusion mechanism to intelligently blend attention-based history aggregation (effective for high-engagement clients with 100+ campaign impressions) with mean-pooling aggregation (effective for single-campaign impression clients); the model learns how much of each method to use based on the count of campaigns a client has been exposed to. Eliminating need to hand-tune separate models  |
| **Impact** | Unlocked new personalization dimension (impression fatigue awareness); improved Vanguard product reccomndation engine feature space |

</details>

<details>
<summary><strong>FINRA Regulatory Complaint Detection</strong> — NLP Ensemble for Perfect Compliance</summary>

| | |
| :--- | :--- |
| **What** | RoBERTa primary classifier + XGBoost 'second opinion' model designed to accurately classify potential falely negative classified inputs to the primary classifier, achieving 100% recall on regulatory complaints |
| **Why** | Primary RoBERTa classifier had 97% recall; missing even 1 complaint per month creates FINRA reporting gaps and serious regulatory risk |
| **How** | Diagnosed false-negative patterns using PCA + Andrews Curves visualizations of labeled text embeddings → identified the specific prediction range (*False Negative Risk Range*) and semantic patterns of false negative outputs coming from the primary RoBERTa model → Using semantic features (syntactic/topic/keyword signals) I trained an XGBoost classifier as a "second opinion" model specialized in accurate classification of potential False negative outputs from the primary RoBERTa model  → routed predictions through both classifiers for second opinion when appropriate |
| **Innovation** | Generated synthetic cases using LLM augmentation and adversarial perturbations (typos, noise, edge cases) to teach the second opinion model robustness on complaint patterns the primary model struggled with; Zero retraining of production RoBERTa |
| **Impact** | Elevated recall to 100%; reduced false positives by 3%; eliminated ~$600K in annual manual review labor |

</details>

<details>
<summary><strong>Utility Pipe Failure Prediction</strong> — Survival Analysis for Infrastructure Maintenance</summary>

| | |
| :--- | :--- |
| **What** | Predictive system combining asset telemetry, environmental variables, and historical fault data to forecast structural failures in subsurface utility pipes, enabling proactive maintenance scheduling |
| **Why** | Municipal utilities relied on reactive repairs or arbitrary maintenance schedules; unplanned failures are expensive (excavation + downtime) and disrupt service |
| **How** | Survival analysis (Random Survival Forest) to handle censored data (pipes that haven't failed yet) + LightGBM regression to predict specific time-to-failure windows + back-dated historical reconstruction to generate training labels |
| **Innovation** | Integrated censored-data modeling: natural handling of incomplete failure history; stratified feature engineering across material/soil/environmental domains ensures model learns environment-specific failure modes separately |
| **Impact** | 93% accuracy within 30-month prediction window; $436K annual savings in targeted maintenance; optimized excavation scheduling reducing emergency repairs |

</details>

<details>
<summary><strong>Property Type Attribute Extraction</strong> — Multi-Task Sequential Deep Learning with Computer Vision</summary>

| | |
| :--- | :--- |
| **What** | Multi-task neural network extracting structural metadata (number of stories, foundation type, finished floor height) from Google Street View imagery and LIDAR Data, enabling automated land-use profiling and flood-risk assessment at municipal scale |
| **Why** | Municipalities need detailed property attributes for accurate land-use profiles and flood-risk modeling; manual field surveying is expensive, time-consuming, and doesn't scale |
| **How** | Sequential architecture chains three prediction tasks: image + metadata → Story classifier → Foundation classifier (using story predictions + metadata) → Finished Floor Height regressor prediction (using both prior predictions + metadata); each stage feeds learned embeddings to improve downstream accuracy |
| **Innovation** | Information gain loop: downstream tasks benefit from semantic abstractions learned by upstream classifiers, progressively refining predictions toward the high-value target (finished floor height); eliminates need for separate single-task models |
| **Impact** | 60% reduction in field surveying costs; enabled automated flood-risk mapping and rapid real-estate categorization at municipal scale |

</details>

<details>
<summary><strong>Material Cost Classification & Database</strong> — NLP Pipeline for Unstructured Data Standardization</summary>

| | |
| :--- | :--- |
| **What** | Automated pipeline standardizing 2 decades of unstructured contractor bid and cost data (PDFs, CSVs, Excel sheets) into a centralized, queryable database with consistent schema and semantic classifications |
| **Why** | Engineering teams wasted time manually parsing inconsistent bid formats and vendor spreadsheets; cost estimates lacked institutional historical context, forcing redundant estimation work |
| **How** | OCR extraction from PDFs → schema normalization across sources → zero-shot NLP classification of utilities and descriptions (Hugging Face transformers) → confidence-gated routing (high-confidence predictions auto-labeled, low-confidence sent to human review) |
| **Innovation** | Confidence-gated human-in-the-loop fallback: only automate what the model is confident about; route ambiguous cases to annotators for labeling, gradually improving model accuracy while preserving data integrity |
| **Impact** | 3x faster cost estimate generation; centralized institutional cost intelligence; enabled engineers to leverage historical trends for more accurate project planning; created data ready for other ML/GenAI usecases |

</details>

---

## 📊 Impact Snapshot

**Enterprise-Grade AI & Analytics**
- Built production LLM systems serving HR analysts and business leaders, collapsing repetitive workflows (discovery/authoring/analytics) from hours to minutes
- Architected governed analytics platforms that translated ambiguous business questions into auditable SQL and insights, enabling non-technical stakeholders to self-serve complex analytics (~$1M NPV, 846–1,487 hours saved annually)
- Designed specialized graph neural networks encoding millions of client marketing interactions into dense embeddings, unlocking new personalization dimensions for financial product recommendations
- Deployed NLP ensembles achieving 100% regulatory compliance detection while eliminating ~$600K in annual manual review labor

**Infrastructure & Risk Intelligence**
- Created predictive systems for municipal utility maintenance using survival analysis, identifying failures before they occur (93% accuracy, $436K annual savings, optimized excavation scheduling)
- Automated property attribute extraction from imagery and LiDAR data, reducing field surveying costs by 60% and enabling real-time flood-risk assessment at scale
- Standardized two decades of unstructured contractor bid data into a centralized, queryable database, accelerating engineering cost estimates 3x and enabling downstream ML/GenAI use cases

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
