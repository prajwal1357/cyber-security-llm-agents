# 🚀 Complete Beginner's Guide: Cyber-Security LLM Agents & MITRE Caldera

Welcome! This repository contains a multi-agent framework built on **Microsoft AutoGen** that uses Large Language Models (LLMs) to automate day-to-day cybersecurity, threat intelligence, and adversary emulation tasks.

---

## 📌 1. Project Overview & Architecture

This platform orchestrates specialized AI agents to perform complex cybersecurity workflows:

* **`task_coordinator_agent`**: Coordinates workflow execution and routes tasks.
* **`text_analyst_agent`**: Analyzes threat intelligence, summarizes data, and generates reports.
* **`internet_agent`**: Fetches online security advisories, CVE reports, and EDR telemetry matrices.
* **`caldera_agent`**: Communicates with **MITRE Caldera** REST API to query agents, retrieve abilities, and create adversary profiles.
* **`cmd_exec_agent`**: Runs local CLI utilities (e.g. `curl`, `jq`, PowerShell).

---

## 📋 2. Prerequisites & Requirements

Before setting up, make sure your computer meets the following requirements:

### Hardware Requirements
* **Operating System:** Windows 10/11, macOS, or Linux.
* **RAM:** Minimum 8 GB (16 GB or higher recommended when using local LLMs with Ollama).
* **Storage:** 5–10 GB free space (for Python packages, MITRE Caldera, and local model weights).

### Software Requirements
1. **Python 3.9 – 3.11** (Python 3.10 is recommended):
   ```powershell
   python --version
   ```
2. **Git**:
   ```powershell
   git --version
   ```
