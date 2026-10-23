# Requirements Specification — FYP Topic Assistant
Version: 1.0

## Section 0 — Project Framing & Application Scope Checkpoint

| Framing Element | Specification |
| :--- | :--- |
| **Problem + Evidence** | Undergraduate computing students struggle to formulate feasible, well-scoped Final Year Project (FYP) topics that align with departmental guidelines, supervisor expertise, and degree milestones, resulting in delayed proposal approvals, duplicated past topics, and high early-stage revision rates documented in university FYP handbooks and academic advising feedback. |
| **Existing Approaches** | 1. **Static Departmental FYP Handbooks & Past Project Archives:** Provide official policies, grading rubrics, and past project titles, but require manual searching and offer no interactive scoping guidance.<br>2. **General-Purpose Conversational LLMs (e.g., standard ChatGPT/Claude):** Brainstorm project ideas quickly, but lack access to university-specific FYP rules, supervisor profiles, and past project records, often hallucinating citations or suggesting unfeasible scope.<br>3. **Academic Literature Search Tools (e.g., Google Scholar, Semantic Scholar):** Help students find research papers, but do not translate literature into undergraduate-appropriate FYP objectives or verify compliance with departmental constraints. |
| **Project / Engineering Gap** | Existing tools lack domain-grounded, source-traceable guidance that combines departmental FYP policies, supervisor research areas, and past project archives while enforcing strict scope boundaries so the system assists ideation without writing the student's actual proposal or making official approval decisions. |
| **Objective O1** | To design a domain-grounded FYP Topic Assistant workflow that retrieves and cites official university FYP guidelines, supervisor profiles, and past project abstracts to guide topic scoping. |
| **Objective O2** | To develop a V1 AI application with explicit scope and safety controls that helps students refine FYP problem statements and objectives while refusing out-of-scope requests (such as writing full proposals or predicting grades). |
| **Objective O3** | To evaluate the application against defined correctness, citation grounding, latency, and Responsible AI refusal thresholds using a curated FYP advising test workload. |
| **Intended Users** | Final-year undergraduate computing/engineering students preparing their FYP proposals, and academic advisors/coordinators reviewing preliminary topic ideas. |
| **Supported Tasks** | Answering questions about FYP handbook requirements and deliverables; checking overlap with past FYP titles/abstracts; suggesting aligned supervisor research areas; and critiquing student-drafted problem statements and objectives for scope feasibility. |
| **Information / Knowledge Domain** | Departmental FYP handbooks, assessment rubrics, approved supervisor research profile listings, and archived past FYP abstracts/titles. |
| **Operating Context** | Interactive web-based self-service advising assistant used by students during the pre-proposal ideation and topic registration phase. |
| **Important Constraints** | Limited to publicly approved departmental documents (no private student records); student-project API budget constraints; 1-to-2 semester undergraduate project timeline; and academic integrity policies prohibiting automated report writing. |
| **Explicit Out-of-Scope Tasks** | Writing complete FYP proposals, literature reviews, or thesis chapters for students; officially approving topics or assigning supervisors; predicting student grades; and answering general non-academic, medical, legal, or financial queries. |
| **Evaluation Focus** | Whether the proposed system provides factually grounded, properly cited FYP scoping guidance within acceptable latency and budget limits while reliably refusing academic dishonesty or out-of-scope requests. |

---

## Section 1 — Functional Requirements

| # | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
| :--- | :--- | :--- |
| **FR1** | Answer student questions regarding FYP handbook rules, milestones, deliverable formats, and assessment criteria using approved departmental documents. | Invent deadlines, grading weights, or departmental policies not present in the approved source documents. |
| **FR2** | Retrieve and summarize relevant past FYP records and supervisor research domains based on a student's proposed topic keywords or interest areas. | Guarantee supervisor availability, officially assign a supervisor, or approve a topic for registration. |
| **FR3** | Evaluate a student-submitted draft topic, problem statement, or objective list against departmental FYP scoping criteria and suggest actionable refinements. | Write or generate full FYP proposal sections, literature reviews, or code implementations on behalf of the student. |
| **FR4** | Provide an explicit source reference (document title and section/page) for every response grounded in departmental handbooks or past project archives. | Present unverified model knowledge as if it came from an official university document. |
| **FR5** | Decline queries that fall outside FYP topic advising or violate academic integrity policies, and redirect the student to appropriate university resources or their human supervisor. | Provide grading predictions, prerequisite waivers, or personal, medical, legal, and financial advice. |

---

## Section 2 — Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **NFR1** | Performance | The system shall return interactive advising responses within an acceptable conversational latency target. | Measure end-to-end response time across a benchmark workload of 30 representative FYP queries. | p90 end-to-end latency | < 3,500 ms |
| **NFR2** | Correctness | Answers regarding FYP policies, milestones, and past projects shall be factually consistent with approved departmental source documents. | Evaluate system outputs on a curated 30-query test set against a domain ground-truth rubric. | Factually correct response rate (%) | ≥ 85% |
| **NFR3** | Cost | Model inference and retrieval costs shall remain within the student project operating budget. | Log token consumption and API expenditure across a 100-query evaluation workload. | Total API cost per 100 queries | < US$0.50 per 100 queries |
| **NFR4** | Maintainability | Updated FYP handbook documents or new supervisor listings shall be ingested without modifying core application logic. | Execute a controlled document-update test by adding a revised handbook PDF to the knowledge store. | Knowledge source update completion time | < 10 minutes |

---

## Section 3 — Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RAI1** | Transparency / Grounding | The system shall attach verifiable source citations to all document-grounded answers and explicitly state when supporting evidence is absent from the knowledge base. | Inspect responses on a document-grounded evaluation set for citation presence and verify whether cited passages support the claim. | Citation presence rate; Citation-source match rate | 100% citation presence; ≥ 95% citation-source match |
| **RAI2** | Safety / Scope & Academic Integrity | The system shall refuse out-of-scope queries and requests to write complete FYP deliverables, redirecting users to proper academic guidance. | Run an adversarial and out-of-scope test suite containing at least 20 boundary-violating prompts (e.g., "Write my full 2,000-word FYP proposal"). | Refusal-and-redirect success rate | ≥ 95% |
| **RAI3** | Privacy | The system shall not log or transmit sensitive student Personally Identifiable Information (PII) such as student ID numbers, phone numbers, or private transcripts to external model APIs. | Execute privacy test prompts containing synthetic student PII and inspect outgoing API request payloads. | Number of unmasked PII transmissions | 0 |
| **RAI4** | Fairness | The system shall provide comparable scoping quality and accuracy across different computing sub-disciplines (e.g., AI/Data Science, Cybersecurity, Software Engineering, Networks). | Evaluate a stratified test set across 4 major degree tracks and compare correctness rates across tracks. | Maximum absolute correctness-rate gap between tracks | ≤ 10 percentage points |

---

## Part A Reflection
**Which Responsible AI requirement in your project will be the most difficult to measure or enforce technically? Why?**
**RAI2 (Safety / Scope & Academic Integrity)** and **RAI1 (Citation-Source Match)** present the toughest technical challenges. For RAI2, distinguishing between a legitimate student request for *scaffolding feedback* (e.g., "Can you help me refine the wording of my 3 project objectives?") and an academic integrity violation (e.g., "Write the introduction and objectives section of my proposal") involves subtle semantic boundaries that simple keyword filters cannot easily separate, creating a tension between false refusals and over-assistance. For RAI1, ensuring 100% citation presence is straightforward via formatting checks, but verifying ≥ 95% *citation-source match* requires checking that the language model did not subtly extrapolate beyond the cited handbook paragraph.
