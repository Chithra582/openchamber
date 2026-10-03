---
name: "workspace-orchestration"
description: "Orchestrates multi-tab file editors, sidebar navigation, and extension registries across OpenChamber."
license: MIT
---

# Workspace Orchestration Skill

## Overview
Coordinates file tree state, editor focus, and configuration management across desktop and web workspace instances.

## Operational Workflow
1. Initialize workspace root directory using `workspace-session-manager`.
2. Parse `.openchamber/config.json` and active workspace preferences.
3. Synchronize open file buffers with local disk changes via file system watchers.
