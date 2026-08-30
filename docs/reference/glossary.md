---
title: "Terminology & Concept Glossary"
---

# 🏷️ Terminology & Concept Glossary

Key health data, ResDAC, CMS, and search engine concepts used across `rkb-rs`.

---

## 🏥 Health Data & CMS Terminology

- **ResDAC (Research Data Assistance Center)**: A CMS-funded contractor providing technical assistance and documentation for researchers using Medicare, Medicaid, and related CMS data.
- **CMS**: Centers for Medicare & Medicaid Services.
- **FFS (Fee-for-Service)**: Traditional Medicare Parts A (Hospital) and B (Medical) coverage.
- **Carrier File (Part B)**: Medicare Fee-for-Service claims submitted by non-institutional providers (physicians, physician assistants, independent laboratories).
- **Inpatient File (Part A)**: Claims for inpatient hospital stays.
- **Outpatient File (Part B)**: Claims for institutional outpatient hospital services.
- **MBSF (Master Beneficiary Summary File)**: Annual summary file containing beneficiary demographic, enrollment, and chronic condition flags.
- **BENE_ID**: Beneficiary Identifier; an encrypted unique identifier for an individual Medicare or Medicaid beneficiary.
- **MedPAR (Medicare Provider Analysis and Review)**: File summarizing inpatient hospital and skilled nursing facility stays into single episode records.

---

## ⚙️ rkb-rs Engine Terminology

- **Provenance**: A traceable audit trail connecting any extracted snippet or variable back to its exact source URL, local file path, page number, and SHA-256 hash.
- **Sliding-Window Chunking**: Partitioning long documents into overlapping segments (e.g. 500 words with 100-word overlap) to preserve local context across boundaries.
- **FTS5**: SQLite's built-in Full-Text Search extension, providing fast BM25 lexical ranking.
- **Hybrid Retrieval**: Combining lexical keyword search scores (BM25) with vector similarity embeddings for optimal recall and semantic accuracy.
- **Agent Context**: Deterministic, citation-preserving prompt blocks designed for LLMs to generate grounded answers without hallucination.
- **MCP (Model Context Protocol)**: An open standard for connecting AI models to local or remote tools and data sources.
