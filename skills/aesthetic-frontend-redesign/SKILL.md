---
name: Aesthetic Frontend Redesign
description: This skill should be used when the user wants to "redesign this site", "make it look premium", "full redesign", "bespoke design", "this looks too generic", "make it look like a high-end agency built it", "complete visual overhaul", or asks for a from-scratch aesthetic transformation using custom design systems and CSS animations.
---

# Aesthetic Frontend Redesign

## 1. Overview
This skill is designed to guide an Antigravity agent in overhauling a generic, templated web property (like a Wix or Squarespace site) and transforming it into a distinctive, production-grade frontend interface. The goal is to avoid "AI slop" or cookie-cutter design choices and instead commit to a bold aesthetic direction (e.g., "Modern Editorial Heritage", "Brutalist", "Maximalist").

By following this skill, the agent will analyze an existing site, define a strong creative direction, scaffold a new Vite+React project, generate high-quality realistic assets, and deploy a polished, animated demo to Vercel.

## 2. Folder Structure
When running this skill, the agent will create and work within the following structure:
```text
[project-name]/
├── public/
│   └── assets/        # Generated AI images (e.g., hero.png, food.png)
├── src/
│   ├── components/    # UI components (Navbar, Hero, Footer, etc.)
│   ├── index.css      # Core design system (CSS variables, typography, animations)
│   └── App.tsx        # Main application assembly
```

## 3. Critical Rules
- **MANDATORY**: Always run `browser_subagent` first to analyze the existing site. Do not build blindly.
- **MANDATORY**: Propose a specific, named aesthetic direction (e.g., "Moody Editorial") to the user before generating images or writing code.
- **CONSTRAINT**: NEVER use generic fonts (Arial, Inter, Roboto) for primary headings. Opt for distinctive choices (Playfair Display, Instrument Serif, Space Grotesk) that elevate the frontend's aesthetics.
- **CONSTRAINT**: Avoid flat, basic color palettes (like generic purple gradients or Bootstrap blue). Use moody textures, off-whites (parchment), and highly intentional contrast.
- **ASSETS**: Never use placeholders. Always use the `generate_image` tool to create realistic necessary assets (textures, food, lifestyle photography). Use prompts like "macro shot, shallow depth of field, premium culinary magazine".
- **CHECKPOINT**: Always ask the user for approval on the aesthetic direction, layout strategy, and color palette via an `implementation_plan.md` before writing code.
- **MANDATORY**: Always deploy to Vercel as the final step and return the live URL to the user. Use the Vercel MCP (`mcp__83f244a8-ea78-4240-8b93-11adcb630e70__deploy_to_vercel`) or `npx vercel --prod --yes`. Never end a session without a live URL — no zip files, no localhost previews as final deliverables.

## 4. Step-by-Step Process

**Step 1: Reconnaissance (browser_subagent)**
- Request the target URL from the user.
- Run the `browser_subagent` to capture screenshots, view DOM structure, and analyze the current site's shortcomings (generic fonts, poor spacing, lack of motion).

**Step 2: Design Direction (implementation_plan.md)**
- Create an `implementation_plan.md` artifact.
- Outline the new creative vibe, specific color palette (using precise names/hexes), and typography choices.
- Outline the component structure.
- Call `notify_user` to request review of the plan. Wait for approval.

**Step 3: Asset Creation (generate_image)**
- Once approved, use `generate_image` to create 3-4 highly specific realistic images needed for the UI.
- Move these generated images from the brain folder to `public/assets/` in the new project directory.

**Step 4: Scaffold Foundation**
- Run `npx --yes create-vite@latest ./ --template react-ts` in the target directory to scaffold the React app.
- Write the global `src/index.css` file. Define custom CSS variables for colors, fonts, and transitions. Implement foundational styled classes.

**Step 5: Component Assembly**
- Build dynamic layouts in separate component files (`Hero.tsx`, `Navbar.tsx`, etc.).
- Break standard 1:1 grids. Add subtle CSS-only animations (staggered fade-ups, subtle zooms) or use `framer-motion` if installed.

**Step 6: Vercel Deployment**
- Run `npm run build` locally to ensure there are no TypeScript/build errors.
- Run `npx vercel --prod --yes` to deploy.
- Provide the live URL to the user.

## 5. Available Tools
- `browser_subagent`: Used for competitive analysis and finding design gaps on the original site.
- `generate_image`: Crucial for avoiding placeholders and achieving the "production-grade" feel.
- `run_command`: Used for scaffolding (`npx create-vite`), file management (`mkdir`, `cp`), and deployment (`npx vercel`).
- `command_status` & `send_command_input`: Needed to handle interactive prompts during Vite scaffolding and Vercel authentication.

## 6. API/Integration Details
- **Vite Initialization**: Use `npx --yes create-vite@latest ./ --template react-ts` followed by `npm install`.
- **Vercel CLI**:
  - `npx vercel login` (if authentication is required).
  - `npx vercel --prod --yes` (for deployment).

## 7. Quick Start
To trigger this skill, a user can say:
> "@antigravity Use the aesthetic-frontend-redesign skill to overhaul `https://example.com`. Make it look premium and unforgettable."
