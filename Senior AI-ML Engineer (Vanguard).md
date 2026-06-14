# Senior AI/ML Engineer | Vanguard — Human Resources & Workforce Analytics
**February 2026 – Present**  



**Focus Areas:** Generative AI Systems, Retrieval-Augmented Generation (RAG), Semantic Data Modeling, Conversational Analytics, Workforce Intelligence Automation, Enterprise AI Governance, AI Product Strategy & Delivery Leadership

---

## Project Portfolio

### 1. Analyst Assistant — Multi-Intent Generative-AI Analytics Copilot for HR Teams (LLM Orchestration, RAG, Semantic Grounding)

*Architected and shipped an enterprise-grade, OIDC-authenticated AI copilot that classifies analyst intent across multiple task types and dynamically dispatches to specialized RAG pipelines grounded in Vanguard's HR data and code knowledge bases — accelerating ad-hoc HR analytics workflows across the organization.*

##### ⚙️ The Challenge & Business Context

Vanguard's HR Data & Analytics team supports data analysts whose daily workload is dominated by **repetitive, high-cognitive-load translation work**: turning ambiguous stakeholder questions into validated SQL, hunting through hundreds of `HRTools` / `CVTools` Python functions to find reusable code, and locating the correct cleansed HR tables among a sprawling data model.

These tasks created operational drag:

- **Discovery overhead** — Analysts spent disproportionate time searching for the *right* table, column, or function before any analysis could begin.
- **SQL authoring inconsistency** — Hand-written queries skipped business rules baked into the semantic layer (grade-level encoding, division/subdivision canonicalization), producing subtly incorrect headcount/attrition metrics.
- **Hard regulatory & privacy constraints** — All compute had to run inside Vanguard's internal AWS estate, behind Okta OIDC, with hashed user identity, KMS-encrypted storage, and Bedrock Guardrails enforced on every LLM call.

##### 🛠️ Architectural Implementation

1. **Intent-Routed LLM Orchestration (Strict Multi-Class Classifier):** Engineered an LLM-based intent classifier backed by **Claude Sonnet 4.5** on Bedrock that maps every analyst prompt into one of several mutually-exclusive intents, returning a single-line label with zero free-form output:

   | Intent | Purpose |
   | :--- | :--- |
   | `data_discovery` | Find HR tables/columns/schemas |
   | `code_discovery` | Find `HRTools` / `CVTools` functions & classes |
   | `code_disc_mod` | Modify/adapt existing Python utilities |
   | `mixed` | Joint code + data exploration |
   | `sql_generation` | Author Dremio SQL from natural language |
   | `general_info` | Conceptual / definitional explanations |

   The classifier system prompt bakes in **hard refusal rules** for harmful, write-mutating, or exfiltration-style requests — implementing a true production gatekeeper rather than a permissive router. Token-cap clamping, exponential-backoff retries, and streaming continuation with prior-tail re-prompting ensure long-tail calls complete reliably under Bedrock rate limits.

2. **Strategy-Pattern Response Router:** Engineered a thin dispatch layer that maps each resolved intent to its specialized synthesis handler. Each handler is fully self-contained with its own system prompt, **per-intent vector-search category filter** (`HR_data` vs. `HRTools`+`CVTools`), and task-specific guardrails — preventing cross-domain retrieval pollution that would inject Python code into SQL responses or vice versa.

3. **Bedrock Knowledge Base RAG with Category-Scoped Retrieval:** Built a centralized RAG wrapper around Bedrock Agent Runtime's `retrieve_and_generate` API with:
   - **Vector search filters by `category` metadata** scoped per intent
   - **Session-stickiness** via stored `sessionId` per KB ID for multi-turn conversational continuity
   - **Per-environment Bedrock Guardrails** (distinct guardrail IDs for eng/test/prod) enforcing content policy at inference time
   - **Cross-account STS role assumption** so the app's task role never holds raw Bedrock credentials

