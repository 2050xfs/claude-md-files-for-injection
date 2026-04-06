# Master Skills Registry
> **Directory**: `/Users/eddie.upflex/.gemini/antigravity/scratch/.agent/skills/`
> **Last updated**: 2026-04-06

Complete inventory of all skills, tools, MCP servers, and plugins available to the Antigravity agent.

---

## SKILL.md Skills — Video Production

| Skill | Description | Status | Keys Required |
| :--- | :--- | :--- | :--- |
| **talking-head-video** | Full talking head pipeline — avatar + environment selection, image gen, video gen, Airtable logging. One command, full production. | ✅ Active | FAL_KEY, AIRTABLE |
| **viral-hooks-skill** | 1000+ proven viral hook templates + 7-factor short-form content framework | ✅ Active | None |
| **remotion** | Programmatic React/TS videos via Remotion — 5 prompt templates, ships live Vercel URL | ✅ Active | None |
| **content-engine** | Automated image/video content pipeline via Airtable + Kie.ai | ✅ Active | KIE_API_KEY, AIRTABLE |
| **creative-cloner** | Video scene analysis & character/subject cloning via Gemini + Kie.ai | ✅ Active | GEMINI_API_KEY, KIE_API_KEY, AIRTABLE |

## SKILL.md Skills — AI Avatars (10 avatars)

| Avatar | Description | Reference Image |
| :--- | :--- | :--- |
| **ventura** | Young woman, light brown hair + freckles, Starbucks iced coffee, GRWM TikTok vibes | ✅ |
| **elena** | (see SKILL.md) | ✅ |
| **camarillo** | (see SKILL.md) | ✅ |
| **jake** | (see SKILL.md) | ✅ |
| **reggie** | (see SKILL.md) | ✅ |
| **zara** | (see SKILL.md) | ✅ |
| **rabii-goldman** | (see SKILL.md) | ✅ |
| **marcus** | (see SKILL.md) | ✅ |
| **oxnard** | (see SKILL.md) | ✅ |
| **sofia** | (see SKILL.md) | ✅ |

Custom avatar builder: `ai-avatars/custom/` — intake, prompt, fal-ai/nano-banana-pro, save + Airtable log.

## SKILL.md Skills — AI Environments (7 environments)

| Environment | Vibe | Status |
| :--- | :--- | :--- |
| **urban-coffee-shop** | Casual, spontaneous, warm Edison + daylight | ✅ |
| **brabus-gwagon-orange** | Luxury, automotive, bold | ✅ |
| **rooftop-city** | Urban, cinematic, open sky | ✅ |
| **cozy-living-room** | Warm, intimate, lifestyle | ✅ |
| **minimalist-studio** | Clean, modern, professional | ✅ |
| **modern-home-office** | Focused, aspirational, WFH | ✅ |
| **custom** | Full custom environment builder — intake → prompt → save → Airtable log | ✅ |

## SKILL.md Skills — Design & Frontend

| Skill | Description | Status |
| :--- | :--- | :--- |
| **aesthetic-frontend-redesign** | Transforms generic sites into high-end bespoke experiences | ✅ Active |
| **redesign-skill** | Surgical UI/UX audits & incremental upgrades | ✅ Active |
| **output-skill** | Guarantees complete code generation; bans placeholders | ✅ Active |
| **taste-skill** | Overrides generic LLM design biases; senior UI engineering standard | ✅ Active |
| **digital-business-card** | 3D interactive pass + cinematic GSAP brand landing — ships to Vercel from `cards.json` | ✅ Active |

## SKILL.md Skills — AI / Agents / Research

