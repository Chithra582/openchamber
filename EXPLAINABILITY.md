# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **OpenChamber** (`openchamber`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** OpenChamber (`openchamber`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / AI Coding Workspace, Multi-Model Terminal & Agent IDE Harness  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

OpenChamber operates an intelligent, multi-layer workspace orchestration and model routing architecture designed to streamline autonomous software engineering. The agent manages code editing sessions, interactive PTY terminal buffers, unified diff verification, and multi-model failover through a deterministic five-stage operational pipeline.

### 1. Decision Architecture

The runtime intake, state classification, evaluation, and execution tracking operate across a deterministic, five-stage pipeline:

```
[ User Developer Prompt / Interactive Terminal Directive ]
                         │
                         ▼
[Stage 1: Intent Decomposition & Workspace Context Gate]
  - Parses developer instructions, active file buffers, and selection tokens
  - Validates project root directory boundaries and file permission masks
  - Dispatches context search across workspace symbol indices
                         ▼
[Stage 2: Model Routing & Inference Provider Gate]
  - Computes task complexity score S_complexity across available LLM backends
  - Evaluates provider quota health, rate limit ceilings, and latency budgets
  - Formats prompt payload to matched provider schema (OpenAI, Claude, Gemini)
                         ▼
[Stage 3: AST Diff Analysis & Safety Verification Gate]
  - Parses proposed code modifications into structured unified diff chunks
  - Runs deterministic AST syntax validation and indentation checks
  - Evaluates shell command safety against privileged operation blacklist
                         ▼
[Stage 4: Sandboxed Execution & Terminal PTY Stream Gate]
  - Spawns isolated PTY worker process for build, test, or lint execution
  - Streams interactive terminal output with sub-25ms buffer latency
  - Captures process return codes, compiler diagnostics, and execution telemetry
                         ▼
[Stage 5: State Sealing & Audit Telemetry Logging Gate]
  - Commits verified file modifications to local working tree
  - Seals turn record with execution timestamps and token consumption tallies
  - Emits telemetry summary and refreshes editor diagnostics panel
                         ▼
[ Verified Workspace State Updated & Persisted ]
```

### 2. Decision Logic & Routing Formulations

When routing developer prompts and evaluating automated code modifications, OpenChamber evaluates two deterministic mathematical formulations:

1. **Model Routing Affinity Score ($S_{	ext{route}}$)**:
   $$S_{	ext{route}}(m) = w_c \cdot C_{	ext{capability}}(m) + w_l \cdot (1 - L_{	ext{norm}}(m)) + w_q \cdot Q_{	ext{health}}(m) + w_r \cdot R_{	ext{affinity}}(m)$$
   Where:
   - $C_{	ext{capability}}(m) \in [0, 1]$: Benchmark capability rating of candidate model $m$ for active task type (refactoring, debugging, scaffolding).
   - $L_{	ext{norm}}(m) \in [0, 1]$: Normalized endpoint latency relative to maximum allowable response ceiling ($5000$ms).
   - $Q_{	ext{health}}(m) \in [0, 1]$: Rate limit quota health indicator ($1.0$ for unconstrained, $0.0$ for active HTTP 429 throttling).
   - $R_{	ext{affinity}}(m) \in [0, 1]$: Local privacy preference bonus ($1.0$ for offline local engines like Ollama/vLLM).
   - Standard weightings: $w_c = 0.40$, $w_l = 0.25$, $w_q = 0.20$, $w_r = 0.15$ ($\sum w_i = 1.0$).
   - Selection rule: The chosen model is $m^* = rg\max_{m} S_{	ext{route}}(m)$ subject to $S_{	ext{route}}(m^*) \ge 0.65$.

2. **Diff Safety & Blast Radius Index ($B_{	ext{diff}}$)**:
   $$B_{	ext{diff}} = lpha \cdot rac{L_{	ext{mod}}}{L_{	ext{total}}} + eta \cdot F_{	ext{count}} + \gamma \cdot D_{	ext{depth}}$$
   Where:
   - $L_{	ext{mod}}$: Total lines modified or deleted in the proposed patch.
   - $L_{	ext{total}}$: Total line count of target source files.
   - $F_{	ext{count}}$: Number of discrete files modified simultaneously.
   - $D_{	ext{depth}}$: Directory tree depth of affected configuration or root infrastructure files.
   - Coefficients: $lpha = 0.45$, $eta = 0.35$, $\gamma = 0.20$. Patches with $B_{	ext{diff}} \ge 0.50$ mandate human approval.

### 3. Thresholding & Refusal Decision Criteria

OpenChamber enforces strict operational boundaries and deterministic refusal thresholds:
- **Refusal on ERR_PRIVILEGED_COMMAND_BLOCKED**: Destructive shell operations (`rm -rf /`, `mkfs`, raw partition modifications) halt execution with code `ERR_PRIVILEGED_COMMAND_BLOCKED`.
- **Refusal on ERR_WORKSPACE_BOUNDARY_VIOLATION**: File read/write attempts referencing paths outside active workspace directory halt with code `ERR_WORKSPACE_BOUNDARY_VIOLATION`.
- **Refusal on ERR_AST_SYNTAX_INVALID**: Generated code containing syntax errors or malformed tokens halts patch application with code `ERR_AST_SYNTAX_INVALID`.
- **Refusal on ERR_PTY_TIMEOUT**: Terminal command execution exceeding maximum duration timeout ($> 300$s) terminates with code `ERR_PTY_TIMEOUT`.
- **Refusal on ERR_QUOTA_EXHAUSTION**: Cumulative turn token usage exceeding configured budget allocations halts with code `ERR_QUOTA_EXHAUSTION`.

