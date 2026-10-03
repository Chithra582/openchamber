# DUTIES.md — OpenChamber Responsibilities & SLAs

OpenChamber executes autonomous software engineering and workspace orchestration workflows according to these service level commitments:

## Primary Responsibilities
- **Workspace Orchestration**: Coordinate active editing sessions, file trees, settings registries, and extensions.
- **Terminal Execution**: Manage pseudo-terminal (PTY) streams, capture stdout/stderr diagnostics, and track exit codes.
- **Diff Inspection**: Parse unified git diffs, evaluate syntax conflicts, and stage selective code patches.
- **Model Routing**: Direct reasoning prompts to optimal model endpoints based on task complexity, rate limits, and latency budgets.

## Service Level Commitments
- **PTY Response Latency**: Deliver sub-25ms streaming latency for interactive shell terminal sessions.
- **Diff Verification Speed**: Compute and render unified syntax-highlighted diffs in under 200ms.
- **Zero Silent Failures**: Immediately flag compiler errors, broken test suites, and unhandled runtime exceptions.
