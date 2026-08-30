---
title: "5-Minute Quickstart Tutorial"
---

# ⚡ 5-Minute Quickstart Tutorial

This tutorial walks you through setting up a complete, working CMS documentation knowledge base on your local machine in five easy steps.

---

## 🎯 What You Will Accomplish

By the end of this tutorial, you will:
1. Discover public ResDAC CMS documentation pages (`inventory`).
2. Download a sample of documents locally (`archive`).
3. Extract clean dataset and document metadata (`extract`).
4. Parse raw HTML, PDF, and XLSX documents into searchable text chunks (`parse`).
5. Build an atomic SQLite search index and query it with citations (`index` & `search`).

---

## Step 1: Initialize Your Workspace & Run Inventory

Create a working directory for your documentation archive:

```bash
mkdir my-cms-kb && cd my-cms-kb
```

Discover the first 3 pages of ResDAC documentation:

```bash
rkb inventory --max-pages 3
```

**What happened?**
- `rkb` crawled the ResDAC CMS data listing.
- Discovered pages and file links were saved into `manifests/site_inventory.csv`.
- Crawl progress was logged to `_workspace/02_inventory_progress.jsonl`.

---

## Step 2: Download Document Samples (`archive`)

Now download the first 5 files from your inventory checklist:

```bash
rkb archive --max-downloads 5
```

**What happened?**
- `rkb` downloaded HTML pages, PDFs, and data dictionary spreadsheets into `data/raw/`.
- Each file was verified with a SHA-256 cryptographic checksum.
- Successful downloads were recorded in `manifests/archive_manifest.csv`.

> [!NOTE]
> `rkb` includes a built-in polite delay (0.5s by default) between downloads to prevent overloading the public server.

---

## Step 3: Extract Metadata (`extract`)

Extract dataset listings and document associations:

```bash
rkb extract
```

**What happened?**
- `rkb` parsed HTML dataset tables and parent-child relations.
- Created `data/metadata/datasets.csv` (catalog of CMS datasets such as Carrier, Inpatient, MBSF).
- Created `data/metadata/documents.csv` (catalog of user manuals, data dictionaries, and methodology briefs).

---

## Step 4: Parse & Chunk Documents (`parse`)

Convert downloaded files into overlapping, search-ready text chunks:

```bash
rkb parse --chunk-size 500 --chunk-overlap 100
```

**What happened?**
- `rkb` opened HTML, PDF, and XLSX files, extracted clean body text, and sliced them into 500-word sliding windows with 100-word overlaps.
- Produced `data/parsed/chunks.jsonl`, where every line contains a searchable paragraph snippet with exact document and page lineage.

---

## Step 5: Build Search Index & Query

Compile your metadata and chunks into an SQLite FTS5 search index:

```bash
rkb index
```

Now search your local documentation:

```bash
rkb search --query "BENE_ID" --limit 3
```

*Example output:*
```text
Match 1: [Chunk c_a1b2c3d4] Score: 18.42
Document: doc_carrier_claims_manual (Page 12)
URL: https://resdac.org/cms-data/files/carrier-claims-2020.pdf
Text: "The Beneficiary Identifier (BENE_ID) uniquely identifies a Medicare beneficiary across claim types and enrollment periods..."
```

To format search results directly as citation-preserving context for an AI prompt:

```bash
rkb agent-context --query "BENE_ID"
```

🎉 **Congratulations!** You now have a working, verifiable CMS documentation knowledge base running entirely on your machine.