| Skill | Description | Status | Keys Required |
| :--- | :--- | :--- | :--- |
| **model-chat** | Multi-agent debate — spawns N Claude instances to debate a topic in rounds | ✅ Active | None |
| **video-to-action** | Extract actionable steps from YouTube tutorials via Gemini | ✅ Active | GEMINI_API_KEY |
| **restaurant-engine** | Lead gen + AI food photo enhancement for F&B industry | ✅ Active | GEMINI_API_KEY |
| **ollama-open-webui** | Deploy local LLM stack (Ollama + Open WebUI via Docker) | ✅ Active | None |

---

## Claude Code Plugins (installed at ~/.claude/plugins/)

| Plugin | What It Does | Trigger |
| :--- | :--- | :--- |
| **frontend-design** | Production-grade frontend interfaces with high design quality | User asks to build web UI |
| **hookify** | Create Claude Code hook rules and configure hooks | "create a hookify rule" |
| **plugin-dev** | Agent + skill development guidance for Claude Code plugins | Building plugins/agents |
| **code-review** | Code review workflows | Code review tasks |
| **commit-commands** | Git commit helpers | Commit tasks |
| **feature-dev** | Feature development workflows | Feature implementation |
| **pr-review-toolkit** | PR review automation | PR review tasks |
| **security-guidance** | Security-focused code guidance | Security tasks |
| **agent-sdk-dev** | Agent SDK development helpers | SDK/agent dev |
| **LSP plugins** | Language servers: TypeScript, Python, Go, Rust, C#, Swift, PHP, Lua, Java, C/C++ | Auto-detected by language |

---

## MCP Servers (live tools in this session)

| Server | Key Tools | Use Case |
| :--- | :--- | :--- |
| **Airtable** | list_bases, list_tables, create/update/list records | Content tracking, avatar/environment logs, project pipeline |
| **Supabase** | execute_sql, apply_migration, deploy_edge_function, get_logs | Backend for React apps, auth, database |
| **Vercel** | list_projects, list_deployments, get_runtime_logs, deploy_to_vercel, toolbar threads | Deploy + monitor frontend projects |
| **Google Calendar** | list_events, create_event, find_free_time, respond_to_event | Scheduling, calendar management |
| **Gmail** | search_messages, read_message, read_thread, create_draft | Email reading and drafting |
| **Stripe** | customers, products, prices, invoices, subscriptions, payment_links | Billing and payments |
| **Google Drive** | search, fetch | Document and file retrieval |
| **Computer Use** | screenshot, click, type, scroll, key, open_application | Full desktop automation |
| **Claude Preview** | start/stop preview, screenshot, click, eval, network | In-session browser preview |
| **Claude in Chrome** | navigate, find, javascript, form_input, gif_creator | Chrome automation + scraping |
| **Control Chrome** | open_url, get_page_content, execute_javascript, tabs | Lightweight Chrome control |
| **Scheduled Tasks** | create_scheduled_task, list, update | Cron-style task scheduling |
| **MCP Registry** | search_mcp_registry, suggest_connectors | Discover + install new MCP servers |
| **Firecrawl** | (via settings.json) web scraping | Web research and scraping |

---

## External APIs (in .agent/.env)

| Variable | Service | Used By |
| :--- | :--- | :--- |
| `GEMINI_API_KEY` | Google Gemini | video-to-action, creative-cloner, restaurant-engine |
| `KIE_API_KEY` | Kie.ai | content-engine, creative-cloner (video generation) |
| `FAL_KEY` | fal.ai | talking-head-video (nano-banana-pro images, Kling/Veo/Sora video) |
| `AIRTABLE_API_KEY` / `AIRTABLE_TOKEN` | Airtable REST API | talking-head-video, content-engine, creative-cloner, custom avatar/env |
| `AIRTABLE_BASE_ID` | Airtable base ID | All Airtable-connected skills |

---

## Maintenance Workflow
1. **New Skill**: Create folder, add `SKILL.md` with frontmatter.
2. **Register**: Add entry to this file under the right section.
3. **Key check**: Note any API keys required in the Keys Required column.
4. **Push**: Sync to https://github.com/2050xfs/claude-md-files-for-injection
