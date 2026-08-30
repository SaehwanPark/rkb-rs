---
title: "CLI Command Reference"
---

# 📖 CLI Command Reference

Exhaustive reference for all 14 `rkb` subcommands.

---

## Command Matrix

| Command | Category | Description | Primary Output Artifact |
| :--- | :--- | :--- | :--- |
| `inventory` | Preservation | Crawls and discovers documentation pages. | `manifests/site_inventory.csv` |
| `archive` | Preservation | Downloads raw files and records checksums. | `manifests/archive_manifest.csv`, `data/raw/` |
| `extract` | Transformation | Scrapes datasets, documents, and edges. | `data/metadata/datasets.csv`, `documents.csv` |
| `parse` | Transformation | Extracts plain text and creates chunk streams. | `data/parsed/chunks.jsonl` |
| `variables` | Transformation | Catalogs variable definitions and linkages. | `data/metadata/variables.csv` |
| `qa` | Verification | Validates provenance, checksums, and references. | `_workspace/06_qa_review.md` |
| `index` | Serving | Compiles metadata into SQLite FTS5 database. | `data/index/retrieval.sqlite` |
| `search` | Serving | Queries SQLite index with ranked citations. | Standard Output / JSON |
| `agent-context` | Serving | Formats citation-preserving context for LLMs. | Standard Output / JSON |
| `mcp` | Serving | Serves stdio JSON-RPC MCP server for AI tools. | Standard IO |
| `mcp-setup` | Integration | Configures Claude/Antigravity/Codex client JSON. | Client config files |
| `integration` | Integration | Downstream availability, crosswalk, cohort tools. | Standard Output / JSON |
| `evaluate` | Verification | Evaluates retrieval accuracy & MRR benchmarks. | `_workspace/retrieval_evaluation_report.md` |
| `progress` | Monitoring | Summarizes JSONL progress logs. | Standard Output / JSON |

---

## 1. `rkb inventory`
Scan the ResDAC website to build an inventory of documentation files.

```bash
rkb inventory [OPTIONS]
```
- `--base-url <URL>`: Starting root URL (Default: `https://resdac.org/cms-data`).
- `--max-pages <NUMBER>`: Maximum search result pages to scan.
- `--output <PATH>`: Destination CSV path (Default: `manifests/site_inventory.csv`).
- `--request-delay-seconds <SECONDS>`: Delay between HTTP requests (Default: `0.5`).

---

## 2. `rkb archive`
Download raw documents and verify SHA-256 signatures.

```bash
rkb archive [OPTIONS]
```
- `--inventory <PATH>`: Inventory checklist CSV (Default: `manifests/site_inventory.csv`).
- `--raw-root <PATH>`: Raw files output directory (Default: `data/raw`).
- `--manifest-output <PATH>`: Output manifest CSV (Default: `manifests/archive_manifest.csv`).
- `--max-downloads <NUMBER>`: Maximum downloads before exiting.
- `--retry-failed-only`: Skip existing valid files; retry failed entries.
- `--max-consecutive-rate-limits <NUMBER>`: Maximum consecutive HTTP 429 errors allowed (Default: `5`).
- `--rate-limit-cooldown-seconds <SECONDS>`: Cooldown period after rate limit (Default: `0`).
- `--request-delay-seconds <SECONDS>`: Request delay in seconds (Default: `0.5`).

---

## 3. `rkb extract`
Scrape structural metadata from downloaded HTML files.

```bash
rkb extract [OPTIONS]
```
- `--archive-manifest <PATH>`: Archive manifest CSV (Default: `manifests/archive_manifest.csv`).
- `--metadata-dir <PATH>`: Metadata output directory (Default: `data/metadata`).
- `--graph-dir <PATH>`: Graph output directory (Default: `data/graph`).

---

## 4. `rkb parse`
Parse HTML, PDF, and XLSX documents into sliding-window text chunks.

```bash
rkb parse [OPTIONS]
```
- `--archive-manifest <PATH>`: Archive manifest CSV (Default: `manifests/archive_manifest.csv`).
- `--output-dir <PATH>`: Parsed files directory (Default: `data/parsed`).
- `--chunks-jsonl <PATH>`: Unified chunks stream (Default: `data/parsed/chunks.jsonl`).
- `--chunk-size <WORDS>`: Maximum words per chunk (Default: `500`).
- `--chunk-overlap <WORDS>`: Word overlap between consecutive chunks (Default: `100`).

