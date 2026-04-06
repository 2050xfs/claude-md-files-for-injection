# Key Decisions

**Last Updated**: 2026-04-05

Architectural and strategic choices that affect multiple projects.

---

## Architecture Decisions

### Brain Stack System (2026-04-05)
**Decision**: Implement hierarchical agent system with three layers
- **Global Brain** (`~/.claude/`): CORE.md, USER.md, SOUL.md, agents/, stacks/
- **Workspace Brain** (`scratch/WORKSPACE.md`): Coordination layer
- **Project Brain** (PROJECT.md in each project): Local context

**Rationale**:
- Single source of truth (no duplicate CLAUDE.md files)
- Composable specialist agents
- Works seamlessly regardless of starting location
- Easy to update (edit once, affects all projects)

**Impact**: All new projects follow this pattern

---

### Specialist Agent Pattern (2026-04-05)
**Decision**: Create domain-specific agents that extend CORE.md
- Instagram specialist (growth + lead gen)
- YouTube specialist (authority building)
- Design OS specialist (product planning tool)

**Rationale**:
- Deep expertise in each domain
- Context-aware auto-loading
- Avoids generic advice

**Impact**: Agents live in `~/.claude/agents/` and are referenced by projects

---

### Tech Stack Templates (2026-04-05)
**Decision**: Create reusable stack documentation
- react-vite-supabase (web apps)
- nextjs-tailwind (production sites)
- marketing-automation (workflows)

**Rationale**:
- Consistency across projects
- Faster onboarding
- Best practices captured once

**Impact**: Templates live in `~/.claude/stacks/` and are referenced by PROJECT.md

---

**Log important decisions here. Future you will thank you.**
