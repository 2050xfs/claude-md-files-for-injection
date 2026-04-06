---
name: model-chat
description: >
  Spawn 5+ Claude instances into a shared conversation room where they debate,
  disagree, and converge on solutions using round-robin turns with parallel
  execution within each round. Triggers on "model chat", "multi-model debate",
  "agent debate", "spawn a chat room", or /model-chat. Pass a topic as the argument.
allowed-tools: Read, Grep, Glob, Bash, Write, Edit
---

# Model Chat

## Overview
Spawn multiple AI agents (default: 5) into a shared debate room. Each agent has a distinct framing context — systems thinker, pragmatist, edge-case finder, user advocate, contrarian, first-principles thinker, risk analyst, or integrator — to systematically surface diverging perspectives on a given topic.

**Why this works:** Same model + slight framing variations = systematically different failure modes. Surfacing disagreements filters hallucinations; divergences reveal genuine judgment calls. Consensus across independent runs is more reliable than any single confident answer.

## Folder Structure

| Path | Purpose |
|------|---------|
| `tools/model_chat.py` | Core orchestration script — spawns agents, runs debate loop, streams synthesis |
| `outputs/model-chat/<YYYYMMDD-HHMMSS>/` | Timestamped output directory per run |
| `outputs/model-chat/<YYYYMMDD-HHMMSS>/conversation.json` | Full structured conversation log |
| `outputs/model-chat/<YYYYMMDD-HHMMSS>/synthesis.md` | Final synthesized answer |
| `outputs/model-chat/latest` | Symlink to the most recent run |

## Critical Rules
- **Always pass a topic argument** — the script will error without it.
- **Cost warning:** Each run makes `(agent_count × round_count) + 1` Anthropic API calls. Warn the user before running with high agent or round counts.
- **Interactive mode:** In `--interactive` mode the user is the moderator. Prompt them for optional input between rounds; wait for their response before continuing.
- **Environment:** `ANTHROPIC_API_KEY` must be set in `.env` at the project root. The script auto-loads it via `dotenv`.

## Step-by-Step Process
1. **Parse the request** — Extract `topic`, optional `--agents N`, `--rounds N`, and `--interactive` flag.
2. **Inform the user** — State agent count, round count, and approximate API call cost before running.
3. **Execute:**
   ```bash
   cd /Users/eddie.upflex/.gemini/antigravity/scratch/creative-cloner
   .venv/bin/python tools/model_chat.py "<topic>"
   # Optional flags:
   # --agents N   (default: 5)
   # --rounds N   (default: 5)
   # --interactive
   ```
4. **Monitor** — Read stdout for real-time per-agent responses and round progress.
5. **Synthesize** — After the final round, the script streams a structured synthesis to stdout.
6. **Deliver results:**
   - Brief summary of key agreements and disagreements
   - Full synthesis text (or link to `outputs/model-chat/latest/synthesis.md`)
   - Note any particularly sharp moments of disagreement worth the user reviewing

## Available Tools

| Tool | Description |
|------|-------------|
| `tools/model_chat.py` | Async Python orchestrator using `anthropic.AsyncAnthropic`. Handles prompt templating, parallel API calls via `asyncio.gather`, stream-based synthesis, ANSI color output, and JSON/Markdown file saving. |

## API / Integration Details
- **Provider:** Anthropic API
- **SDK:** `anthropic` Python (async client)
- **Model:** `claude-sonnet-4-6`
- **Concurrency:** All agents within a round fire simultaneously via `asyncio.gather`
- **Synthesis:** Single non-streamed call to `claude-sonnet-4-6` with full transcript, `max_tokens: 4096`
- **Agent framings available:** systems-thinker, pragmatist, edge-case-finder, user-advocate, contrarian, first-principles, risk-analyst, integrator

## Storage Schema

**`conversation.json`**
```json
{
  "topic": "string",
  "agent_count": "int",
  "round_count": "int",
  "model": "string",
  "timestamp": "ISO datetime",
  "agents": [{ "id": "string", "idx": "int" }],
  "conversation": [
    {
      "round": "int",
      "agent_id": "string",
      "content": "string",
      "timestamp": "ISO datetime"
    }
  ],
  "synthesis": "string"
}
```

**`synthesis.md`** — Markdown with consensus points, key disagreements, surprising insights, and a final recommendation.

## Quick Start
```bash
# 5 agents, 5 rounds — default
.venv/bin/python tools/model_chat.py "Should we use a monorepo or separate repos?"

# 3 agents, 3 rounds — faster/cheaper
.venv/bin/python tools/model_chat.py "Best way to handle auth in a Next.js app" --agents 3 --rounds 3

# Interactive mode — you moderate between rounds
.venv/bin/python tools/model_chat.py "How do we scale our API?" --interactive
```