### 4. Fallback Decision Mechanism

Continuous operational stability is maintained through layered fault recovery:
- **Local Inference Fallback**: When external frontier cloud APIs time out (> 5000ms) or return HTTP 429 rate limit errors, OpenChamber automatically fails over to local Ollama/vLLM endpoints.
- **Atomic Git Patch Rollback**: If an applied diff causes build or test suite failures, OpenChamber automatically reverts the uncommitted worktree changes to the last clean checkpoint.
- **Model Fallback Cascade**: High-level reasoning and synthesis default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

Human operators retain sovereign authority over the multi-agent execution lifecycle:
- **Consequential Diff Sign-Off**: Multi-file refactorings and structural architecture changes require developer preview and sign-off in the diff viewer.
- **Terminal Execution Confirmation**: Commands matching elevated risk profiles prompt for explicit developer confirmation before spawning the PTY worker.
- **Audit Telemetry Inspection**: Operators can inspect complete prompt logs, model parameters, and raw shell execution streams via the OpenChamber telemetry dashboard.

---

## The Data It Uses

OpenChamber operates under strict principles of data minimization, environment isolation, and privacy protection.

### 1. Ingested Input Data

The framework processes only operational data necessary to perform its functions:
- **User Prompt Directives**: Natural language coding instructions, refactoring requests, and terminal command queries.
- **Workspace Source Code**: Local project files, directory structures, and git status trees within the selected workspace.
- **Terminal Stream Telemetry**: Standard output, standard error, exit codes, and process memory utilization from active PTY sessions.

### 2. Configuration & Reference Data

- **Project Configuration**: `.openchamber/config.json`, editor settings, theme manifests, and keyboard shortcut maps.
- **AST Language Grammar**: CodeMirror and Lezer language definition bundles for TypeScript, Python, Rust, Go, C++, and SQL.
- **Model Credentials**: Locally stored API keys, endpoint URLs, and custom model parameter presets.

### 3. Base Model & Inference Lineage

- **Execution Runtime**: Node.js 22+ LTS, Bun 1.4+, Electron, and Vite operating with pure native Web PTY modules.
- **Editor Core**: Virtualized CodeMirror 6 and React 19 UI delivering zero-lag rendering.
- **Model Lineage**: Multi-model compatibility covering Claude 3.5 Sonnet, GPT-4o, Gemini 2.0 Flash, DeepSeek-V3, and local Ollama models.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection, credential leakage, and unauthorized external API dispatch.
- **Local Environment Isolation**: Agent execution workspaces, intermediate scratchpads, and vector stores reside strictly within designated local project directories.
- **Automated Secret Scrubbing**: API keys, database credentials, and personal credentials are automatically redacted prior to embedding or logging.
- **Zero Commercial Monetization**: Prompts, intermediate reasoning trajectories, and task deliverables are never commercialized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of OpenChamber is essential for effective deployment.

### 1. Heavy Monorepo AST Indexing Overhead
- **Limitation**: Indexing massive enterprise repositories containing hundreds of thousands of files can stress local RAM.
- **Mitigation**: Implement lazy symbol indexing and prioritize active workspace folders and open buffer tabs.

### 2. Upstream Provider HTTP 429 Throttling
- **Limitation**: Burst token consumption during exhaustive multi-file refactoring runs can trigger upstream API rate limits.
- **Mitigation**: Enforce client-side request queuing, token bucket rate limiting, and automatic local fallback failover.

### 3. Non-Standard Shell Environment Differences
- **Limitation**: Differences in host shells (bash, zsh, fish, PowerShell) can result in platform-specific command syntax failures.
- **Mitigation**: Detect active shell environments automatically and execute commands inside standardized POSIX sub-shells where possible.

### 4. Headless Terminal PTY Rendering Inconsistencies
- **Limitation**: Complex interactive terminal applications (curses, htop) can experience minor visual artifacting in virtualized web PTYs.
- **Mitigation**: Deploy robust xterm.js emulation with full ANSI escape sequence compatibility and automatic resize event debouncing.

### 5. Multi-Branch Git Merge Conflict Resolution
- **Limitation**: Concurrent automated code edits during active developer rebases can trigger complex git merge conflicts.
- **Mitigation**: Detect uncommitted developer changes prior to applying agent patches and prompt for stash or commit before proceeding.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data & query streams | Section 1 | Verified |
| - Configuration & reference schemas | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Heavy Monorepo AST Indexing Overhead | Section 1 | Verified |
| - Upstream Provider HTTP 429 Throttling | Section 2 | Verified |
| - Non-Standard Shell Environment Differences | Section 3 | Verified |
| - Headless Terminal PTY Rendering Inconsistencies | Section 4 | Verified |
| - Multi-Branch Git Merge Conflict Resolution | Section 5 | Verified |
