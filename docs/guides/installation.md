---
title: "Installation & Setup Guide"
---

# 🚀 Installation & Setup Guide

`rkb-rs` is distributed as a single lightweight binary (`rkb`) compiled with zero native C-library runtime requirements beyond SQLite (which is bundled statically).

---

## 📦 Method 1: Install via Cargo (Recommended for Rust Users)

If you already have Rust and Cargo installed via [rustup.rs](https://rustup.rs):

```bash
cargo install rkb-rs
```

This downloads, compiles, and installs `rkb` into your Cargo binary directory (typically `~/.cargo/bin`). Ensure `~/.cargo/bin` is in your shell's `PATH`.

To update to the latest version at any time:

```bash
cargo install --force rkb-rs
```

---

## 🍺 Method 2: Install via Homebrew (macOS & Linux)

If you use Homebrew on macOS or Linux:

```bash
# Add the tap and install
brew install SaehwanPark/tap/rkb-rs
```

To upgrade later:

```bash
brew update
brew upgrade rkb-rs
```

---

## 🛠️ Method 3: Build from Source

If you want to contribute or build the latest development branch directly:

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/SaehwanPark/rkb-rs.git
   cd rkb-rs
   ```

2. **Run Tests to Verify**:
   ```bash
   cargo test --all-targets --all-features
   ```

3. **Build the Release Binary**:
   ```bash
   cargo build --release
   ```

   The compiled executable will be located at `target/release/rkb`. You can copy or link it into your system `PATH`:
   ```bash
   sudo cp target/release/rkb /usr/local/bin/
   ```

---

## ✅ Verify Installation

Run the following commands in your terminal to confirm that `rkb` is installed and ready:

```bash
rkb --version
```
*Expected output:*
```text
rkb 0.1.0
```

To see all available commands:

```bash
rkb --help
```

---

## 💻 System & Platform Requirements

| Platform | Supported Architecture | Notes |
| :--- | :--- | :--- |
| **Linux** | x86_64, aarch64 | Fully supported. Standard GLIBC / Musl. |
| **macOS** | Apple Silicon (M1/M2/M3/M4), Intel (x86_64) | Fully supported on macOS 12+ (Monterey, Ventura, Sonoma, Sequoia). |
| **Windows** | x86_64, aarch64 | Supported natively and via WSL2 (Windows Subsystem for Linux). WSL2 recommended for bash script parity. |

> [!TIP]
> **Shell Autocompletion**: `rkb` commands follow standard POSIX CLI conventions. Flags should always be passed after the subcommand name (e.g. `rkb search --query "BENE_ID"`, not `rkb --query "BENE_ID" search`).
