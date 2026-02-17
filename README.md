# Sentinel-RAG

### A Safety-Gated, Explainable Agentic Decision Support System

---

## Overview

**Sentinel-RAG** is a constrained multi-agent system that demonstrates how Retrieval-Augmented Generation (RAG) can be used for structured, safety-first decision support.

Instead of building an autonomous LLM pipeline, this project separates:

* Retrieval
* Reasoning
* Deterministic validation
* Post-hoc evaluation

The system evaluates simulated infrastructure change requests and produces a **structured recommendation** within a strict finite action space:

* `APPROVE`
* `REJECT`
* `REQUEST_MORE_INFORMATION`

LLM outputs are treated as **suggestions**, not authority.

The system prioritizes:

* Control over autonomy
* Determinism over creativity
* Auditability over convenience

---

## Architecture

The system is composed of five isolated components:

1. **Knowledge Ingestion Agent**

   * Parses documents
   * Chunks text deterministically
   * Generates embeddings
   * Stores metadata in vector database

2. **Retrieval Agent**

   * Performs semantic search
   * Applies metadata filters
   * Computes retrieval confidence
   * Detects coverage gaps

3. **Planner / Reasoning Agent (LLM-Based)**

   * Reasons only over retrieved context
   * Selects action from finite action space
   * Cites evidence explicitly
   * Produces structured JSON output

4. **Safety & Constraint Validator (Deterministic)**

   * Enforces rule-based constraints
   * Applies confidence thresholds
   * Rejects unsafe or unsupported decisions
   * Overrides planner output when necessary

5. **Evaluator / Critic Agent**

   * Logs decisions and validation results
   * Categorizes failures
   * Supports drift and consistency analysis

### Core Design Principle

> No single agent has enough authority to produce unsafe behavior.

---

## Decision Schema

All decisions must conform to a strict JSON schema:

```json
{
  "action": "APPROVE | REJECT | REQUEST_MORE_INFORMATION",
  "confidence": 0.0,
  "justification": "",
  "citations": [],
  "constraints_checked": [],
  "risk_summary": ""
}
```

Invalid outputs are rejected by the validator and never surfaced to users.

---

## Confidence Model

Confidence is not a vague LLM estimate.

It is derived from:

* Retrieval similarity scores
* Evidence coverage completeness
* Constraint validation results
* Coverage gap penalties

Confidence is capped when:

* Evidence is inferred
* Required constraints are undocumented
* Coverage gaps exist

The system prefers **no decision over unsafe decisions**.

---

## Failure Isolation

Common failure modes addressed:

| Failure Type            | Mitigation                                   |
| ----------------------- | -------------------------------------------- |
| Hallucinated citations  | Evidence validation against retrieved chunks |
| Missing documentation   | Coverage gap detection                       |
| Unsafe recommendation   | Deterministic constraint validator           |
| Low retrieval relevance | Confidence gating                            |
| Inconsistent outputs    | Post-hoc evaluation logging                  |

Failures do not propagate silently.

---

## Example Flow

1. Change request is submitted.
2. Retrieval agent fetches relevant documentation.
3. Planner proposes structured decision.
4. Validator enforces deterministic constraints.
5. Decision is accepted or rejected.
6. Evaluator logs outcome.

---

## Technology Stack

* Python
* FAISS or Chroma (vector database)
* OpenAI / local embedding model
* FastAPI (API layer)
* Docker (deployment)
* JSONL / SQLite (logging)

---

## Non-Goals

This system is intentionally **not**:

* Autonomous
* Self-learning
* Self-modifying
* Connected to production infrastructure
* Capable of executing real-world actions

It is a **decision-support prototype**, not an automation system.

---

## Deployment

Run locally:

```bash
docker build -t sentinel-rag .
docker run -p 8000:8000 sentinel-rag
```

API endpoint:

```
POST /evaluate_change
```

---

## Why This Project Exists

Many LLM-based systems optimize for autonomy and capability.

This project explores the opposite question:

> How do we build LLM systems that are controlled, constrained, and predictable?

Sentinel-RAG demonstrates how architectural separation and deterministic validation can make probabilistic reasoning safer and more auditable.

---

## Future Improvements

* Confidence calibration experiments
* Embedding drift monitoring
* Constraint policy expansion
* Adversarial testing scenarios
* Retrieval quality benchmarking

---

## Author

Yash Jaiswal

---
