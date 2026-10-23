# V1 Architecture Decisions & Traceability — FYP Topic Assistant
Version: 1.0

## 1. Architecture Needs Derived from Requirements (Step C2)

| Requirement ID | What does the requirement need? | Architecture Response |
| :--- | :--- | :--- |
| **FR1 / FR2 / NFR2** | Answers about FYP rules, past projects, and supervisor domains must meet the ≥ 85% factual correctness threshold using university-specific information that a base model does not possess. | Add a **Departmental FYP Knowledge Source** component and knowledge retrieval path to supply relevant document passages to the Language Model Service. |
| **FR4 / RAI1** | Responses must include 100% verifiable source references (document title and section) and ≥ 95% citation-source fidelity. | Include structured source metadata in the knowledge access flow and an **Evaluation Point** to verify citation presence and grounding. |
| **FR5 / RAI2** | Out-of-scope queries and academic integrity violations (e.g., requests to write full proposals) must be refused at a ≥ 95% success rate. | Add an explicit **Scope, Integrity & Privacy Control** component at the application layer before/after calling the Language Model Service. |
| **RAI3** | Student PII must not be transmitted to external model APIs (0 unmasked transmissions). | Integrate input PII detection/masking within the **Scope, Integrity & Privacy Control** boundary prior to external model invocation. |
| **NFR1 / NFR3** | End-to-end responses must complete within < 3,500 ms (p90) and cost < US$0.50 per 100 queries. | Maintain a streamlined single-pass retrieval-and-generation path and add an **Evaluation Point** to track latency and token usage. |

---

## 2. Architecture Decision Record (ADR) (Step C4)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
| :--- | :--- | :--- | :--- | :--- |
| **ADR 1: Include dynamic knowledge access to departmental FYP sources** | **FR1, FR2, FR4, NFR2, RAI1:** Responses must be grounded in official university FYP handbooks, supervisor profiles, and past project archives with verifiable citations and < 10 min update maintainability (**NFR4**). | 1. Base language model only (relying on pre-trained weights)<br>2. Stuffing all documents into every prompt<br>3. Dynamic knowledge-access component retrieving relevant document sections per query | **Generic dynamic knowledge-access component** connected to Departmental FYP Knowledge Sources. | Increases system complexity and adds retrieval latency compared to a bare model call, but significantly reduces hallucinations, lowers per-query token cost compared to full-document stuffing, and enables < 10 min document updates. |
| **ADR 2: Include an explicit Scope, Integrity & Privacy Control component** | **FR5, RAI2, RAI3:** The system must refuse out-of-scope/essay-writing queries (≥ 95% rate) and block student PII from reaching external APIs (0 leaks). | 1. System prompt instructions inside the main generation call only<br>2. Dedicated pre/post Scope, Integrity & Privacy Control component in the application flow | **Explicit Scope, Integrity & Privacy Control component** inspecting user inputs before external model calls. | Introduces a small processing overhead and potential false refusals on borderline scoping queries, but prevents PII transmission to external APIs and provides much stronger protection against prompt injection or academic dishonesty. |
| **ADR 3: Include an explicit Evaluation Point in the V1 flow** | **NFR1, NFR2, NFR3, RAI1:** The system must be evaluated against quantitative thresholds for latency (< 3.5 s), cost (< $0.50/100 queries), correctness (≥ 85%), and citation presence (100%). | 1. Ad-hoc manual spot-checking in the UI<br>2. Built-in Evaluation Point capturing inputs, retrieved sources, outputs, latency, and token metrics | **Dedicated Evaluation Point** capturing structured interaction logs and response metadata. | Requires extra instrumentation and storage logic early in V1, but makes systematic benchmarking and Responsible AI auditing repeatable and evidence-based. |

---

## 3. Initial Requirements Traceability Matrix (Step C4)

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
| :--- | :--- | :--- | :--- | :--- |
| **O1** (Design grounded FYP advising workflow) | **FR4 / RAI1** (Provide verifiable source citations for document-grounded answers) | Source Citation Presence & Grounding Fidelity (100% presence; ≥ 95% match) | **Departmental FYP Knowledge Sources** + **Language Model Service** + **Evaluation Point** | Grounding evaluation log and citation verification score table on the 30-query FYP benchmark set. |
| **O2** (Develop V1 system with scope & academic integrity controls) | **FR5 / RAI2** (Refuse out-of-scope and full-proposal writing requests) | Out-of-Scope & Academic Integrity Refusal (≥ 95% refusal-and-redirect rate) | **FYP Topic Assistant Application** + **Scope, Integrity & Privacy Control** | Pass/fail test report from the 20-query adversarial and out-of-scope safety test suite. |
| **O3** (Evaluate system correctness and operational performance) | **NFR2 / FR1** (Factual consistency with FYP handbooks and archives) | FYP Policy & Domain Answer Correctness (≥ 85% correct response rate) | **Departmental FYP Knowledge Sources** + **Language Model Service** | Graded rubric evaluation sheet comparing system responses against official FYP handbook answers. |
| **O3** (Evaluate system correctness and operational performance) | **NFR1 / NFR3** (Meet p90 latency < 3.5 s and cost < US$0.50 / 100 queries) | Interactive Latency & Budget Efficiency | **FYP Topic Assistant Application** + **Language Model Service** + **Evaluation Point** | Automated latency distribution summary (p50/p90) and token cost telemetry log across the benchmark workload. |

---

## Part C Reflection
**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?**
**ADR 1 (Including a dynamic knowledge-access component connected to Departmental FYP Knowledge Sources)** involved the most consequential engineering trade-off. It was driven by **FR1, FR4, NFR2, NFR4, and RAI1**, which require ≥ 85% factual correctness on university-specific FYP rules, 100% source citation presence, and < 10-minute maintainability when handbooks change. By selecting a dynamic knowledge-access component over a simple direct-to-model architecture, we accepted **higher architectural complexity and additional retrieval latency** (which consumes part of our 3,500 ms latency budget in **NFR1**), as well as the risk of retrieval misses, in exchange for verifiable grounding, lower per-query token costs, and effortless document updates.