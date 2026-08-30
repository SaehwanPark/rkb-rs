---
title: "Release & Distribution Runbook"
---

# 🔢 Release & Distribution Runbook

This guide covers release procedures, preflight verification, crates.io publication, and Homebrew tap updates for `rkb-rs`.

---

## ⚙️ Configuration Files

- `release.toml`: Central release metadata configuration (package names, tap targets, preflight checks).
- `Cargo.toml`: Package dependencies, versions, and publication allowlists.
- `.github/workflows/release.yml`: Automated GitHub Actions release workflow.

---

## 📋 Preflight Verification Checklist

Run these local checks before publishing any release:

```bash
# 1. Run formatting, linting, tests, and documentation checks
scripts/release-check

# 2. Verify package contents and dry-run cargo publish
scripts/release-package

# 3. Verify release targets and dist planning
scripts/release-plan
```

---

## 🏷️ Publishing a Release

Releases are tag-driven:

```bash
# Create and push a semantic version tag
git tag v0.1.0
git push origin v0.1.0
```

The GitHub Actions workflow will automatically:
1. Validate package contents.
2. Publish `rkb-rs` to [crates.io](https://crates.io/crates/rkb-rs).
3. Build cross-platform binaries (Linux x86_64, macOS Apple Silicon, macOS Intel).
4. Create the GitHub Release with attached binaries and release notes.
5. Update the Homebrew formula in `SaehwanPark/homebrew-tap`.

---

## 🧪 Post-Release Smoke Testing

Verify public distribution channels:

```bash
# 1. Test crates.io install
cargo install rkb-rs
rkb --version

# 2. Test Homebrew install
brew update
brew install SaehwanPark/tap/rkb-rs
rkb --version
```
