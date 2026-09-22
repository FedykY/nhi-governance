# Real-Time Non-Human Identities Governance and Auditing System

## Author: Yana Fedyk

A deterministic-first, cascaded Zero-Trust governance and auditing system for Non-Human Identities (NHIs), combining deterministic authorization, behavioral anomaly detection, contextual routing, local LLM-based semantic analysis, and cryptographic audit logging.

## Project Overview

This project implements a multi-layer architecture for governing and auditing Non-Human Identities in a Zero-Trust environment.

The system is designed so that AI/ML components provide security evidence and routing signals, while final authorization remains deterministic.

## Architecture

### Layer 0 — Deterministic Policy Decision Point

The authoritative authorization layer validates:

- NHI existence and lifecycle state
- Token identity binding
- Token expiration and revocation
- Token origin binding
- Nonce/JTI replay detection
- Identity, action, resource, and scope authorization
- Hard frequency limits

A Layer-0 denial cannot be overridden by downstream components.

### Layer 1 — Behavioral Anomaly Detection

Isolation Forest is used to detect behavioral anomalies and generate a routing signal.

The model does not make authorization decisions.

### Context and Routing

The system includes:

- Stateful per-NHI history
- Sequence-aware analysis
- Contextual routing
- Deterministic prompt-injection guardrails

Suspicious behavioral sequences can be routed to deeper semantic analysis even when individual actions are otherwise authorized.

### Layer 2 — Local Semantic Auditor

Semantic analysis is performed locally using:

- Ollama
- Qwen2.5-1.5B-Instruct

The LLM is advisory only. It cannot directly issue PERMIT, DENY, or QUARANTINE decisions.

### Layer 3 — Deterministic Arbitration

The final decision is produced by deterministic arbitration using the available security signals, state, routing evidence, semantic evidence, and failure state.

Possible decisions are:

- PERMIT
- DENY
- QUARANTINE

### Audit Layer

The system maintains a cryptographic audit ledger using a SHA-256 hash chain to provide tamper-evident auditability.

## Experimental Evaluation

The controlled development benchmark contains:

- 80 telemetry events
- 8 threat families
- 10 events per family

Four configurations were evaluated:

| Configuration | Accuracy | False Negatives | False Positives |
|---|---:|---:|---:|
| PDP only | 56.25% | 35 | 0 |
| PDP + behavioral ML | 77.5% | 15 | 3 |
| PDP + ML + guardrail | 90% | 5 | 3 |
| Full hybrid configuration | 96.25% | 0 | 3 |

The full hybrid configuration includes sequence-aware routing.

## Important Evaluation Note

The 80-event benchmark is a controlled development and ablation benchmark that was reused during iterative architectural refinement. The reported results therefore should not be interpreted as real-world or generalization accuracy.

An independent frozen hold-out benchmark would be required to establish generalization to unseen data.

## Local LLM Considerations

The project evaluates local LLM-based semantic auditing as an advisory security layer. Experiments showed that mandatory synchronous LLM processing is not suitable for the authorization path because of inference latency and output-consistency limitations.

The architecture therefore keeps deterministic authorization independent of the LLM.

## Notebook

The complete implementation is provided in the final Jupyter/Google Colab notebook:

**`Fedyk_NHI_Auditor`**

The notebook contains the implementation, benchmark generation, evaluation, and reproducibility workflow.

## Reproducibility

The project uses synthetic telemetry and local inference components.

The notebook is intended to be executed in a compatible Python/Google Colab environment with the required dependencies and local Ollama/Qwen model configuration.
