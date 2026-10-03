---
name: "multi-model-routing"
description: "Directs coding prompts across frontier cloud APIs and local inference engines based on cost, latency, and capability."
license: MIT
---

# Multi-Model Routing Skill

## Overview
Evaluates user task complexity and routes turns across Anthropic Claude, OpenAI, Google Gemini, and local Ollama instances.

## Operational Workflow
1. Evaluate token budget and task complexity tier via `model-provider-router`.
2. Select target model endpoint and format prompt according to provider schema.
3. Stream tokens to editor UI with automatic failover upon HTTP 429 rate limit triggers.
