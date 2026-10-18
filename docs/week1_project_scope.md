\# Week 1 Project Scope



\## 1. Project Name and Domain

\- \*\*Project Name:\*\* FYPAdvisor — AI FYP Topic Recommendation Assistant

\- \*\*Domain:\*\* Higher Education / Undergraduate Final Year Project (FYP) Supervision \& Topic Selection



\## 2. Problem Statement

Final-year undergraduate students struggle to formulate suitable Final Year Project (FYP) topics, align their technical interests with faculty research domains, and verify whether proposed titles comply with official university thesis guidelines. Meanwhile, FYP coordinators and potential supervisors face heavy workloads answering repetitive scoping inquiries, as project guidelines, past project repositories, and supervisor expertise profiles are scattered across multiple separate documents and web pages. An AI engineering solution using document-grounded retrieval (RAG) can synthesize official FYP handbooks, supervisor profiles, and past project abstracts to recommend feasible, guideline-compliant FYP topics 24/7.



\## 3. Evidence of the Problem

\- \*\*Evidence 1:\*\* Official FYP requirements, thesis formatting rules, scope constraints, and past project titles are distributed across separate university handbooks, faculty portals, and institutional repositories, requiring students to manually cross-reference multiple documents before proposing a topic.

&#x20; - \*\*Source / citation:\*\* Faculty of Computing Final Year Project (PSM) Materials \& UTM Thesis Manual Guidelines (https://comp.utm.my/psm/psm-materials/)

\- \*\*Evidence 2:\*\* Undergraduate students frequently experience delays and topic rejections during early project scoping when proposed titles do not adhere to institutional title rules (such as word limits and prohibited phrases) or fail to match supervisor research areas.

&#x20; - \*\*Source / citation:\*\* UTM Thesis Manual — Section 1.5.1 Thesis Title Requirements (https://research.utm.my/wp-content/uploads/sites/25/2018/08/manual\_thesis\_2015.pdf)



\## 4. Initial Related Work (at least 3)

\- \*\*Study / solution 1:\*\* Human FYP supervisor and coordinator consultation

&#x20; - \*\*Source / citation:\*\* University Undergraduate FYP / PSM Supervision Guidelines (https://comp.utm.my/psm/psm-materials/)

&#x20; - \*\*Key limitation:\*\* Highly contextual and personalized, but strictly constrained by lecturer availability, consultation hours, and supervision workload.

\- \*\*Study / solution 2:\*\* Static university FYP handbooks, thesis manuals, and past title lists

&#x20; - \*\*Source / citation:\*\* Official University Thesis Manual \& Faculty FYP Portal (https://mech.utm.my/fyp/fyp-report/)

&#x20; - \*\*Key limitation:\*\* Authoritative and official, but fragmented across multiple static PDFs and web pages with no interactive topic matching or feasibility feedback.

\- \*\*Study / solution 3:\*\* General-purpose conversational AI assistants (e.g., standard ChatGPT / Claude)

&#x20; - \*\*Source / citation:\*\* OpenAI ChatGPT (https://chat.openai.com) / Anthropic Claude (https://claude.ai)

&#x20; - \*\*Key limitation:\*\* Flexible at brainstorming broad project ideas, but not grounded in official university FYP guidelines, faculty-specific scope expectations, or real supervisor expertise documents.



\## 5. Engineering Gap / Limitation

\- \*\*Gap / Limitation:\*\* Existing approaches are either fragmented across static PDFs, limited by academic staff availability, or rely on ungrounded general-purpose LLMs that generate overly ambitious or non-compliant project titles without referencing approved university FYP guidelines and faculty research documentation.



\## 6. Initial Project Objectives (2–3)

\- \*\*O1:\*\* To design a document-grounded AI FYP topic recommendation and scoping solution based on official university FYP handbooks, thesis guidelines, and faculty research documents.

\- \*\*O2:\*\* To develop the proposed solution using retrieval-augmented generation (RAG) and appropriate input/output guardrails to ensure recommended topics comply with institutional guidelines.

\- \*\*O3:\*\* To evaluate the solution in terms of recommendation relevance, answer groundedness, response latency, and Responsible AI safety.



\## 7. Target Users

Third- and final-year undergraduate computing and engineering students preparing to select their Final Year Project (FYP) topic and potential supervisor.



\## 8. Data Sources (at least 2)

\- \*\*Source 1:\*\* UTM Thesis Manual PDF (Official rules on thesis titles, abstract structure, and report scoping) — \*\*URL:\*\* https://research.utm.my/wp-content/uploads/sites/25/2018/08/manual\_thesis\_2015.pdf

\- \*\*Source 2:\*\* Faculty of Computing PSM (Final Year Project) Guidelines \& Synopsis Materials — \*\*URL:\*\* https://comp.utm.my/psm/psm-materials/

\- \*\*Source 3 (if any):\*\* Faculty Final Year Project Report Guidelines \& Templates — \*\*URL:\*\* https://mech.utm.my/fyp/fyp-report/



\## 9. Comparison with EduAdvisor

\- \*\*Similar to EduAdvisor:\*\* Both systems operate in the Higher Education domain and use a document-grounded Retrieval-Augmented Generation (RAG) architecture with guardrails to answer undergraduate student queries using official university PDF documents.

\- \*\*Different from EduAdvisor:\*\* While EduAdvisor focuses on factual Q\&A for course prerequisites and graduation policies, this project focuses on guided FYP topic recommendation, scope feasibility checking, and aligning student interests with official FYP handbook criteria and faculty research areas.



\## 10. Responsible AI Risks (at least 2)

\- \*\*Risk 1 (Hallucination):\*\* The model invents non-existent supervisor research areas, fabricates citation links, or claims a topic meets faculty rules when it violates official handbook constraints, causing a student to submit an invalid FYP proposal.

\- \*\*Risk 2 (Fairness / Bias):\*\* Certain research tracks or sub-disciplines (e.g., popular AI topics vs. networking or cybersecurity) may be over-represented in the source documents, leading to unequal recommendation quality for students interested in niche specializations.

\- \*\*Risk 3 (Safety / Academic Integrity):\*\* A student asks the assistant to write their entire FYP proposal or fabricate experimental data, or expresses severe emotional distress over failing their project, resulting in an inappropriate or unethical system response if guardrails are missing.

