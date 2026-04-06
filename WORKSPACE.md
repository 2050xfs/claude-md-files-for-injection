# Scratch Workspace — Primary Coordination

**Extends**: `~/.claude/CORE.md` (already loaded)

This is Eddie's primary development workspace. The coordinator for all projects in the scratch/ directory.

---

## Workspace Structure

```
scratch/
  WORKSPACE.md           — this file (workspace coordination)
  memory/                — shared memory across all projects
    ├── active-projects.md
    ├── blockers.md
    └── decisions.md

  Projects:
  ├── pixel-agents/      — VS Code extension with pixel art agents
  ├── resume-explorer/   — AI resume & career toolkit
  ├── cinematic-site-components/ — 30 cinematic HTML modules
  ├── Instagram/         — Instagram growth specialist (links to ~/.claude/agents/)
  ├── Youtube/           — YouTube growth specialist (links to ~/.claude/agents/)
  ├── upflex-digital-ghl-clone/ — GHL CRM clone
  ├── creative-cloner/
  ├── digital-business-cards/
  ├── Rosemont-Track-and-field/
  └── audio-processor/
```

---

## Your Role as Workspace Coordinator

**Handle from this root:**
- Quick tasks spanning multiple projects
- Coordination between projects
- Routing to project-specific sessions
- Memory management across workspace

**Delegate to project folders:**
- Deep work inside a single project
- When working on pixel-agents/, cd into it and work there
- Each project may have its own PROJECT.md — respect it

**Never start work inside a project without:**
1. Reading its PROJECT.md (if exists)
2. Understanding its current state
3. Loading appropriate specialist agent or tech stack

---

## Memory Management

**Workspace Memory (`memory/`):**
- `active-projects.md` — Current priorities across all projects
- `blockers.md` — Issues that need resolution
- `decisions.md` — Important choices that affect multiple projects

**Check memory at startup:**
Don't ask Eddie to re-explain prior context. Read memory/ first.

**Update memory before ending long sessions:**
Context switches are expensive. Write down what matters.

---

## Project Types

### Technical Projects
- pixel-agents (VS Code extension)
- resume-explorer (React + Vite + Supabase)
- upflex-digital-ghl-clone (GHL CRM system)
- cinematic-site-components (HTML/CSS/JS modules)

### Growth/Content Projects
- Instagram (specialist agent in ~/.claude/agents/)
- Youtube (specialist agent in ~/.claude/agents/)

### Client/Service Projects
- creative-cloner
- digital-business-cards
- Rosemont-Track-and-field
- audio-processor

---

## Multi-Agent Coordination

If pixel-agents spawns sub-agents (alpha, beta, gamma terminals):
- Each sub-agent inherits SOUL.md rules
- Sub-agents work within assigned project scope
- Coordination decisions come back to the primary agent

**Never duplicate work another agent is handling.**
Check shared memory before starting tasks.

---

## Decision Rules (Workspace-Specific)

**Proceed without asking:**
- Moving between project directories
- Reading project files
- Running project-specific commands
- Creating/updating PROJECT.md files

**Ask before proceeding:**
- Deleting entire project directories
- Modifying workspace-level config
- Pushing to remote repositories
- Changes affecting multiple projects simultaneously

---

## Specialist Routing

When working in this workspace:

**Instagram or Youtube directories:**
→ Auto-load respective specialist agent from `~/.claude/agents/`

**design-os projects:**
→ Load `~/.claude/agents/design-os.md`

**React + Vite + Supabase projects:**
→ Load `~/.claude/stacks/react-vite-supabase.md`

**Next.js projects:**
→ Load `~/.claude/stacks/nextjs-tailwind.md`

---

## Common Operations

### Starting Work on a Project
```
1. cd into project directory
2. Read PROJECT.md (if exists)
3. Load specialist/stack (if needed)
4. Check project-specific memory
5. Resume work
```

### Ending Session
```
1. Update all active project memory files
2. Update ~/.claude/MEMORY.md with key decisions
3. Note any blockers in memory/blockers.md
```

---

**This workspace is your command center. Keep it organized.**
