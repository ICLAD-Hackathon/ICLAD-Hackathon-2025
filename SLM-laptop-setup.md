# 🚀 ICLAD Hackathon 2025 — SLM Laptop Setup

Welcome to the **SLM (Small Language Model)** category of the ICLAD Hackathon 2025. This guide walks you through everything—from setting up your pre-configured laptop environment to understanding and evaluating the **Qualcomm AES Debugging Challenge**.

---

## 🧑‍💻 1. Login and Access Instructions

### ✅ System Credentials

- **Username**: `gmrd01`
- **Password**: `gmrd01`

### 💻 Launch Ubuntu via Windows Subsystem for Linux (WSL)

Open PowerShell and execute:

```bash
wsl --list
wsl -d Ubuntu-22.04
```

> 🧠 *Ubuntu-22.04 contains all necessary EDA tools pre-installed.*

---

## 📂 2. Folder Structure After Login

```plaintext
~/miniconda3/
~/iclad_hackathon/
```

## 📦 Clone the Hackathon Repository

```bash
cd ~/iclad_hackathon
git clone --recursive https://github.com/ICLAD-Hackathon/ICLAD-Hackathon-2025.git
```

---

## 🛠️ 3. EDA Tool Setup & Verification

All tools below are **pre-installed** in the Ubuntu image:

### ⚡ OpenROAD Flow

```bash
cd ~/iclad_hackathon/tools/OpenROAD-flow-scripts
source env.sh
cd flow/
make
```

> ✅ Look for successful GDSII generation as confirmation.

### 📘 Icarus Verilog (Simulation)

```bash
cd ~/iclad_hackathon/tools/iverilog_test
iverilog -o hello hello.v
vvp hello
```

Expected output:

```text
Hello, World!
```

### 📊 GTKWave (Waveform Viewer)

To view generated `.vcd` files:

```bash
gtkwave dump.vcd
```

---

### Using SLMs on the Laptop

Coming soon. 
