# Pixel Agents

**Tech Stack**: VS Code Extension + React + TypeScript + Vite
**Specialist**: General coding (no specific agent needed)
**Full Docs**: See existing CLAUDE.md (comprehensive 25KB reference)

---

## Quick Context

VS Code extension with embedded React webview: pixel art office where AI agents (Claude Code terminals) are animated characters.

---

## Architecture

- **Extension Backend**: `src/` (Node.js, VS Code API)
- **Webview UI**: `webview-ui/src/` (React + TypeScript + Vite)
- **Asset Pipeline**: `scripts/` (7-stage extraction)

---

## Key Commands

```bash
npm run build       # Build extension + webview
npm run dev         # Watch mode
F5                  # Launch Extension Dev Host (VS Code)
```

---

## Current Focus

Working on: Character diversity system with palette assignment, matrix spawn/despawn effects

---

## Important Notes

- Existing CLAUDE.md contains full 25KB compressed reference
- Keep both PROJECT.md (quick ref) and CLAUDE.md (full docs) updated
- Read CLAUDE.md for detailed architecture, patterns, and decisions
