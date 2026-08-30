---
title: "Search & Agent Context Guide"
---

# 🔍 Search & Agent Context Guide

`rkb-rs` provides high-performance lexical search and hybrid search capabilities designed for both human analysts and autonomous AI agents.

---

## 🗄️ Building the Index (`rkb index`)

Before searching, compile your metadata and chunks into an SQLite FTS5 database:

```bash
# Standard Lexical Index
rkb index

# Build with Deterministic Embeddings Table for Hybrid Search
rkb index --build-embeddings
```

### What happens during indexing?
- `rkb` creates `data/index/retrieval.sqlite`.
- Inserts indexed records for datasets, documents, variables, and text chunks.
- Configures full-text search (FTS5) tables with BM25 ranking.
- If `--build-embeddings` is supplied, populates the `record_embeddings` vector table.

---

## 🔎 Lexical & Exact-Identifier Search (`rkb search`)

Query the knowledge base using full-text search:

```bash
# Search for a variable identifier
rkb search --query "BENE_ID" --limit 5

# Search for medical terminology
rkb search --query "inpatient deductible" --limit 5

# Output in JSON format for automated pipelines
rkb search --query "dual eligibility" --json
```

### Exact Identifier Boost:
`rkb` automatically boosts exact variable names and document IDs (e.g. `BENE_ID`, `CLM_FROM_DT`) so technical matches rank above loose keyword mentions.

---

## ⚡ Hybrid Retrieval (`--hybrid`)

To combine lexical BM25 search with vector similarity reranking:

```bash
rkb search --query "beneficiary enrollment" --hybrid --semantic-weight 0.4
```

### Options:
- `--hybrid`: Activates hybrid fusion scoring.
- `--semantic-weight <FLOAT>`: Balances keyword relevance vs semantic vector similarity (Range: `0.0` to `1.0`, default `0.5`).

---

## 🤖 Generating Agent-Context (`rkb agent-context`)

When building LLM agents, feeding raw search results often leads to lost citation links or hallucinations. `rkb agent-context` formats retrieved evidence into deterministic, citation-preserving context blocks:

```bash
# Standard agent context
rkb agent-context --query "BENE_ID" --limit 5

# JSON structure for structured tool returns
rkb agent-context --query "BENE_ID" --json

# Hybrid reranked agent context
rkb agent-context --query "MBSF Part D drug coverage" --hybrid
```

### Example Context Output:
```text
=== RESDAC DOCUMENTATION CONTEXT ===
Query: BENE_ID
Retrieved: 2 records

[CITATION 1]
Source: ResDAC Variable Dictionary (BENE_ID)
URL: https://resdac.org/cms-data/variables/bene-id
Document ID: doc_mbsf_summary
Text: The Beneficiary ID is an encrypted 8-character string assigned by CMS to uniquely identify an individual beneficiary across Medicare enrollment and claims files.

[CITATION 2]
Source: Carrier Claims User Manual (Page 18)
URL: https://resdac.org/cms-data/files/carrier-claims-2020.pdf
Document ID: doc_carrier_claims
Text: BENE_ID links carrier claims to corresponding inpatient and outpatient hospital events.
====================================
```

> [!TIP]
> Use `rkb agent-context` directly inside AI Agent tools (e.g., LangChain, LlamaIndex, or MCP servers) to provide accurate grounded facts with provenance URLs.
