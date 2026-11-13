\# FYPAdvisor — AI FYP Topic Recommendation Assistant



> Recommends feasible Final Year Project (FYP) topics and answers project scoping questions for undergraduate students, grounded in official university FYP handbooks and thesis guidelines.



\## Problem Statement

Undergraduate students entering their final year often struggle to select viable, appropriately scoped Final Year Project (FYP) topics that align with both their technical skills and official university thesis requirements. At the same time, supervisors and coordinators have limited availability to repeatedly explain basic scoping rules, while official guidelines and past project references are scattered across separate PDFs and portals. AI engineering enables a 24/7 document-grounded assistant that combines retrieval and guardrails to recommend compliant project ideas based on approved institutional documents.



\## Evidence of the Problem

\- Official FYP scoping rules, thesis title constraints, and synopsis formats are distributed across multiple separate university handbooks and faculty web portals (Source: Faculty of Computing PSM Materials, https://comp.utm.my/psm/psm-materials/).

\- Students frequently propose titles that violate institutional formatting and scope requirements—such as exceeding the 15-word limit or using redundant phrases—leading to proposal revisions and delays (Source: UTM Thesis Manual Section 1.5.1, https://research.utm.my/wp-content/uploads/sites/25/2018/08/manual\_thesis\_2015.pdf).



\## Initial Related Work / Existing Approaches

\- \*\*Human FYP supervisor consultation\*\* (Source: University PSM Supervision Guidelines, https://comp.utm.my/psm/psm-materials/) — Provides tailored mentorship, but is constrained by staff availability and scheduling bottlenecks.

\- \*\*Static university FYP handbooks and repositories\*\* (Source: Official University Thesis Manual \& FYP Portals, https://mech.utm.my/fyp/fyp-report/) — Authoritative and accurate, but fragmented across multiple documents and difficult to search interactively by student interest.

\- \*\*General-purpose AI assistants\*\* (Source: OpenAI ChatGPT, https://chat.openai.com) — Flexible for brainstorming ideas, but not grounded in official university FYP handbooks, grading rubrics, or supervisor profiles.



\## Engineering Gap / Limitation

Existing approaches are fragmented across static university documents, depend heavily on limited staff consultation hours, or rely on general-purpose AI chatbots that do not ground topic recommendations in approved institutional FYP guidelines.



\## Project Objectives

\- O1: To design a document-grounded AI FYP topic recommendation solution based on official university FYP handbooks and guidelines.

\- O2: To develop the proposed solution with document retrieval and appropriate input/output guardrails.

\- O3: To evaluate the solution in terms of answer quality, groundedness, latency, and safety.



\## Target Users

Third- and final-year undergraduate students preparing their Final Year Project (FYP) topic proposals.



\## Data Sources

\- UTM Thesis Manual PDF — https://research.utm.my/wp-content/uploads/sites/25/2018/08/manual\_thesis\_2015.pdf

\- Faculty of Computing PSM (Final Year Project) Guidelines \& Materials — https://comp.utm.my/psm/psm-materials/

\- Faculty FYP Report Guidelines \& Templates — https://mech.utm.my/fyp/fyp-report/



\## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + [docs/week1_project_scope.md](docs/week1_project_scope.md) | ✅ |
| W2 | Requirements, evaluation criteria, V1 architecture | [requirements](docs/week2_requirements.md), [evaluation criteria](docs/week2_evaluation_criteria.md), [architecture](docs/week2_architecture_v1.png), [decisions](docs/week2_architecture_decisions.md) | ✅ |
| W3 | Data engineering: V1 data pipeline, lineage | [notebook](notebooks/week3_pipeline.ipynb), [data inventory](docs/week3_data_inventory.md), [pipeline diagram](docs/week3_pipeline_diagram.png), [pipeline decisions](docs/week3_pipeline_decisions.md), [prepared data](data/processed/prepared_text.jsonl), [lineage manifest](data/week3_manifest.json), [pipeline run](data/pipeline_v1/documents/) | ✅ |


## Week 3 — V1 Data Pipeline (Pathway A: PDF/TXT)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Efxn-spec/ai-engineering-fyp-topic-assistant/blob/main/notebooks/week3_pipeline.ipynb)

The *Departmental FYP Knowledge Sources* component of the Week 2 architecture is now an executable, traceable batch ETL pipeline:

**SOURCE** (3 UTM PDFs, 129 pages) → **INGEST** (explicit selection, SHA-256) → **INSPECT** (pypdf, extractability check) → **PREPARE** (whitespace cleanup, paragraph-aware chunks ≤ 500 chars **per page**) → **VALIDATE** (non-empty output, unique IDs, every chunk has a page) → **RECORD LINEAGE** (manifests, rerun check)

- **Why PDF/TXT only:** every V1 knowledge source is an official PDF (FR1, FR3, FR4/RAI1). No requirement needs tabular, image, JSON, audio or time-series data.
- **Key decision:** page-aware paragraph chunking was chosen over fixed-size chunking. Every one of the 420 chunks carries document title + page for citations (FR4/RAI1), and 22.9 % of chunks start mid-sentence, against 71.7 % for fixed-size chunks.
- **Evidence:** lineage validation PASS, repeatability rerun PASS, and the Part C pipeline output is byte-identical to the Part B prepared data.
- **Known caveats:** two sources are interim proxies (FKM undergraduate guideline, MSc project handbook) for the Faculty of Computing PSM guideline. Supervisor profiles and past FYP titles (FR2) are not yet collected. See [docs/week3_data_inventory.md](docs/week3_data_inventory.md).

```
data/
├── raw/fyp_documents/          # original PDFs (unchanged, S01–S03)
├── processed/prepared_text.jsonl   # Part B prepared corpus
├── week3_manifest.json         # Part B lineage manifest
└── pipeline_v1/documents/      # Part C pipeline output + pipeline_manifest.json
```

