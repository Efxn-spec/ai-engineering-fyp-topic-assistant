# Week 3 Data Inventory — FYPAdvisor (FYP Topic Assistant)
Version: 1.0

Evidence for this document comes from executing [`notebooks/week3_pipeline.ipynb`](../notebooks/week3_pipeline.ipynb) (Steps A3, 5A1 and 5A2).

---

## 1. From Topic 2 requirements to data needs (Step A1)

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
| :--- | :--- | :--- | :--- |
| **FR1** — answer FYP handbook rules, milestones, deliverables and assessment criteria | Official FYP/UGP procedures: topic proposal, UGP 1/UGP 2 structure, supervisor and student roles, seminar and report requirements | S01, S02 | Official UTM documents. **Constraint:** S01 belongs to the Faculty of *Mechanical Engineering* and S02 is a *Master's* handbook. Both are interim proxies until the Faculty of Computing PSM guideline is obtained. |
| **FR3** — critique draft title, problem statement and objectives for scope | Title rules (≤ 15 words, no redundant phrases such as "A study of …"); proposal structure (problem statement, objectives, scope) | S03 §1.5.1; S02 (proposal structure, p.14); S01 §6.0 | Authoritative wording of the rules. **Constraint:** the S03 manual targets graduate theses (2015 edition). |
| **FR4 / RAI1** — cite document title and section/page for every grounded answer | Source identity (curated title, version, SHA-256) **and a page number for every prepared passage** | S01–S03 plus metadata | Page-level provenance is mandatory. This drove the Part B choice of a page-aware chunker. |
| **NFR4** — ingest an updated handbook in < 10 min without code changes | Re-runnable ingestion with checksums | Part C pipeline (`data/pipeline_v1/documents/`) | Raw PDFs are kept unchanged, so the corpus can be rebuilt by re-running Cells 7–9. |

Evaluation criteria that data preparation may affect later: *Source Citation Presence & Grounding Fidelity* (needs the page field), *FYP Policy & Domain Answer Correctness* (at risk while proxy documents are used) and *Interactive Latency & Budget Efficiency* (chunk size sets the size of the prompt context).

---

