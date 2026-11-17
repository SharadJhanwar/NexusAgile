# ⚡ NexusAgile: Enterprise Multi-Agent Agile Orchestrator & Governance Engine

**NexusAgile** is an enterprise-grade multi-agent sprint orchestration and delivery governance system. It connects directly to Atlassian Jira using the **Model Context Protocol (MCP)** to automate backlog triaging, dynamic workload rebalancing, sprint shaping, continuous risk detection, and standup synthesis—while enforcing strict **Human-in-the-Loop (HITL)** approval gates and defense-in-depth prompt isolation.

---

## 📋 Architecture & Prerequisites

1. **Python 3.8+** (for environment utilities, validation scripts, and team-map policy parsing).
2. **Atlassian Jira MCP Server** (`mcp-atlassian` installed and running in your agent/assistant framework).
3. **Agent CLI / Orchestrator** (e.g. Codex, Claude, or custom LLM runners with MCP support).

---

## 🛠️ Step-by-Step Setup

### Step 1: Configure the Team Map

NexusAgile relies on a declarative team map to understand engineer skills, capacity points, WIP limits, and approval authorities.

1. Navigate to the `references/` directory.
2. Copy `team-map.example.yaml` and rename it to `team-map.local.yaml`:
   ```bash
   cp references/team-map.example.yaml references/team-map.local.yaml
   ```
3. Open `team-map.local.yaml` and configure your:
   * **Jira Board ID** and **Project Key** (e.g., `ENG`, `PROD`, or your team's project key).
   * **Assigning Authorities** (e.g., Team Lead or Engineering Manager).
   * **Team Directory** (engineers, component ownership, skill tags, capacity, and max WIP limits).

---

### Step 2: Initialize Python Virtual Environment

Helper utilities and policy validators use a Python virtual environment:

```bash
python -m venv .venv-jira-tools
source .venv-jira-tools/bin/activate  # On Windows: .venv-jira-tools\Scripts\activate
pip install pyyaml  # Dependency for team map & policy parsing
```

---

### Step 3: Install the Skill

Symlink or copy the skill into your agent framework's skills directory:

```bash
./automation/nexusagile-install-skill.sh
```

---

### Step 4: Validate Configuration

Run the validator script to verify YAML structure and prompt consistency:

```bash
./automation/nexusagile-validate-skill.sh
```

---

## 🚀 Operational Modes & CLI Execution

NexusAgile provides ready-to-run automation scripts in the `automation/` directory:

* **Triage Backlog Queue**:
  ```bash
  ./automation/nexusagile-triage-queue.sh
  ```
* **Recommend Assignees / Owners**:
  ```bash
  ./automation/nexusagile-suggest-owner.sh --issue-keys "ENG-101"
  ```
* **Shape Sprint Scope**:
  ```bash
  ./automation/nexusagile-shape-sprint.sh --board-id 1234
  ```
* **Inspect Sprint Health**:
  ```bash
  ./automation/nexusagile-inspect-sprint.sh
  ```
* **Daily Standup Brief**:
  ```bash
  ./automation/nexusagile-standup-brief.sh
  ```
* **Surface Delivery Risks**:
  ```bash
  ./automation/nexusagile-surface-risk.sh
  ```
* **Balance Workload**:
  ```bash
  ./automation/nexusagile-balance-load.sh
  ```

### 🔒 Human-in-the-Loop Mutation Safety

All commands run in **`dry-run`** mode by default. To persist modifications to Jira:
```bash
./automation/nexusagile-suggest-owner.sh --issue-keys "ENG-101" --apply --approved-by "Engineering Lead"
```
*Assignee changes and state mutations strictly require an authorized signer matching `approval.assigning_authorities` in your team map.*