3. **LLM Provider (Choose ONE)**:
   * **Option A: Ollama (Recommended - Free & Offline)**
     * Download and install from [ollama.com/download](https://ollama.com/download).
   * **Option B: OpenAI API Key (Cloud-based)**
     * Active API key from [platform.openai.com](https://platform.openai.com/).

---

## 🛠️ 3. Step-by-Step Setup Guide (After `git clone`)

If you just cloned this repository, follow these steps to get everything running:

### Step 3.1: Set Up the LLM Backend

#### Option A: Local LLM using Ollama (Free & Offline)
1. Install **Ollama**.
2. Download the `llama3.1` model in PowerShell / Terminal:
   ```powershell
   ollama pull llama3.1
   ```
3. Ensure Ollama is running in the background.

#### Option B: Cloud LLM using OpenAI
1. Obtain an OpenAI API key.
2. You will configure this key in Step 3.3.

---

### Step 3.2: Install Agent Dependencies
Navigate into the project directory and install the required Python packages:
```powershell
cd cyber-security-llm-agents

# Install core packages
pip install -r requirements.txt

# CRITICAL: Install exact AutoGen version compatible with the API
pip install pyautogen==0.2.27
```

---

### Step 3.3: Environment Configuration (`.env`)
Create your local `.env` configuration file from the template:

* **On Windows PowerShell:**
  ```powershell
  copy .env_template .env
  ```
* **On macOS / Linux:**
  ```bash
  cp .env_template .env
  ```

#### For Ollama (Local Setup):
Edit `.env` to match the following configuration:
```env
LLM_WORKING_FOLDER="llm_working_folder"

CALDERA_SERVER="http://127.0.0.1:8888"
CALDERA_API_KEY="caldera_secret_key"

OPENAI_MODEL_NAME="llama3.1"
OPENAI_API_KEY="ollama"
OPENAI_BASE_URL="http://127.0.0.1:11434/v1"

WEB_SERVER_PORT=8800
FTP_SERVER_ADDRESS="127.0.0.1:2100"
FTP_SERVER_USER="user"
FTP_SERVER_PASS="12345"
```

#### For OpenAI (Cloud Setup):
```env
LLM_WORKING_FOLDER="llm_working_folder"

CALDERA_SERVER="http://127.0.0.1:8888"
CALDERA_API_KEY="caldera_secret_key"

OPENAI_MODEL_NAME="gpt-3.5-turbo"
OPENAI_API_KEY="sk-your-actual-openai-api-key"
OPENAI_BASE_URL="https://api.openai.com/v1"
```

---

### Step 3.4: Verify Code Fixes Applied

This repository includes critical fixes for AutoGen & Windows UTF-8 compatibility:
1. **`utils/constants.py`**: Added `OPENAI_BASE_URL = os.getenv("OPENAI_BASE_URL")`.
2. **`utils/shared_config.py`**: Configured `base_url` inside `llm_config`.
3. **`tools/web_tools.py`**: Added `encoding="utf-8"` to `subprocess.check_output` to prevent Windows character decoding crashes.

---

### Step 3.5: Set Up MITRE Caldera (Optional / For Caldera Scenarios)

If you plan to run Caldera-integrated scenarios (`TTP_REPORT_TO_ADVERSARY_PROFILE`, `DETECT_EDR`, etc.):

1. Open **Terminal #1** and navigate to the Caldera folder:
   ```powershell
   cd ../caldera
   ```
2. Install Caldera dependencies:
   ```powershell
   pip install -r requirements.txt
   pip install aioftp==0.21.4
   ```
3. Set your API Key in `conf/local.yml` (or `conf/default.yml`):
   ```yaml
   api_key_red: "caldera_secret_key"
   ```
4. Start Caldera Server:
   ```powershell
   python server.py --insecure
   ```
   > 💡 Caldera Web Dashboard will open at `http://localhost:8888`.

---

## 🎯 4. Available Scenarios & How to Run Them

Run any scenario using the command:
`python run_agents.py <SCENARIO_NAME>`

### 🟢 Category A: Standalone Scenarios (No Caldera Required)
Works immediately with your local Ollama or OpenAI setup:

1. **`HELLO_AGENTS`** (Sanity Test):
   ```powershell
   python run_agents.py HELLO_AGENTS
   ```
   * *Expected Output:* Agent conversation generating cybersecurity jokes.

2. **`TTP_REPORT_TO_TECHNIQUES`** (Threat Report Analysis):
   ```powershell
   python run_agents.py TTP_REPORT_TO_TECHNIQUES
   ```
   * *Expected Output:* Downloads Microsoft threat blog and extracts MITRE ATT&CK technique IDs (`T1003`, `T1059`).

3. **`IDENTIFY_EDR_BYPASS_TECHNIQUES`** (Telemetry Blind-Spots):
   ```powershell
   python run_agents.py IDENTIFY_EDR_BYPASS_TECHNIQUES
   ```
   * *Expected Output:* Identifies detection gaps for Elastic EDR from public telemetry matrices.

4. **`SUMMARIZE_RECENT_CISA_VULNS`** (CISA Vulnerabilities Digest):
   ```powershell
   python run_agents.py SUMMARIZE_RECENT_CISA_VULNS
   ```
   * *Expected Output:* Structured table of top 10 CISA vulnerabilities. *(Requires `jq`)*.

---

### 🟡 Category B: Caldera Integrated Scenarios (Caldera Server Required)
Requires MITRE Caldera server running at `http://127.0.0.1:8888`:

5. **`TTP_REPORT_TO_ADVERSARY_PROFILE`** (Automated Purple Teaming):
   ```powershell
   python run_agents.py TTP_REPORT_TO_ADVERSARY_PROFILE
   ```
   * *Expected Output:* Automatically creates a new Adversary Profile inside Caldera web UI.

6. **`DETECT_EDR`** (EDR Software Detection):
   ```powershell
   python run_agents.py DETECT_EDR
   ```
   * *Expected Output:* Correlates running Windows services against EDR signatures.

7. **`DETECT_AGENT_PRIVILEGES`** (Privilege Audit):
   ```powershell
   python run_agents.py DETECT_AGENT_PRIVILEGES
   ```
   * *Expected Output:* Table of user rights and privilege level determination (Standard User / Admin / SYSTEM).

8. **`HELLO_CALDERA`** (GUI Action):
   ```powershell
   python run_agents.py HELLO_CALDERA
   ```
   * *Expected Output:* Displays pop-up GUI joke dialog box on endpoint desktop.

9. **`COLLECT_CALDERA_INFO`** (API Discovery):
   ```powershell
   python run_agents.py COLLECT_CALDERA_INFO
   ```
   * *Expected Output:* Outputs active agent PAWs and operation IDs.

---

## 🌟 5. Major Project Presentation Guide

If you are demonstrating this project to a friend, professor, or interviewer:

### 🏆 Recommended Demo: `TTP_REPORT_TO_ADVERSARY_PROFILE`
1. Open **Terminal 1** and start Caldera: `python server.py --insecure`.
2. Open your web browser to `http://localhost:8888` (MITRE Caldera Web Console).
3. Open **Terminal 2** and run: `python run_agents.py TTP_REPORT_TO_ADVERSARY_PROFILE`.
4. **Show the magic:** Watch the AI agents fetch threat intelligence, map attack techniques, and create a live Adversary Profile inside the Caldera UI automatically!

---

## ❓ 6. Troubleshooting Matrix

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| `openai.APIConnectionError: [WinError 10061]` | Ollama service is not running. | Launch Ollama app or run `ollama serve`. |
| `AttributeError: module 'autogen' has no attribute ...` | Mismatched AutoGen package version. | Run `pip install pyautogen==0.2.27`. |
| `TypeError: asyncio.get_event_loop() ...` in Caldera | Incompatible `aioftp` version on Python 3.10+. | In `caldera/`, run `pip install aioftp==0.21.4`. |
| `UnicodeDecodeError` in web tool output | PowerShell default character encoding issue. | Ensure `encoding="utf-8"` is set in `tools/web_tools.py`. |
