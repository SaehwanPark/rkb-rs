---
title: "Troubleshooting Guide"
---

# 🛟 Troubleshooting Guide

Common issues, error messages, and operational guidance for `rkb-rs`.

---

## 1. Flag Ordering & Syntax: "unexpected argument"

### ❌ Symptom:
```text
error: unexpected argument '--retry-failed-only' found
```

### 🔍 Cause:
`rkb` strictly requires flags to be placed **after** the subcommand name.

### ✅ Solution:
```bash
# Correct
rkb archive --retry-failed-only

# Incorrect
rkb --retry-failed-only archive
```

---

## 2. HTTP 429 Errors ("Too Many Requests")

### ❌ Symptom:
```text
Rate limited (HTTP 429). Backing off...
```

### 🔍 Cause:
The public ResDAC web server is asking `rkb` to reduce download frequency.

### ✅ Solution:
1. `rkb` automatically detects HTTP 429 and applies exponential backoff.
2. If rate limits persist, increase the delay between consecutive requests:
   ```bash
   rkb archive --request-delay-seconds 2.0 --rate-limit-cooldown-seconds 5
   ```
3. Use `--max-downloads` to download in smaller batches:
   ```bash
   rkb archive --max-downloads 25 --retry-failed-only
   ```

---

## 3. SQLite Search Index Out of Date

### ❌ Symptom:
Newly downloaded documents or updated metadata do not show up in `rkb search` results.

### 🔍 Cause:
The SQLite database (`data/index/retrieval.sqlite`) is a rebuildable serving snapshot. It must be re-indexed after updating raw or metadata files.

### ✅ Solution:
Rebuild the index atomically with:
```bash
rkb index
```
*(If hybrid search is needed, add `--build-embeddings`)*:
```bash
rkb index --build-embeddings
```

---

## 4. Permission Denied Writing to `data/` or `_workspace/`

### ❌ Symptom:
```text
Error: PermissionDenied (os error 13) when creating data/raw/...
```

### 🔍 Cause:
The current working directory does not have write permissions for your user account.

### ✅ Solution:
1. Ensure you are running `rkb` inside a directory where you have full read/write access.
2. You can customize output directories via CLI options:
   ```bash
   rkb archive --raw-root /path/to/my/writable/data/raw
   ```

---

## 5. Automated QA Reports a Non-Pass Verdict (`FIX` or `REDO`)

### ❌ Symptom:
`rkb qa` exits with a nonzero code and prints a list of findings.

### 🔍 Cause:
- `FIX`: Minor integrity issues, such as a missing optional file or a single orphaned citation link.
- `REDO`: Structural data failure, such as missing primary manifests or corrupted checksum records.

### ✅ Solution:
1. Open the detailed QA report generated at `_workspace/06_qa_review.md`.
2. Follow the specific remediation recommendations for each flagged file.
3. Re-run `rkb qa` until it reports `PASS`.
