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

| W1 | System map + project scope | README + docs | ✅ |

