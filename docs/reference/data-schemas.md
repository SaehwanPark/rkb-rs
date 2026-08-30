---
title: "Data Artifacts & Schemas Reference"
---

# 🗂️ Data Artifacts & Schemas Reference

`rkb-rs` uses standardized CSV, JSON, and JSONL schemas to maintain strict provenance and interoperability.

---

## 1. `manifests/site_inventory.csv`

Records web pages and assets discovered during the `inventory` stage.

| Column | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `url` | String | Fully qualified URL. | `https://resdac.org/cms-data/files/carrier-ffs` |
| `kind` | String | Enum: `Listing`, `Dataset`, `Asset`, `Unknown`. | `Dataset` |
| `depth` | Integer | Link distance from starting root URL. | `1` |

---

## 2. `manifests/archive_manifest.csv`

The cryptographic ledger of locally preserved files.

| Column | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `url` | String | Source URL of the resource. | `https://resdac.org/sites/default/files/carrier.pdf` |
| `local_path` | String | Relative path inside `data/raw/`. | `data/raw/carrier.pdf` |
| `sha256` | String | Hexadecimal SHA-256 hash of file content. | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| `downloaded_at` | String | ISO 8601 UTC timestamp. | `2026-06-22T14:32:00Z` |
| `status` | String | `Success`, `Failed`, or `Skipped`. | `Success` |

---

## 3. `data/metadata/datasets.csv`

Catalog of CMS datasets derived during `extract`.

| Column | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `dataset_id` | String | Unique identifier for dataset. | `dataset_carrier_ffs` |
| `title` | String | Human-readable title. | `Fee-for-Service Carrier Claims` |
| `category` | String | Category grouping. | `Claims` |
| `source_url` | String | ResDAC webpage URL. | `https://resdac.org/cms-data/files/carrier-ffs` |
| `years_available` | String | Available year ranges. | `1999-2022` |

---

## 4. `data/metadata/documents.csv`

Catalog of technical documentation guides and data dictionaries.

| Column | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `document_id` | String | 10-char SHA-1 derived document hash. | `doc_4a9b2c8e1f` |
| `title` | String | Document title. | `Carrier Claims File User Manual` |
| `file_path` | String | Local path to preserved document. | `data/raw/carrier_manual.pdf` |
| `source_url` | String | Source download URL. | `https://resdac.org/sites/default/files/carrier_manual.pdf` |
| `sha256` | String | Cryptographic SHA-256 digest. | `a8b7c6d5...` |

---

## 5. `data/parsed/chunks.jsonl`

The search-ready, sliding-window text chunk stream. Each line is a valid JSON object:

```json
{
  "chunk_id": "chunk_4a9b2c8e1f_0001",
  "document_id": "doc_4a9b2c8e1f",
  "source_url": "https://resdac.org/sites/default/files/carrier_manual.pdf",
  "page_number": 12,
  "start_word_index": 0,
  "end_word_index": 498,
  "word_count": 498,
  "text": "The Medicare Carrier File contains Fee-for-Service claims submitted by professional providers such as physicians, physician assistants, and clinical social workers...",
  "checksum": "f1d2d3e4..."
}
```

---

## 6. `data/metadata/variables.csv`

Extracted CMS variable records with provenance evidence.

| Column | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `variable_id` | String | Uppercase variable identifier. | `BENE_ID` |
| `short_name` | String | Short descriptive name. | `Beneficiary ID` |
| `long_name` | String | Full descriptive title. | `Encrypted Beneficiary Identifier` |
| `definition` | String | Extracted plain-language definition. | `Unique individual beneficiary identifier...` |
| `source_url` | String | ResDAC variable definition URL. | `https://resdac.org/cms-data/variables/bene-id` |
| `evidence_chunk_id` | String | Foreign key to `chunks.jsonl`. | `chunk_4a9b2c8e1f_0001` |
