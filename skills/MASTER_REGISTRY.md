# Master Skills Registry

This registry tracks all production-grade skills. Each skill directory contains a `SKILL.md` (logic/instructions).

## Active Skills Inventory

| Skill Name | Description | Status | Primary Use Case |
| :--- | :--- | :--- | :--- |
| **Aesthetic Frontend Redesign** | Transforms generic sites into high-end bespoke experiences | Active | Full visual overhauls |
| **Redesign Existing Project** | Surgical UI/UX audits & incremental upgrades | Active | Iterative component polish |
| **Output Enforcement** | Guarantees complete code generation; bans placeholders | Active | Production implementation |
| **Taste Skill** | Overrides generic LLM biases; senior UI engineering | Active | High-end SaaS/App design |
| **Content Engine** | Automated Image/Video content pipeline via Airtable | Active | Marketing & Social Content |
| **Restaurant Engine** | Lead gen & AI food photo enhancement for F&B | Active | Restaurant industry refresh |
| **Creative Cloner** | Video scene analysis & character/subject cloning | Active | Video production & IP injection |
| **Model Chat** | Multi-agent debate — spawns N Claude instances to debate a topic in rounds | Active | Complex decisions, architecture reviews |
| **Video-to-Action** | Extract actionable steps from YouTube tutorials via Gemini passthrough | Active | Learning & executing from video content |
| **Remotion Video Creator** | Programmatic React/TypeScript videos — 5 prompt templates, full API reference, always deploys live Vercel URL | Active | Code-driven video: explainers, product demos, testimonial reels, data dashboards |
| **Digital Business Card** | Two-layer digital identity: 3D interactive pass + cinematic GSAP brand landing — ships to Vercel from a single `cards.json` | Active | Premium client-facing digital business cards |
| **Talking Head Video** | Full talking head video pipeline — hooks, avatar, environment, image gen, video gen, Airtable logging | Active | AI talking head video production |
| **Viral Hooks** | 1000+ proven viral hook templates + 7-factor content framework | Active | Social media content creation |
| **Ollama + Open WebUI** | Deploy fully local ChatGPT-equivalent with Ollama + Docker | Active | Local LLM setup |
| **AI Avatars** | Character definitions for talking head video generation | Active | Avatar management |
| **AI Environments** | Background/scene definitions for video generation | Active | Environment management |

## Directory Structure
```text
skills/
├── MASTER_REGISTRY.md           # This file
├── SKILL_TEMPLATE.md            # Standard doc template
├── aesthetic-frontend-redesign/
├── ai-avatars/                  # Subdirectory per avatar character
├── ai-environments/             # Subdirectory per environment
├── content-engine/
├── creative-cloner/
├── digital-business-card/
├── model-chat/
├── ollama-open-webui/
├── output-skill/
├── redesign-skill/
├── remotion/
├── restaurant-engine/
├── talking-head-video/
├── taste-skill/
├── video-to-action/
└── viral-hooks-skill/
```

## Maintenance Workflow
1. **New Skill**: Create folder, add `SKILL.md`.
2. **Register**: Add entry to this `MASTER_REGISTRY.md`.
3. **For AI avatars/environments**: Add subdirectory under `ai-avatars/` or `ai-environments/`.
