# Week 3 Pipeline Decisions, Stage Contracts and Traceability — FYPAdvisor
Version: 1.0

Companion files:
* Notebook: [`notebooks/week3_pipeline.ipynb`](../notebooks/week3_pipeline.ipynb)
* Data inventory: [`docs/week3_data_inventory.md`](week3_data_inventory.md)
* Diagram: [`docs/week3_pipeline_diagram.png`](week3_pipeline_diagram.png), with editable source [`.drawio`](week3_pipeline_diagram.drawio)

---

## 1. Requirement traceability

| Topic 2 requirement | Data need | Pipeline response | Evidence |
| :--- | :--- | :--- | :--- |
| **FR1** (answer FYP rules) | Official rule text | S01 and S02 ingested, all 49 pages kept as text | 182 chunks from S01/S02 in `prepared_text.jsonl` |
| **FR3** (critique title/scope) | Title and scope rules | S03 §1.5.1 and S02 proposal structure kept intact within chunks | B3 output: the 15-word rule sits in one chunk, `utm_thesis_manual_2015-0016` (p.6) |
| **FR4 / RAI1** (cite title + section/page) | Title and page per passage | Curated `document_title`; **page-aware chunking**, so every chunk has a page | `with_page_ref_% = 100.0` (Cell 5A1); assertion in Cell 5A2 |
| **NFR4** (handbook update < 10 min, no code change) | Re-runnable, checksummed ingestion | Batch pipeline (Cells 7–9) over the `fyp_documents` folder; SHA-256 per source and output | Cell 9 rerun PASS; Cell 9B Part B = Part C |
| FR2 (supervisor/past-project data) | Profiles, past titles | **Not yet available.** Recorded as a data gap | `week3_data_inventory.md` §4 |

## 2. Selected sources

S01 FKM UGP Guideline 2014, S02 MSc BIA Master's Project Handbook 2017/2018 and S03 UTM Thesis Manual 2015. All are PDFs in `data/raw/fyp_documents/`; identity, rights and caveats are in the data inventory. **Selected pathway:** A (PDF/TXT) only.

## 3. Part B comparison and evidence (Step B3)

All configurations use the same three sources, the same cleaning rule (collapse spaces/tabs) and the same 500-character size.

| Configuration | Chunks | Mean chars | Chunks < 100 chars | Chunks starting mid-sentence | Chunks ending at sentence punctuation | Chunks with a page reference |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| `fixed-char-500` (whole document) | 300 | 499 | 0 | 71.7 % | 6.7 % | 0 % |
| `paragraph-aware-char-500` (whole document) | 381 | 375 | 28 | 26.2 % | 30.7 % | 0 % |
| **`page-paragraph-aware-char-500` (selected)** | **420** | **340** | **48** | **22.9 %** | **36.0 %** | **100 %** |

Qualitative evidence (Cell 5A1 output):
* **Fixed** cuts S03 mid-sentence: chunk 1 ends `"…presented in a manner that"` and chunk 2 starts `"will reflect credit on the student…"`.
* **Page-paragraph-aware** keeps the whole §1.5.1 *Thesis Title* rule ("…should not contain more than 15 words…", rule ii on redundant phrases) in one chunk, cited as *UTM Thesis Manual*, p.6.

| Approach / Configuration A | Approach / Configuration B | Observed evidence | Selected approach and trade-off |
| :--- | :--- | :--- | :--- |
| Fixed 500-character chunks over the whole document (lab baseline) | Paragraph-aware 500-character chunks applied **within each page** | A: 300 chunks; 71.7 % start mid-sentence; no page reference. B: 420 chunks; 22.9 % start mid-sentence; 100 % carry a page number; the title rule is preserved intact on p.6 | **B.** It is the only configuration that can meet FR4/RAI1 (title + page for every passage), and it keeps rule passages readable for FR1/FR3. Accepted: more and shorter chunks, and paragraphs that cross a page break are split. |

The lab's whole-document paragraph-aware splitter was also measured. Its boundaries are similar to B, but it sets `page = None`, so it was rejected for FR4.

## 4. Chosen preparation configuration

| Setting | Value |
| :--- | :--- |
| Method | `page-paragraph-aware`: the lab paragraph-aware splitter applied to each page's text. Paragraphs longer than 500 characters fall back to fixed 500-character pieces. |
| Chunk size | 500 characters (lab default; not yet tuned for retrieval) |
| Cleaning | Collapse runs of spaces/tabs only. No header removal, spelling correction or de-duplication. |
| Metadata per chunk | `record_id`, `source`, `source_id`, `document_title`, `page`, `source_sha256`, `processing` |
| Output | `data/processed/prepared_text.jsonl` (Part B) = `data/pipeline_v1/documents/prepared_text.jsonl` (Part C) |