4. **Semantic-Layer-Aware Prompt Composition:** Every RAG prompt template is dynamically composed at request time to inject:
   - **Business rule semantic layer** — canonical entity definitions, metric formulas, table-relationship constraints (separate layers for data vs. code intents)
   - **Grade Dictionary augmentation** — instructs the LLM to expand colloquial grade tokens inline (`S5 → S5 (CS05, IS05, SP05, TS05)`) to ensure SQL `WHERE` clauses match the cleansed schema
   - **Division & Subdivision Dictionaries** — full enumeration of Vanguard's organizational hierarchy injected verbatim to constrain LLM grounding to real business units
   - **Few-shot Dremio SQL examples** covering canonical analyst patterns (crew movements, promotions YTD, headcount-by-site, rolling attrition) — pre-priming the model with the exact `hrwday_cleansed.*` schema and join idioms it should produce

5. **Adaptive Prompt-Length Enforcement Engine:** Implemented a **priority-aware prompt trimmer** that hard-caps composite prompts at Bedrock's input limit with a safety margin. When over budget, it prunes in a fixed precedence order — `interpretation → system → instructions` — while guaranteeing the user query stays intact, preventing silent truncation that would produce structurally broken SQL.

6. **Production Streamlit Deployment on ECS Fargate:** Containerized via a custom Dockerfile (Python 3.13 on Vanguard's internal artifactory base image), fronted by **nginx with TLS termination on 443** and websocket support for Streamlit's streaming channel. Deployed across three isolated environments via Vanguard's Dexter ECS exemplar with per-env IAM task roles, KMS-encrypted S3 buckets, Okta OIDC authentication (4-hour session timeout), and auto-scaling 1–2 containers.

7. **PII-Safe Identity & Storage Layer:** User identity extracted from upstream Okta cookies via Streamlit's WebSocket headers, then **SHA-256 hashed with a fixed salt before any persistence** — raw crew IDs never touch S3. All S3 writes routed through a wrapper with **automatic SSE-KMS** application. All persistence paths namespaced under `{feedback_logs|chat_logs|uploaded_data}/{hashed_crew_id}/...` — no cross-user data leakage even if listing permissions broaden.

8. **Inline Feedback Loop with Structured Failure Taxonomy:** Every assistant response surfaces an inline 👍 / 👎 widget tied to a unique `interaction_id`. Downvotes open a **closed-vocabulary multiselect** capturing structured failure modes (didn't understand request, incorrect SQL, syntax errors, too generic, hallucinated, not thorough enough, other) plus free-text. Every interaction is logged with full provenance — `timestamp, user_prompt, intent, retrieved_tables, retrieved_code, model_response, feedback` — and synchronized to S3. **Chat history** is overwrite-synced to a single per-session S3 key on every assistant turn, embedding the resolved intent alongside each message to enable downstream feedback-driven prompt iteration.

9. **Multi-Model Inference Profile Architecture:** Exposed **five Bedrock Application Inference Profiles** behind a user-selectable model toggle — Claude Sonnet 3.5 / 4 / 4.5, Opus 4.1, Titan Text Embeddings — with **per-env ARN injection**, enabling controlled rollout of new Anthropic releases through eng → test → prod without code changes.

##### 📈 Impact & Business Value

- **End-to-End Analytics Acceleration** — Collapsed the most repetitive HR-analyst workflows (data discovery, code discovery, SQL authoring) into a single conversational surface, removing context-switching across documentation, code repos, and SQL editors.
- **Semantic-Layer-Enforced Correctness** — By injecting Vanguard's grade/division/subdivision dictionaries and business-rule semantic layer into every prompt, generated SQL respects organizational metric definitions out of the box — eliminating a major class of subtle metric-calculation errors that plagued hand-written queries.
- **Production-Hardened Safety Posture** — Zero raw user identifiers in storage, KMS encryption on every write, env-isolated Bedrock Guardrails, OIDC-enforced access, and refusal-on-harmful-request at the intent classifier itself — deployable into a regulated financial-services environment with no governance exceptions.
- **Continuous-Improvement Telemetry** — Structured feedback (intent + retrieved context + downvote category) creates a closed-loop dataset directly mappable to specific handler/prompt iterations, enabling targeted prompt refinement rather than blind regeneration.

##### 💻 Tech Stack

`Python 3.13` • `Streamlit` • `AWS Bedrock (Claude Sonnet 3.5/4/4.5, Opus 4.1, Titan Embeddings)` • `Bedrock Knowledge Bases (RetrieveAndGenerate)` • `Bedrock Guardrails` • `AWS ECS Fargate` • `nginx (TLS termination)` • `boto3 / botocore` • `AWS STS Cross-Account AssumeRole` • `S3 with SSE-KMS` • `Okta OIDC` • `Dremio SQL` • `Prompt Engineering` • `Few-Shot Learning` • `Strategy Pattern / Intent Routing` • `SHA-256 Hashed Identity`

---

### 2. Agentic Analyst Platform — Enterprise GenAI Analytics Co-Pilots (Technical Lead + Product/Delivery Owner)

*Led the design, productionization, and enterprise rollout of Vanguard's governed Agentic Analyst platform — a metadata-driven GenAI system that turns natural-language business questions into governed SQL-backed insights and visualizations — and stood up two production line-of-business implementations on it: the **Business Partner Virtual Analyst** for HR workforce analytics and the **Recruiting Virtual Analyst** for Talent Acquisition. This was a deliberate blend of hands-on architecture and end-to-end product/delivery ownership, spanning use-case definition, NPV/value modeling, AI governance approval, and cross-functional stakeholder management.*

##### ⚙️ The Challenge & Business Context

Across HR and Talent Acquisition, analytics delivery was bottlenecked. Recurring questions — *"What's time-to-fill for this division?"*, *"Show me workforce movement trends"*, *"How effective is this source?"* — funneled through a small set of analysts, producing **manual reporting cycles, inconsistent metric interpretations, and fragmented access to data, logic, and definitions**. HR Business Partners and recruiting leaders couldn't self-serve, and analysts were consumed by repetitive pulls instead of strategic work.

The mandate was **not** to build a generic chatbot. It was to deliver an **analytics co-pilot grounded in enterprise data, business logic, and governance controls** — one that preserves data governance, metric consistency, and enterprise definitions while making self-service safe in a regulated environment. Beyond the engineering, the effort required **defining the use cases, quantifying the business case, and clearing Vanguard's AI governance gates** before anything could ship.

##### 🧭 Product, Strategy & Delivery Leadership

1. **End-to-End Use-Case Strategy & Question Taxonomy:** Synthesized ambiguous, fragmented business needs (delayed recruiting insights, inconsistent metric definitions) into clearly scoped GenAI use cases with measurable outcomes. Defined the **core question taxonomy** — pipeline/funnel health, time-to-fill, source effectiveness, requisition health, headcount, workforce movement & composition trends — that anchored both system design and the underlying data modeling. Aligned every use case to **HR DnA and Talent Acquisition OKRs** (efficiency, analytics adoption, decision speed) to ensure enterprise relevance.

2. **Value Estimation & Multi-Year NPV Modeling:** Built a detailed **5-year NPV model for Recruiting Virtual Analyst that decomposed productivity savings across **seven distinct recruiting inquiry categories** (funnel & conversion, source/school/pipeline effectiveness, requisition health & aging, time-based process efficiency, workforce composition, hiring volume & demand, and in-development functionality). Layered in **persona-based cost-per-hour assumptions with 3% annual merit growth**, **YoY inquiry-growth curves** (20% near-term, tapering to 10%) tied to concrete demand drivers (India hiring ramp from 50 → ~1,700 hires by 2027, international site expansion, advice-business growth), and **adoption-adjusted time savings** for the HR DnA analyst pool. The model landed at an **NPV of ~$1.0M ($1,006,194)** — the artifact that secured stakeholder buy-in and funding.

3. **AI Governance Approval (AIDP / SAR / PRA):** Drove the program through Vanguard's AI governance gates — **led preparation of AI divisional panel materials** (use-case narratives, risk summaries, system-workflow diagrams) and **coordinated the Security Architecture Review (SAR) and Privacy Risk Assessment (PRA)**. Designed and documented the core risk controls that made approval defensible: **metadata-only LLM interaction, aggregation-level query enforcement, prompt isolation, and enterprise AWS bedreock guardrails for PII and policy compliance.** For Recruiting VA, **reused previously approved BPVA artifacts** to materially accelerate sign-off.

4. **Cross-Functional Stakeholder Management & Translation:** Operated as the **translator between business and technical domains**, partnering with HR Business Partners, recruiting leadership, data engineering, and enterprise risk/governance. Drove stakeholder alignment, adoption readiness, and governance compliance, and **supported onboarding of multi-region users and cross-functional HR teams** — with access governed by the underlying data permissions (e.g., Workday datasets).

5. **Pilot → Production → Reuse Roadmap:** Shepherded the platform along an iterative path: early *Analyst Assistant* pilot foundations (SQL generation, code reuse, data discovery) → **BPVA productionization** as the first enterprise HR use case (establishing the architecture, governance model, and guardrails) → **RVA extension** reusing the same architecture with changes limited to datasets, personas, and domain logic. Ran pilot studies, testing workflows, and feedback loops to refine system performance and UX.

##### 🛠️ Architectural Implementation

1. **Metadata-Driven, Governed NL → SQL → Insight Pipeline:** Architected the end-to-end flow — *user prompt → LLM-orchestrated prompt chain → SQL generation → query execution against Athena-backed curated datasets → post-processing & explanation → structured output (tables, summaries, suggested follow-ups)* — enabling non-technical users to query enterprise data through natural language while every result stays grounded in approved data and definitions.

2. **Semantic / Metadata Layer (Critical Design Element):** Designed the platform so the **LLM never sees raw records** — it reasons exclusively over **table schemas, column descriptions, business definitions, and metric formulas**. This metadata-first abstraction is what drives consistent, safe, explainable interpretations and is the cornerstone control that satisfied privacy review.

3. **Multi-Step Prompt Chaining for Controlled Reasoning:** Contributed to a prompt-chaining design that decomposes complex analytical asks into safe, controlled sub-steps (intent/metric interpretation → query construction → result explanation), producing **explainable outputs with a retrieval explanation** and materially reducing hallucination risk.

4. **Three-Layer Data Protection Strategy:** Implemented defense-in-depth — **(a) Data Abstraction** (LLM sees only metadata), **(b) Prompt Constraints** (queries restricted to aggregated insights, no row-level exposure), and **(c) Enterprise Guardrails** (PII blocking, toxicity filtering, policy compliance enforced at inference) — the exact control set documented for AIDP/SAR/PRA.

5. **Manifest-Driven Multi-LOB Architecture (Reuse Engine):** Operationalized a **platform reuse strategy** so a new line of business is stood up via **manifest-driven configuration in S3** rather than re-engineering. Each LOB carries its own database, tables, prompt templates, metadata, and semantic files, with **role-based access control via SailPoint** governing who can reach which LOB. Extending BPVA → RVA collapsed to **re-pointing to new curated datasets (candidate + requisition tables) and onboarding new personas (recruiters, TA leaders)** — establishing a repeatable pattern for future Virtual Analyst use cases and additional LOBs.

6. **Governed Self-Service Output Contract:** Standardized every response to return not just an answer but **metric definitions, the generated SQL/logic, the data table, a natural-language summary, an explanation of how the data was retrieved, and suggested follow-up questions** — combining chatbot UX with BI rigor so outputs are context-aware, explainable, and governed by construction.

##### 📈 Impact & Business Value

- **~$1.0M NPV Business Case (5-Year, 8% Discount Rate)** — Authored the RVA value model that projected a **net present value of $1,006,194**, with **non-discounted total benefit scaling from ~$197K in Year 1 to ~$321K by Year 5** against a rapidly amortizing build cost (~$57K in Year 1 falling to ~$6K by Year 5). Year 1 discounted net benefit alone reached **~$140K**, climbing to **~$232K by Year 5**.
- **~850 → ~1,500 Analyst Hours Saved Annually (GTA)** — Quantified **846 hours saved in Year 1 growing to 1,487 hours by Year 5** across recruiting inquiry categories (funnel & conversion, source/pipeline effectiveness, requisition health, process efficiency, workforce composition, and hiring demand), translating to **$128K → $254K in GTA productivity savings** as recruiting demand scaled with the India hiring ramp and international expansion.
- **~515 Adoption-Adjusted Hours/Year Returned to HR DnA Analysts** — On top of GTA savings, modeled **~515 adoption-adjusted hours saved annually** across new, veteran, and reporting analysts (~$63K–$69K/yr), shifting capacity from repetitive reporting toward strategic work.
- **Governed Self-Service Analytics at Scale** — Replaced ad-hoc reporting cycles for HR Business Partners and recruiters with a single conversational surface that delivers **governed insights grounded in Vanguard data and analyst logic**, eliminating manual data pulls, reconciliation, and back-and-forth intake requests.
- **Cleared Enterprise AI Governance** — Achieved **AIDP / SAR / PRA approval** for a generative-AI system operating on sensitive HR and recruiting data by designing the governance controls into the architecture (metadata-only interaction, aggregation-only queries, guardrails) — and accelerated RVA approval by reusing BPVA's approved artifacts.
- **Multi-Domain Scalability Proven** — Extended the platform from HR (BPVA) to Recruiting (RVA) with **minimal reengineering**, validating a reusable, manifest-driven blueprint for onboarding additional lines of business and personas.

##### 💻 Tech Stack

`Python` • `AWS Bedrock (LLM Orchestration & Guardrails)` • `Amazon Athena` • `AWS Lambda` • `S3` • `Role-Based Access Control` • `Metadata / Semantic Layer Design` • `Multi-Step Prompt Chaining` • `Prompt Engineering` • `Enterprise AI Governance` • `Value Modeling` • `Streamlit`

> **Role blend:** Technical Lead (architecture, semantic-layer & prompt-chain design, multi-LOB reuse engine) **and** Product/Delivery Owner (use-case strategy, question taxonomy, ~$1.0M NPV/value modeling, AIDP/SAR/PRA governance approval, and cross-functional stakeholder management).

---

## 🛠️ Summary Technical Proficiency Map

| Category | Technologies & Tooling |
| :--- | :--- |
| **Generative AI / LLM Systems** | AWS Bedrock (Claude, Titan Embeddings), Bedrock Knowledge Bases, Bedrock Guardrails, Intent Routing, Strategy Pattern, Multi-Step Prompt Chaining |
| **RAG & Semantic Grounding** | Category-Scoped Vector Retrieval, Semantic/Metadata Layer Design, Few-Shot Learning, Adaptive Prompt-Length Enforcement |
| **Natural Language → SQL / Conversational Analytics** | Dremio SQL, Amazon Athena, Metric Definition Grounding, Explainable Query Generation |
| **Cloud & Deployment** | AWS ECS Fargate, AWS Lambda, S3 |
| **Security, Identity & Governance** | Okta OIDC, SailPoint Role Based Access Control, Bedrock Guardrails (PII/Toxicity/Policy), Enterprise AI Governance |
| **Product & Delivery Leadership** | Use-Case Strategy, Question Taxonomy Definition, 5-Year NPV & Value Modeling, KPI Alignment, Risk Control Documentation, Cross-Functional Stakeholder Management, Pilot→Production→Reuse Roadmapping |
| **MLOps & Telemetry** | Multi-Model Inference Profiles, Per-Env ARN Injection, Structured Feedback Taxonomy, Provenance Logging, Feedback-Driven Prompt Iteration |
