---
title: "Downstream Integration & Retrieval Evaluation Guide"
---

# 📊 Downstream Integration & Retrieval Evaluation Guide

`rkb-rs` provides specialized downstream research tools for CMS analysts as well as quantitative benchmarking tools to measure retrieval quality.

---

## 🛠️ Downstream Research Helpers (`rkb integration`)

The `integration` subcommand provides utility actions for common data preparation and analysis tasks:

### 1. Check Dataset Availability by Year
Verify whether a CMS dataset or file exists for specific calendar years:

```bash
# Check all available years for a dataset
rkb integration availability --dataset carrier-ffs

# Check if available in a specific year (prints true/false)
rkb integration availability --dataset carrier-ffs --year 2020
```

---

### 2. Variable Crosswalk Mapping
Find relationships, alternate names, and canonical groupings across multiple variables:

```bash
rkb integration crosswalk --variables BENE_ID,bene_id,MEDPAR_ID
```
*Emits JSON mapping source variables to their canonical definitions and dataset occurrences.*

---

### 3. Cohort Data Dictionary Generator
Generate a self-contained data dictionary for a specific research cohort's variable set:

```bash
rkb integration cohort-dictionary --variables BENE_ID,GNDR_CD,BENE_BIRTH_DT,CLM_DRG_CD
```
*Emits a unified JSON schema dictionary complete with data types, description text, and source ResDAC URLs.*

---

### 4. Format Prompt Context
Export context snippets in Markdown, XML, or prompt-ready text:

```bash
# Markdown format
rkb integration format-context --query "BENE_ID" --format markdown

# XML format for Claude / LLMs
rkb integration format-context --query "BENE_ID" --format xml
```

---

### 5. Scan Codebase Caveats
Scan research scripts (e.g. SAS, Stata, R, Python, SQL) for CMS data caveats and quirks:

```bash
rkb integration scan-caveats \
  --files analysis.sas \
  --keywords encounter,dual_eligibility
```
*Checks your analysis code against documented CMS gotchas (such as MedPAR short-stay adjustments or Medicare Advantage encounter data lags).*

---

## 📈 Retrieval Quality Evaluation (`rkb evaluate`)

`rkb evaluate` benchmarks the accuracy, MRR (Mean Reciprocal Rank), and citation validity of the search engine.

### 1. Seeded Variable Sample Evaluation
Run deterministic sanity checks against known canonical variables:

```bash
# Run with sample size 10 and deterministic seed
rkb evaluate --sample-size 10 --seed 20260616

# Emit JSON results for CI tracking
rkb evaluate --sample-size 10 --json
```

---

### 2. Full Benchmark Question Suite
Run an end-to-end benchmark comparison across lexical, hybrid, and agent-context retrievers:

```bash
rkb evaluate \
  --benchmark data/evaluation/benchmark_questions.json \
  --output-report _workspace/retrieval_evaluation_report.md
```

This generates a markdown benchmark report detailing:
- **Hit Rate @ K** (k=1, 3, 5)
- **Mean Reciprocal Rank (MRR)**
- **Citation Validity Score** (Percentage of citations resolving to verified source evidence)
- **Comparison between Lexical (BM25) vs Hybrid (Embeddings)**
