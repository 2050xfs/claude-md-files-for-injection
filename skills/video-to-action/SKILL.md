---
name: video-to-action
description: >
  Use Gemini as a video analysis passthrough to extract actionable steps from YouTube videos, tutorials, and demos. Claude queries Gemini about video content and converts it into executable instructions. Triggers on "watch this video", "learn from this video", "extract steps from video", "video to action", "analyze this YouTube", or /video-to-action. Also triggers on phrases like "what does this tutorial show", "turn this video into steps", or "learn from this tutorial".
allowed-tools: Read, Grep, Glob, Bash, Task, Write, Edit
---

# Video-to-Action via Gemini Passthrough

## Overview
Claude can't watch video natively — Gemini can. This skill uses Gemini as a passthrough: send it a YouTube URL, ask structured questions, and receive actionable steps that Claude can execute directly.

**Why this works:** A 30-minute tutorial becomes executable instructions. Course content, documentation videos, product demos — all become agent-parseable. You don't need Claude to "see" the video; you need a structured extraction of what the video teaches.

---

## Folder Structure

| Path | Purpose |
|------|---------|
| `tools/video_to_action.py` | Core script — queries Gemini in 2-3 phases and writes procedure doc |
| `outputs/video-actions/<title>.md` | Per-video extracted procedure document |

---

## Critical Rules
- **Cost warning:** Each run makes 2–3 Gemini API calls. Warn the user before running Phase 3 (technical extraction) which adds a third call.
- **Never expose API keys:** If a video shows real API keys, log them as `<REDACTED>` and warn the user.
- **Unavailable videos:** If Gemini cannot access the video (private, deleted), stop and tell the user. Do not silently produce an empty document.
- **Low-confidence steps:** If Gemini flags that content was fast-scrolling or partially visible, mark those steps `⚠️ LOW CONFIDENCE` in the output.
- **Do not overwrite different videos:** Each video gets its own file (`outputs/video-actions/<title>.md`). The same video's file may be overwritten on re-run.
- **GEMINI_API_KEY required:** Must be set in `.agent/.env` or project root `.env`.

---

## Step-by-Step Process

### 1. Parse the request

Extract from the user's message:
- **Video URL** — YouTube link
- **Intent** — what the user wants:
  - `"Follow this tutorial"` → extract steps and execute them
  - `"What does this video show?"` → extract and summarize only
  - `"Learn this technique"` → extract as a reusable procedure
- **Scope** (optional) — e.g. `"the part about routing"` or `"starting at 5:00"`
- **Technical flag** — set if it's a coding/CLI tutorial needing code extraction

If no URL is provided, ask for one before running.

### 2. Run the extraction script

```bash
cd /Users/eddie.upflex/.gemini/antigravity/scratch/creative-cloner

# Standard tutorial (2 phases)
.venv/bin/python tools/video_to_action.py "https://youtube.com/watch?v=..."

# Coding tutorial (3 phases — adds code/command extraction)
.venv/bin/python tools/video_to_action.py "https://youtube.com/watch?v=..." --technical

# With scope constraint
.venv/bin/python tools/video_to_action.py "https://youtube.com/..." --scope "the authentication section"

# Custom intent label
.venv/bin/python tools/video_to_action.py "https://youtube.com/..." --intent "Learn this design technique"
```

**Available flags:**

| Flag | Default | Description |
|------|---------|-------------|
| `url` | required | YouTube video URL |
| `--intent` | `"Follow this tutorial"` | How to label the extraction intent |
| `--scope` | None | Focus on a specific section |
| `--technical` | False | Run Phase 3 for code/command extraction |
| `--output` | auto-named | Custom output file path |

### 3. Review the procedure document

The script saves to `outputs/video-actions/<video-title>.md`. Read that file and present it to the user with:
- A brief summary of key prerequisites and outcomes
- The full step list (or a condensed version for long tutorials)
- Any `⚠️ LOW CONFIDENCE` steps flagged for manual review

### 4. Execute or deliver

Based on the user's intent:

#### "Follow this tutorial" → Execute
- Work through the procedure step by step
- Use extracted code/commands directly
- If a step requires visual confirmation, pause: *"Step N requires visual verification — check that [expected result] before I continue."*
- Adapt paths, filenames, and project structure to the user's actual environment

#### "What does this video show?" → Summarize
- Present the overview and step list
- Do not execute anything
- Offer to execute if the user wants

#### "Learn this technique" → Save permanently
- Move the file from `outputs/video-actions/` to the project's permanent `docs/` or procedures directory
- Strip video-specific details; generalize steps where appropriate

---

## Available Tools

| Tool | Description |
|------|-------------|
| `tools/video_to_action.py` | Python script using `google-genai` SDK. Runs 2–3 Gemini phases (overview, steps, optional technical extraction), builds a Markdown procedure document, and saves it to `outputs/video-actions/`. |

---

## API / Integration Details

- **Provider:** Google Gemini API
- **SDK:** `google-genai` Python package
- **Model:** `gemini-2.5-pro`
- **Input:** YouTube URL passed as text in the prompt (Gemini can natively parse YouTube URLs)
- **Output:** Plain text — parsed and formatted by the script into Markdown
- **API key env var:** `GEMINI_API_KEY`

### Extraction capacity

| Content type | Reliability |
|-------------|------------|
| Spoken instructions | ✅ High |
| On-screen text and UI | ✅ High |
| Code shown on screen | ✅ High (may need cleanup) |
| Terminal commands | ✅ High |
| Fast-scrolling content | ⚠️ Partial |
| Subtle visual technique | ⚠️ Low — flag and recommend watching |
| Audio-only cues | ⚠️ Low |

---

## Storage Schema

**`outputs/video-actions/<title>.md`**
```markdown
# Video-to-Action — Extracted Procedure

**Source**: <URL>
**Extracted**: <date>
**Intent**: <intent>

## Overview
<Phase 1 output — title, length, summary, prerequisites, sections, outcomes>

## Steps
<Phase 2 output — numbered steps with timestamp, action, details, expected result>

## Code & Commands
<Phase 3 output — only if --technical flag was set>

## Notes
- Low-confidence caveat
- Outdated API warnings
```

---

## Quick Start

```bash
# Extract steps from a tutorial and save the procedure doc
.venv/bin/python tools/video_to_action.py "https://www.youtube.com/watch?v=dQw4w9WgXcQ"

# For a coding tutorial — extract code + commands too
.venv/bin/python tools/video_to_action.py "https://www.youtube.com/watch?v=..." --technical

# Read the result
cat outputs/video-actions/<title>.md
```

**Prerequisite:** `GEMINI_API_KEY` must be set in `.agent/.env`.

---

## Edge Cases

| Situation | Handling |
|-----------|---------|
| Video is private/unavailable | Script detects and exits cleanly; tell user to provide a transcript instead |
| Video is not a tutorial | Still extract key claims, demonstrated results, referenced tools; note it's not step-by-step |
| Video is very long (1hr+) | Ask user which section matters; use `--scope` flag |
| Extracted code doesn't work | Common — video code is often partial; flag inferred sections |
| Video content is outdated | Note version discrepancies when tools/APIs have newer versions |
