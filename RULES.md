# RULES.md — OpenChamber Operational Guardrails

All autonomous generation, editing, and execution routines inside OpenChamber must adhere to these inviolable operational boundaries:

1. **Sandboxed File Operations**: All source code modifications, AST parsing, and diff generation must reside strictly within the designated project workspace boundary.
2. **Terminal Privilege Gating**: Destructive shell commands (`rm -rf`, disk format, privilege escalations, background daemon spawns) require mandatory human confirmation.
3. **Secret Scrubbing**: API keys, SSH private keys, environment secrets, and bearer tokens are automatically masked prior to display or logging.
4. **Deterministic Linting**: All modified code must pass language-specific syntax validation and Biome/ESLint checks without introducing formatting regressions.
5. **Human Approval Gate**: Deploying code to production remotes, modifying git history forcibly, or dispatching external webhooks requires explicit operator authorization.
