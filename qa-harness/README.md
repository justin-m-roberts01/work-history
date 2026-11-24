# QA Harness — Enterprise AI Harness Engineering Project

A deliberately broken document QA agent. Your job is to harden it.

---

## The Setup

You have a simple QA agent that answers employee questions about company policy documents. It kind of works. It will return answers, it won't crash, and on happy-path inputs it looks fine.

It has **5 deliberate failure modes** baked in. Your job across 5 milestones is to:
1. Diagnose the failure (Challenge Mode — find it yourself first)
2. Design and build the fix (Guided Mode — use the questions below as a compass)

**The rule**: Don't patch `naive.py`. Build your hardened agent in `agent.py`. The test suite (`tests/test_suite.py`) imports from `agent.py` and will tell you when each milestone is passing.

---

## Project Structure

```
qa-harness/
├── naive.py              # The broken starting point — read it, diagnose it
├── agent.py              # YOU CREATE THIS — your hardened agent
├── harness/              # YOU POPULATE THIS — one module per milestone
│   ├── gate.py           # Milestone 1
│   ├── contract.py       # Milestone 2
│   ├── provenance.py     # Milestone 3
│   ├── checks.py         # Milestone 4
│   └── evaluator.py      # Milestone 5
├── docs/                 # The three policy documents the agent reads
├── workspace/            # Runtime state — logs, provenance, eval reports
├── tests/
│   └── test_suite.py     # Pre-written adversarial tests — make these pass
└── README.md
```

---

## How to Run

```bash
# See the naive agent in action (and its failures)
python naive.py

# Run the test suite against your hardened agent
python -m pytest tests/ -v

# Run a specific milestone's tests only
python -m pytest tests/ -v -k "TestInputGating"
```

Install dependencies:
```bash
pip install pytest
```

To use a real LLM instead of the mock, replace the `call_llm()` function
in `naive.py` (and carry it into your `agent.py`) with a real API call.
The rest of the harness is LLM-agnostic.

---

## The 5 Failure Modes (Challenge Mode)

Before reading the milestone guides below, **run `naive.py` and read the source code**. Try to identify all 5 failure modes on your own. Each one is commented with `# FAILURE MODE N` to confirm your diagnosis — but try to spot them before reading the comments.

Ask yourself:
- What inputs could break this?
- What happens when retrieval fails?
- What does "it worked" actually mean here?
- If something goes wrong in production, how would you investigate it?
- If one category of question fails 80% of the time, would you know?

---

## The 5 Milestones (Guided Mode)

### Milestone 1 — Input Gating
**Concept**: The environment should enforce what kinds of inputs are allowed before the LLM ever sees them. Correct behavior shouldn't depend on the LLM being well-behaved.

**Guiding questions** (think through these before writing code):
- What categories of questions should this agent answer? How would you define those boundaries?
- What's the difference between a question the agent *can't* answer (no relevant docs) vs. one it *shouldn't* answer (out of scope or unsafe)?
- Where should the gate live — before retrieval, before the LLM call, or somewhere else? Why does that order matter?
- What should the gate *return* when it blocks something? A refusal? Silence? A specific error type?
- How would you test that your gate is rejecting the right things without over-blocking valid questions?

**Build**: `harness/gate.py`
**Tests**: `TestInputGating` in `test_suite.py`

---

### Milestone 2 — Acceptance Rules
**Concept**: Before you run an experiment, you have to define what success looks like. The standard for judging an answer must be set before the answer exists — not after you see it.

**Guiding questions**:
- What are the different *types* of questions this agent handles? Think about what a correct answer looks like for each type — is it a specific number? A procedural description? A list of steps?
- How would you classify a question's type before answering it?
- What should the acceptance rule *contain*? What fields does it need to be actionable?
- When in the pipeline does the rule get recorded — and how do you ensure it can't be silently changed after the answer is generated?
- What's the risk of applying the same acceptance rule to every query type?

**Build**: `harness/contract.py`
**Tests**: `TestAcceptanceRules` in `test_suite.py`

---

### Milestone 3 — Provenance
**Concept**: Every answer must be traceable back to the chain of decisions that produced it. If you can't reconstruct why the agent said what it said, you can't debug failures or improve the system.

**Guiding questions**:
- What is the minimum set of information you'd need to fully reproduce an answer from scratch?
- Where does that information get recorded — in memory? In a file? Why does the choice matter?
- What's the difference between logging for debugging and logging for provenance? (Hint: one is about what happened, the other is about *why* it happened.)
- What should happen to the provenance record if a run fails partway through?
- How does your provenance structure connect to the acceptance rule from Milestone 2?

**Build**: `harness/provenance.py`
**Tests**: `TestProvenance` in `test_suite.py`

---

### Milestone 4 — External Validation
**Concept**: The LLM cannot reliably judge its own output. An external check — running outside the model's reasoning — must verify that the answer meets the acceptance rule before it reaches the user.

**Guiding questions**:
- What does it mean for an answer to be "grounded" in the retrieved documents? How would you check that programmatically?
- What's the difference between checking whether the answer *looks* right vs. checking whether it *is* traceable to the source?
- What should happen when validation fails — silent fallback? Explicit refusal? Flagged response?
- How does your external check use the acceptance rule you recorded in Milestone 2?
- What kinds of hallucinations would your check catch? What kinds would it miss? Is that acceptable?

**Build**: `harness/checks.py`
**Tests**: `TestExternalValidation` in `test_suite.py`

---

### Milestone 5 — Stratified Evaluation
**Concept**: Aggregate metrics hide failures. A system that's right 90% of the time on easy questions and 0% of the time on hard ones looks fine in aggregate — until the hard questions matter.

**Guiding questions**:
- What are the distinct categories of questions your agent handles? How does failure in each category differ in severity?
- What does your evaluation report need to contain to be useful to someone trying to improve the system?
- How do you define "pass" for a given question in the evaluation suite, given that different question types have different acceptance rules?
- Where does the eval report get written, and when? Should it be per-run or cumulative?
- If you look at your report after running 20 test questions, what would tell you the system is production-ready vs. not?

**Build**: `harness/evaluator.py`
**Tests**: `TestStratifiedEvaluation` in `test_suite.py`

---

## What `agent.py` Should Look Like

Your hardened agent should expose the same `answer_question(question, documents)` interface the tests expect, but the return value is now a dict instead of a plain string:

```python
{
    "answer": str,                    # The answer (or refusal message)
    "gated": bool,                    # True if the input gate blocked this
    "acceptance_rule": dict,          # The pre-registered rule for this query type
    "retrieved_chunks": list[str],    # What was retrieved
    "prompt": str,                    # The prompt sent to the LLM
    "validation_passed": bool,        # Whether external checks passed
    "grounded": bool,                 # Whether answer is traceable to retrieved chunks
    "timestamp": str,                 # When this was processed
}
```

Wire your harness modules together in `agent.py`. Each module should be independently importable and testable.

---

## A Note on Difficulty

Milestone 1 and 3 are the most concrete — you'll know quickly if they're working.  
Milestone 2 is the most conceptual — sit with the guiding questions before coding.  
Milestone 4 is the most interesting — a simple groundedness check will catch obvious hallucinations, but edge cases will make you think hard.  
Milestone 5 is the most revealing — build it last and you'll see your whole system clearly for the first time.

Good luck.
