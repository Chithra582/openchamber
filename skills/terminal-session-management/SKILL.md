---
name: "terminal-session-management"
description: "Controls pseudo-terminal (PTY) processes, handles interactive shell commands, and captures build logs."
license: MIT
---

# Terminal Session Management Skill

## Overview
Spawns isolated PTY worker sessions to run package managers, test runners, and build commands with real-time log streaming.

## Operational Workflow
1. Spawn sandboxed shell instance using `terminal-pty-executor`.
2. Stream raw terminal escape sequences and ANSI color buffers to UI.
3. Monitor exit codes and trigger automatic error remediation suggestions upon non-zero exits.