## 2. Data inventory (Step A4)

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **S01** | `data/raw/fyp_documents/utm_fyp_psm_guideline.pdf` <br>Origin URL not recorded; probably the FKM FYP portal (https://mech.utm.my/fyp/fyp-report/), **to confirm** | Unstructured text, PDF, 23 pages. Undergraduate project rules: topic proposal procedure, UGP 1/UGP 2, supervisor/student roles, seminars, report, technical paper → FR1, FR3 | *Undergraduate Project (UGP) Guideline*, UGP Guideline/2014/1 (2014); PDF created 27 Apr 2016; Undergraduate Project Committee, Faculty of Mechanical Engineering (FME), UTM | Publicly downloadable UTM document; © UTM; redistribution permission **not verified** | 23/23 pages have a text layer; 36,087 characters extracted; running header `UGP Guideline/2014/1` on most pages; §6.0 *Type, Scope and Structure of Project* read cleanly on p.3; SHA-256 `8ca7ec4a…5b34186`; 102 prepared chunks |
| **S02** | `data/raw/fyp_documents/utm_project_scoping_handbook.pdf` <br>Origin URL not recorded, **to confirm** | Unstructured text, PDF, 26 pages. Project scoping and proposal structure (problem statement, aim, objectives, scope, deliverables) → FR1, FR3 | *Student Handbook for Master's Project*, MSc Business Intelligence and Analytics, 1st Edition 2017/2018; PDF created 7 Sep 2019; Programme Coordinator, Advanced Informatics School (AIS), UTM | Publicly downloadable UTM document; © UTM; redistribution permission **not verified** | 26/26 pages have text; 26,725 characters; 58 letter-spaced fragments (e.g. `I n t r o d u c t i o n`); running footer `Student Handbook for Master's Project N`; proposal structure ("Research scope … boundary of the project") on p.14; SHA-256 `ba5dc825…1c05617`; 80 prepared chunks |
| **S03** | `data/raw/fyp_documents/utm_thesis_manual_2015.pdf` <br>https://research.utm.my/wp-content/uploads/sites/25/2018/08/manual_thesis_2015.pdf | Unstructured text, PDF, 80 pages. Thesis title and format rules (≤ 15-word title, banned redundant phrases) → FR3 title critique, FR4 citations | *UTM Thesis Manual*, 2015 edition; PDF created 30 Sep 2015; School of Graduate Studies (SPS), UTM | Public URL; © UTM; redistribution permission **not verified** | 80/80 pages have text; 93,647 characters; PDF metadata title unusable (`Microsoft Word - B I_edited.doc`), so the title is curated; §1.5.1 title rule extracted intact on p.6; SHA-256 `dd33cd2f…2bf23f0`; 238 prepared chunks |

Full SHA-256 checksums are in [`data/week3_manifest.json`](../data/week3_manifest.json).

### Raw vs prepared storage

| Layer | Location | Content |
| :--- | :--- | :--- |
| Raw (never modified) | `data/raw/fyp_documents/*.pdf` | The three original PDFs (S01–S03) |
| Prepared — Part B | `data/processed/prepared_text.jsonl` | 420 page-referenced text chunks |
| Lineage — Part B | `data/week3_manifest.json` | One record per source: SHA-256 of source and output, processing, version, owner, access, limitations |
| Pipeline run — Part C | `data/pipeline_v1/documents/prepared_text.jsonl`, `pipeline_manifest.json` | Same 420 records (identical SHA-256), plus a run manifest with inspection, validation and mapping |

### Prepared record schema (`prepared_text.jsonl`)

| Field | Example | Purpose |
| :--- | :--- | :--- |
| `record_id` | `utm_thesis_manual_2015-0016` | Stable ID: source stem plus a sequence number |
| `text` | "1.5.1 Thesis Title … It should not contain more than 15 words …" | Chunk content (≤ 500 characters) |
| `source` / `source_id` | `fyp_documents/utm_thesis_manual_2015.pdf` / `S03` | Links back to this inventory |
| `document_title` | `UTM Thesis Manual, School of Graduate Studies` | Citation title (FR4) |
| `page` | `6` | Physical PDF page, 1-based (FR4) |
| `source_sha256` | `dd33cd2f…` | Which exact version of the source the chunk came from |
| `processing` | `page-paragraph-aware-char-500` | Preparation method |

---

## 3. Inspection observations (carried to Topic 4 quality review)

* **No OCR needed:** all 129 pages have a text layer, and no page is near-empty.
* **Short noise chunks:** 48 of the 420 chunks are under 100 characters (S01: 18, S02: 7, S03: 23). Most are a running header on its own (`UGP Guideline/2014/1`) or the tail of an over-long paragraph. They are kept for now; Topic 4 should decide whether to drop or merge them.
* **Letter-spaced headings** in S02 (58 fragments, e.g. `1 . 5 S c o p e o f M a s t e r ' s P r o j e c t`) will hurt keyword matching.
* **Typo in the source:** S01's cover reads "UNDERGARDUATE". This is the original document, not an extraction error, and it is left unchanged.
* **Page semantics:** `page` is the physical PDF page index. It can differ from the printed page number in front matter.

## 4. Relevance caveats and data gaps

* **S01 is a proxy.** It is the Mechanical Engineering UGP guideline, not the Faculty of Computing PSM guideline listed in the README (https://comp.utm.my/psm/psm-materials/). Replace it, or add the Computing guideline as a new source, before evaluation.
* **S02 is a proxy.** It is a postgraduate (Master's) handbook. Use it for scoping and proposal *structure* only. The assistant must not present Master's rules as undergraduate rules.
* **S03** is the 2015 graduate thesis manual. Check whether a newer edition exists.
* **FR2 gap:** supervisor research profiles and past FYP titles/abstracts have not been obtained. Planned as S04 and S05; a past-titles list would likely need Pathway B (tabular).
* **Rights:** the repository is public, and redistribution permission for the PDFs has not been verified. If UTM does not permit it, remove the raw PDFs from the repository and keep only their URLs and SHA-256 checksums.

## 5. Checkpoint A

*Which Topic 2 requirement needs this data, and can you show the loaded sample?*
FR1 and FR3 need the rule text itself, and FR4/RAI1 need a title and page for each passage. The A3 cell prints real extracted passages, not just "file loaded":
* S03 p.6: the title "should not contain more than 15 words";
* S01 p.3: §6.0 *Type, Scope and Structure of Project*;
* S02 p.14: "Research scope … Indicates the boundary of the project".

The B3 cell then shows that the same passages can be cited from the prepared records with title and page.

## 6. Selection rule decision

* **Required decision:** all three sources are justified by FR1, FR3 and FR4/RAI1 (Section 1). Source identity and access are recorded above. The preparation method is page-paragraph-aware chunking (see `week3_pipeline_decisions.md`).
* **Conditional implementation:** only Pathway A (PDF/TXT) is implemented. Pathways B–F do not serve any current requirement and were not run. No dummy practice dataset was used, because real project data was available.
