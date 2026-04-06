# Claude Brain Stack — Injection Files

Global configuration files for Eddie's Claude Code setup. Clone or sync these to `~/.claude/` to restore the full brain stack on any machine.

---

## Installation

```bash
# Clone to a temp location
git clone https://github.com/2050xfs/claude-md-files-for-injection.git /tmp/brain-stack

# Copy files to ~/.claude/
cp /tmp/brain-stack/CORE.md ~/.claude/
cp /tmp/brain-stack/USER.md ~/.claude/
cp /tmp/brain-stack/SOUL.md ~/.claude/
cp /tmp/brain-stack/MEMORY.md ~/.claude/
cp /tmp/brain-stack/settings.json ~/.claude/
cp /tmp/brain-stack/settings.local.json ~/.claude/
cp -r /tmp/brain-stack/agents ~/.claude/
cp -r /tmp/brain-stack/stacks ~/.claude/
```

> **Note**: After copying `settings.json`, replace `YOUR_FIRECRAWL_API_KEY` with your actual Firecrawl API key.

---

## File Structure

```
~/.claude/
├── CORE.md              # Primary agent identity, routing, decision rules
├── USER.md              # Eddie's profile, projects, preferences
├── SOUL.md              # Shared tone and communication rules
├── MEMORY.md            # Session continuity — update before ending sessions
├── settings.json        # MCP servers + permission mode
├── settings.local.json  # Local permission overrides
│
├── agents/
│   ├── instagram.md     # Instagram growth + lead gen specialist
│   ├── youtube.md       # YouTube authority + growth specialist
│   └── design-os.md     # Design OS product planning specialist
│
└── stacks/
    ├── react-vite-supabase.md      # React + Vite + Supabase conventions
    ├── nextjs-tailwind.md          # Next.js + Tailwind CSS conventions
    └── marketing-automation.md    # GHL + Make.com + automation patterns
```

---

## How It Works

**3-layer hierarchy:**

1. **Global** (`~/.claude/`) — Always active. Defines identity, style, decision rules.
2. **Workspace** (`WORKSPACE.md` in workspace root) — Coordination across related projects.
3. **Project** (`PROJECT.md` in project root) — Local context and overrides.

**Startup sequence (every session):**
1. Read `USER.md` — who Eddie is, what he's building
2. Read `MEMORY.md` — in-progress work from last session
3. Detect specialist need from directory path or task type
4. Load matching agent from `agents/`
5. Load tech stack from `stacks/` if PROJECT.md references one
6. Read local `PROJECT.md` if it exists

**Auto-loading triggers:**
- Path contains "Instagram" → load `agents/instagram.md`
- Path contains "Youtube" → load `agents/youtube.md`
- Path contains "design-os" → load `agents/design-os.md`
- PROJECT.md references a stack → load matching stack template

---

## MCP Servers

`settings.json` configures:
- **firecrawl-mcp** — web scraping via Firecrawl API

Set `FIRECRAWL_API_KEY` in `settings.json` with your actual key (not committed here).

---

## Updating This Repo

When you change any brain files locally, push the updates:

```bash
cd /tmp/brain-stack
git pull
cp ~/.claude/CORE.md .
cp ~/.claude/USER.md .
cp ~/.claude/SOUL.md .
cp ~/.claude/MEMORY.md .
cp ~/.claude/agents/*.md agents/
cp ~/.claude/stacks/*.md stacks/
git add -A
git commit -m "sync brain stack"
git push
```