---

## 5. `rkb variables`
Extract variable definitions, aliases, and provenance edges.

```bash
rkb variables [OPTIONS]
```
- `--chunks-jsonl <PATH>`: Parsed chunks stream (Default: `data/parsed/chunks.jsonl`).
- `--archive-manifest <PATH>`: Archive manifest CSV (Default: `manifests/archive_manifest.csv`).
- `--metadata-dir <PATH>`: Output metadata directory (Default: `data/metadata`).
- `--graph-dir <PATH>`: Output graph directory (Default: `data/graph`).

---

## 6. `rkb qa`
Perform comprehensive integrity and provenance validation.

```bash
rkb qa [OPTIONS]
```
- `--workspace-dir <PATH>`: QA report output directory (Default: `_workspace`).
- Output report written to `<workspace-dir>/06_qa_review.md`. Returns exit code 0 for `PASS`, 1 for `FIX`, 2 for `REDO`.

---

## 7. `rkb index`
Compile canonical CSV and JSONL artifacts into a local SQLite FTS5 index.

```bash
rkb index [OPTIONS]
```
- `--database-path <PATH>`: Target SQLite database file (Default: `data/index/retrieval.sqlite`).
- `--build-embeddings`: Populate deterministic vector embeddings table for hybrid retrieval.

---

## 8. `rkb search`
Execute full-text and hybrid queries against the SQLite index.

```bash
rkb search --query <STRING> [OPTIONS]
```
- `--query <STRING>`: Search term or variable name (Required).
- `--limit <NUMBER>`: Maximum results to return (Default: `5`).
- `--json`: Format output as JSON.
- `--hybrid`: Enable hybrid lexical + embedding fusion ranking.
- `--semantic-weight <FLOAT>`: Semantic weight balance for hybrid scoring (Default: `0.5`).

---

## 9. `rkb agent-context`
Format search results as citation-grounded context blocks for LLMs.

```bash
rkb agent-context --query <STRING> [OPTIONS]
```
- `--query <STRING>`: Search query (Required).
- `--limit <NUMBER>`: Maximum citations to include (Default: `5`).
- `--json`: Output as structured JSON context.
- `--hybrid`: Use hybrid search for evidence ranking.

---

## 10. `rkb mcp`
Run the Model Context Protocol stdio JSON-RPC server.

```bash
rkb mcp
rkb mcp start --host <HOST> --port <PORT>
rkb mcp status
rkb mcp stop
```

---

## 11. `rkb mcp-setup`
Configure MCP client configuration files.

```bash
rkb mcp-setup --client <CLIENT> [OPTIONS]
```
- `--client <NAME>`: One of `claude-desktop`, `claude-code-project`, `claude-code-user`, `antigravity`, or `codex-project`.
- `--project-path <PATH>`: Target project directory (Default: `.`).
- `--dry-run`: Preview config changes without modifying files.

---

## 12. `rkb integration`
Execute downstream health-data utilities.

```bash
rkb integration <SUBCOMMAND> [OPTIONS]
```
- `availability --dataset <NAME> [--year <YEAR>]`
- `crosswalk --variables <VARS>`
- `cohort-dictionary --variables <VARS>`
- `format-context --query <STRING> [--format markdown|xml|prompt]`
- `scan-caveats --files <FILES> --keywords <KEYWORDS>`

---

## 13. `rkb evaluate`
Benchmark retrieval accuracy and citation precision.

```bash
rkb evaluate [OPTIONS]
```
- `--sample-size <NUMBER>`: Sample size for seeded evaluation.
- `--seed <NUMBER>`: Deterministic PRNG seed.
- `--benchmark <PATH>`: Path to benchmark question JSON suite.
- `--output-report <PATH>`: Path to write markdown evaluation report.

---

## 14. `rkb progress`
Summarize inventory and archival event logs.

```bash
rkb progress [OPTIONS]
```
- `--log <PATH>`: Explicit log file to summarize.
- `--json`: Output progress rollups as JSON.
