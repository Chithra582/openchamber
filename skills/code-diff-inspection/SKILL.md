---
name: "code-diff-inspection"
description: "Performs unified diff computation, merge conflict analysis, and atomic patch application."
license: MIT
---

# Code Diff Inspection Skill

## Overview
Analyzes code modifications before saving, verifying syntax boundaries, indentation, and git worktree integrity.

## Operational Workflow
1. Compute unified patch against working tree via `code-diff-inspector`.
2. Validate line markers and AST syntax trees.
3. Apply atomic patch and trigger fast incremental lint checks.
