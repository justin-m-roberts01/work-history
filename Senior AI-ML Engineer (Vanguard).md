# Senior AI/ML Engineer | Vanguard — Human Resources & Workforce Analytics
**March 2026 – Present**  



**Focus Areas:** Generative AI Systems, Retrieval-Augmented Generation (RAG), Semantic Data Modeling, Conversational Analytics, Workforce Intelligence Automation, Enterprise AI Governance

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
