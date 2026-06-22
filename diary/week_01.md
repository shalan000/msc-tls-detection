# Week 1 — Environment Setup and Foundation

## Day 1 — [22 June 2026]

### Goals for today
- Set up complete development environment for the dissertation project
- Verify all required tools and libraries install and run correctly
- Establish project structure, version control, and remote backup

### Work completed

**WSL and operating system**
- Installed Windows Subsystem for Linux 2 with Ubuntu 22.04
- Confirmed the distribution runs and provides a working Linux environment

**Project structure and version control**
- Created project folder at `~/dissertation` inside WSL with subfolders for data, notebooks, src, results, report, diary, and scripts
- Initialised Git repository locally
- Created private GitHub repository `shalan000/msc-tls-detection`
- Set up `.gitignore` to exclude large PCAP files and the virtual environment
- Configured Personal Access Token authentication for Git push

**Python environment**
- Installed Python 3.11 and created an isolated virtual environment at `~/dissertation/.venv`
- Installed the full machine learning stack:
  - numpy 1.26.4
  - pandas 3.0.3
  - scikit-learn 1.9.0
  - xgboost 3.2.0
  - shap 0.51.0
  - imbalanced-learn (for SMOTE)
  - matplotlib, seaborn (for visualisation)
  - scapy (for packet-level work)
  - jupyter and ipykernel (for notebooks)

**Network analysis tools**
- Installed Zeek 8.0.5 from the official openSUSE repository
- Installed tshark (Wireshark command-line tool) for PCAP inspection
- Installed cicflowmeter (Python implementation) for automated flow feature extraction

**IDE setup**
- Installed VS Code on Windows with the WSL extension
- Installed Python and Jupyter extensions on the WSL side
- Connected VS Code to the WSL distribution (confirmed by the `WSL: Ubuntu-22.04` badge)
- Registered the venv as a Jupyter kernel named `TLS Detection (3.11)`

**Verification**
- Created `notebooks/00_environment_check.ipynb` to confirm the full stack
- Successfully imported all six core libraries
- Trained a sample XGBoost classifier on synthetic data — score 0.986
- Confirmed the kernel runs inside the venv, not against system Python

### Decisions made and rationale

**Chose WSL2 + Ubuntu over native Windows.** Zeek, CICFlowMeter, and most packet analysis tools are Linux-first. Native Windows compatibility is patchy. WSL2 gives a real Linux environment with no virtualisation overhead worth worrying about.

**Chose WSL2 over a Kali Linux VM.** Kali is built for offensive security testing, not data science workloads. The Python ML stack is better supported on Ubuntu, and the project's actual work is data processing rather than network exploitation.

**Switched from pyenv to system Python 3.11.** Initial attempt to use pyenv for Python version management led to pip routing issues — packages were installing into the pyenv site-packages rather than the venv, despite the venv being activated. Diagnosed the cause as pyenv shims taking precedence over the venv's PATH. Switched to Ubuntu's system Python 3.11, which removed the conflict. Pyenv would be the better choice for a multi-version project, but is unnecessary overhead for a single-version dissertation.

**Chose the cicflowmeter Python implementation over the original Java version.** The Python port is easier to install, has fewer dependencies, and produces compatible output. If feature output differs from the documented CICFlowMeter spec later in the project, the Java version remains a fallback inside a Docker container.

### Problems encountered

**pyenv pip routing.** Around two hours lost to debugging why `pip install` inside an activated venv was writing to the pyenv-level Python instead. Resolution: switched to system Python entirely. Lesson: when a tooling decision creates friction in the first hour, replace it rather than fight it.

**Ubuntu mirror errors during package installation.** Several `400 Bad Request` errors from the UK Ubuntu archive mirror during `apt install`. Resolved using `--fix-missing` and switching to a different mirror.

**VS Code WSL mode confusion.** Multiple VS Code windows opened simultaneously — one in Windows mode connecting to WSL via network share (`\\wsl.localhost\...`), and one properly bridged via the WSL extension. The Windows-mode window could see files but could not run the venv kernel, producing the error `Python -1.-1.-1 is no longer available`. Resolution: ensure VS Code is launched only via `code .` from inside a WSL shell, and confirm the `WSL: Ubuntu-22.04` badge appears in the bottom-left corner before working.

### Notes for later

- The active kernel reports Python 3.11.0rc1 rather than 3.11.10. All packages work correctly, but investigate if behaviour issues arise during the actual project work.
- Daily startup routine: open Windows Terminal → select Ubuntu-22.04 → `cd ~/dissertation` → `source .venv/bin/activate` → `code .` if working in the editor.

### Plan for tomorrow

- Download the CIC-IDS2017 Monday PCAP file (smallest day, benign-only traffic — simplest to verify the pipeline)
- Run CICFlowMeter on it and inspect the output CSV
- Begin reading Anderson & McGrew (2016) on TLS metadata for malware classification
- Begin reading Sharafaldin et al. (2018) — the CIC-IDS2017 dataset paper, paying attention to documented dataset flaws

### Time spent today

Approximately 6 hours, including the debugging detours. Setup-only hours that would feed into the 600-hour project budget: roughly 4.