**Benefit:** every passage can be cited with document title and page, which makes the RAI1 target of 100 % citation presence achievable. Chunk boundaries follow paragraphs: 22.9 % of chunks start mid-sentence, against 71.7 % for fixed-size chunks.

**Accepted limitations:**
* A paragraph that spans a page break becomes two chunks.
* 48 short chunks (mostly running headers on their own) are kept.
* `page` is the physical PDF page, not always the printed number.
* This is not semantic or heading-aware chunking, so section numbers are not stored as a separate field.

## 5. ETL / ELT decision — **ETL**

Text is **extracted** (pypdf), **transformed** (cleaned, chunked, enriched with metadata) and then **loaded** as a JSONL corpus. A later topic will load that corpus into a vector index. ETL fits because the retriever can only use clean, chunked, citation-ready text; loading raw PDFs into a store and transforming them there would gain nothing. The raw PDFs are still kept unchanged in `data/raw/`, so the corpus can always be rebuilt with a different chunking decision. This keeps the main benefit of ELT.

## 6. Batch / streaming decision — **Batch**

The sources are institutional handbooks that change at most once per semester. A batch rebuild of the whole corpus is fast; a full run plus the Cell 9 rerun finishes well under a minute locally, which meets NFR4 (< 10 min). Streaming would add complexity with no benefit. Re-run the batch pipeline whenever a source PDF is added or replaced, then update the inventory version.

## 7. Output locations

| Artefact | Location |
| :--- | :--- |
| Raw sources | `data/raw/fyp_documents/` |
| Part B prepared corpus | `data/processed/prepared_text.jsonl` |
| Part B lineage manifest | `data/week3_manifest.json` |
| Part C pipeline output and run manifest | `data/pipeline_v1/documents/prepared_text.jsonl`, `data/pipeline_v1/documents/pipeline_manifest.json` |
| Diagram | `docs/week3_pipeline_diagram.png` (source: `docs/week3_pipeline_diagram.drawio`) |

## 8. Stage contracts

| Stage | Contract / evidence | Failure response | Implemented in |
| :--- | :--- | :--- | :--- |
| Ingest | The selected folder `fyp_documents` exists; only `.pdf`/`.txt` files are taken; each source gets a SHA-256 | Stop on a missing folder or no valid input (`FileNotFoundError` / `ValueError`) | Cell 7 `resolve_source`, `execute_pipeline` |
| Inspect | Every source is readable and has extractable text; pages and characters are recorded | Stop: "No extractable text found … may require OCR" | Cell 8A `inspect_documents`; Step A3 |
| Prepare | The chosen Part B method yields non-empty chunks, each with a page | Stop on an unknown method or zero chunks | Cell 8A `prepare_documents`; Cell 5A2 |
| Validate | The output exists and is non-empty; output SHA-256 and bytes are recorded; read-back equals what was written; IDs are unique; no chunk is empty | Stop on missing or empty output, or a read-back mismatch | `execute_pipeline` VALIDATE; Cell 5A2; B3 |
| Record lineage | Each source is mapped to its output with a record count; manifests are saved; a rerun reproduces the same hashes | Stop and report on a checksum mismatch or missing mapping | Cells 6A/6B, 9, 9B |

## 9. Failure points (annotated in the diagram)

1. **Wrong or changed source.** Example: a handbook is replaced by a new edition, or the wrong file is selected. *Detection:* the SHA-256 no longer matches (Cell 6B / Cell 9), or record counts change. *Response:* stop, update the inventory version, re-run the batch pipeline and re-register lineage.
2. **Unusable text.** Example: a scanned or image-only PDF, or garbled extraction such as S02's letter-spaced headings. *Detection:* the empty-text check raises, or near-empty pages are counted in A3. *Response:* stop, flag the source in the inventory, then add OCR or replace the source before re-ingesting.

## 10. Provenance and access caveats

* The PDFs are UTM documents committed to a **public** repository; redistribution permission has not been verified. If it is not permitted, remove the raw PDFs and keep only URLs plus SHA-256 checksums.
* The origin URLs of S01 and S02 were not recorded at download time and should be confirmed.
* S01 (Mechanical Engineering) and S02 (Master's) are **proxies** for Faculty of Computing undergraduate rules. Answers grounded in them may be wrong for computing students, which is a risk to NFR2 correctness. Their replacement is a priority before evaluation.
* The SHA-256 checks prove identity, not quality or permission. Data-quality and fitness-for-purpose checks belong to Topic 4.
