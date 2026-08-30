---
title: "Architecture & Design Invariants"
---

# 🏗️ Architecture & Design Invariants

`rkb-rs` is engineered with a strict functional-first philosophy in Rust, prioritizing provenance, immutability, and deterministic behavior.

---

## 🏛️ Layered Design

```text
CLI (Clap)  ──>  Typed Configuration  ──>  Pure Core Logic  ──>  I/O Boundary Adapters  ──>  Durable Artifacts
```

1. **CLI Layer (`src/main.rs`, `src/cli.rs`)**:
   - Parses arguments and environment variables.
   - Maps inputs to typed command options.
   - Handles OS signals, terminal rendering, and process exit codes.

2. **Domain Configuration Boundary (`src/config.rs`)**:
   - Strictly validates inputs (e.g. valid URL structure, non-NaN semantic weights, bounded chunk overlap).
   - Rejects invalid configurations before any side effects occur.

3. **Pure Core Logic (`src/variables.rs`, `src/parse.rs`, `src/qa.rs`, `src/retrieval.rs`)**:
   - Deterministic algorithms: sliding-window chunking, HTML AST traversal, graph edge generation, exact identifier boosting.
   - Zero side-effects or network calls inside pure transform functions.

4. **I/O & Persistence Adapters (`src/archive.rs`, `src/inventory.rs`, `src/paths.rs`)**:
   - Network requests, filesystem writes, and SQLite database interactions occur solely at the boundary.

---

## 🔒 Provenance Invariant Rules

1. **No Phantom Facts**: Every extracted variable, chunk, or metadata record must point to a real, downloaded file in `data/raw/` and a corresponding entry in `manifests/archive_manifest.csv`.
2. **Cryptographic Signatures**: All raw files and chunks retain SHA-256 digests. If a local file is altered, `rkb qa` immediately flags it as corrupted.
3. **CSV/JSONL Canon**: Text files (CSV and JSONL) are the single source of truth. The SQLite database is purely a derived, throwaway serving index that can be regenerated on demand with `rkb index`.
4. **Hermetic Testing**: Unit and integration tests run entirely hermetically without hitting live networks, using pinned baseline fixtures in `tests/fixtures/python-baseline/`.
