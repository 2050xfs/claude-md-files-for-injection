---
name: Digital Business Card Builder
description: Builds and deploys a two-layer premium digital identity system — a 3D interactive business card pass (utility) + a cinematic GSAP-animated brand story landing page — from a single JSON data file. Ships to Vercel in one command.
---

# Digital Business Card Builder

## Overview
This skill produces a production-ready React/Vite application that gives any professional a cinematic digital identity in two layers:

1. **Digital Pass** — A mobile-first card featuring a 3D business card widget (tilts on hover, flips on click to reveal a QR/NFC back face), vCard download, shareable link, contact links, portfolio section, and social footer.
2. **Cinematic Landing** — A full GSAP scroll-animated brand story with a hero section, three interactive feature cards, a philosophy manifesto, sticky-stacking protocol steps, and a dark system footer. Accessible via "Explore Brand Story" CTA on the pass.

Both layers share a single `cards.json` data file. The project ships to a live Vercel URL via `npx vercel --prod`.

## Folder Structure
- `src/data/cards.json` — **Primary data source.** One JSON object per card holder. All content is driven from here — never hardcode names, emails, or phone numbers in components.
- `src/App.jsx` — Root component. Manages `currentView` state (`'pass'` | `'landing'`) and GSAP fade-out/fade-in transition between views.
- `src/components/NoiseOverlay.jsx` — SVG `feTurbulence` filter at 5% opacity. Applied globally for premium non-digital paper texture.
- `src/components/cards/DigitalPass.jsx` — The utility-layer pass UI. Accepts `data` (card holder object) and `onExplore` (callback to switch view).
- `src/components/cards/BusinessCard3D.jsx` — The 3D flip widget inside the pass. Uses explicit CSS classes from `index.css` (not arbitrary Tailwind values) for `preserve-3d` and `backface-visibility`.
- `src/components/CinematicLanding.jsx` — Full brand story page. Accepts `data` and `onBack` (callback to return to pass). Calls `window.scrollTo(0,0)` on mount.
- `src/utils/vcard-generator.js` — Generates a VCard 3.0 `.vcf` blob and triggers a browser download.
- `src/index.css` — Tailwind directives + explicit 3D CSS classes (`.card-3d-scene`, `.card-3d-inner`, `.card-face`, `.card-face-back`, `.shimmer-hover`) + scrollbar refinements.

## Critical Rules
- **COST WARNING:** `npx vercel --prod --yes` charges against the user's Vercel plan. **Always confirm before running.** If the account is on the free tier, warn that bandwidth limits apply.
- **Never use arbitrary Tailwind values for 3D** — `preserve-3d`, `backface-visibility`, and `perspective` must be defined as explicit CSS rules in `index.css`. Tailwind v4's JIT may not compile them correctly.
- **GSAP must use context + cleanup** — All animations must be wrapped in `gsap.context(() => { ... }, ref)` and return `ctx.revert()`. Failure to do this causes scroll trigger zombies on view switches.
- **`window.scrollTo(0, 0)` is required** in `CinematicLanding.jsx`'s first `useEffect`. Without it, the landing page inherits the scroll position from the pass, breaking ScrollTrigger start positions.
- **Run `npm run build` before every deployment** to catch import/transform errors that the dev server swallows silently.
- **Checkpoint — new card holder data:** Confirm all fields (phone, email, socials, 3 value props, portfolio) with the user before writing to `cards.json` and deploying.

## Step-by-Step Process

1. **Gather identity data from user**
   - Full name, title, organization
   - Email, phone (digits only), website URL
   - LinkedIn + Instagram (or other social) URLs
   - 3 value propositions: `{ title, description }` each
   - Portfolio items: `{ name, url }` — up to 5
   - Aesthetic preset (see Design System below)
   - Brand one-liner for the Cinematic Landing hero

2. **Scaffold project (first time only)**
   ```bash
   npm create vite@latest digital-business-cards -- --template react
   cd digital-business-cards
   npm install gsap lucide-react
   npm install -D tailwindcss @tailwindcss/vite
   ```
   For an existing project, skip and go to step 3.

3. **Write card holder data to `src/data/cards.json`**
   Use the data schema below. `App.jsx` loads `cardsData[0]` by default.

4. **Verify 3D CSS classes exist in `src/index.css`**
   Must include `.card-3d-scene { perspective: 1200px; }`, `.card-3d-inner { transform-style: preserve-3d; }`, `.card-face { backface-visibility: hidden; position: absolute; inset: 0; }`, `.card-face-back { transform: rotateY(180deg); }`.

5. **Build and verify locally**
   ```bash
   npm run build   # must pass with 0 errors
   npm run dev     # open http://localhost:5173
   ```
   Verify:
   - [ ] Loading screen → fades to Digital Pass
   - [ ] 3D card tilts on hover, flips on click
   - [ ] SAVE button downloads a `.vcf` file
   - [ ] "Explore Brand Story" button transitions to Cinematic Landing
   - [ ] "Return" button in Landing nav returns to Pass
   - [ ] All contact links, portfolio links, and social icons work

6. **Confirm with user before deploying**

7. **Deploy to Vercel**
   ```bash
   npx vercel --prod --yes
   ```
   Output will include:
   - `Production:` — unique build URL
   - `Aliased:` — clean slug URL (e.g. `https://upflex-digital-cards.vercel.app`)

   Share the **Aliased** URL with the client.

## Available Tools

| Tool | Usage | Description |
| :--- | :--- | :--- |
| `src/utils/vcard-generator.js` | `import { generateVCard } from '../utils/vcard-generator'` | Generates VCard 3.0 `.vcf` blob and triggers browser download. Called by SAVE button. |
| `src/components/cards/BusinessCard3D.jsx` | `<BusinessCard3D data={cardData} />` | Self-contained 3D flip widget. Manages own GSAP tilt/flip state. Reads `name`, `title`, `organization` from `data` prop. |
| `src/components/NoiseOverlay.jsx` | `<NoiseOverlay />` | Drop in as first child of any view container for global noise texture. |

## API / Integration Details

### Vercel CLI
- **Command:** `npx vercel --prod --yes`
- **Auth:** Handled interactively on first run (OAuth browser login). Subsequent runs use cached token.
- **Build config (auto-detected):** Framework = Vite, Build Command = `npm run build`, Output = `dist/`
- **No `vercel.json` required** — Vite SPA is auto-detected.

### vCard Generator
- **Standard:** VCard 3.0
- **Output:** `.vcf` text file downloaded via browser blob URL
- **Required fields from `data`:** `name`, `organization`, `title`, `phone`, `email`, `url`
- **Example output:**
  ```
  BEGIN:VCARD
  VERSION:3.0
  FN:Edgar Gillies
  ORG:Upflex Digital
  TITLE:CEO
  TEL;TYPE=CELL:9166598073
  EMAIL;TYPE=INTERNET:info@upflexdigital.com
  URL:https://upflexdigital.com
  END:VCARD
  ```

## Database / Storage Schema

**File:** `src/data/cards.json`

| Field | Type | Required | Notes |
| :--- | :--- | :--- | :--- |
| `id` | string | Yes | URL-safe slug, e.g. `"edgar-upflex"` |
| `name` | string | Yes | Full display name |
| `organization` | string | Yes | Company name |
| `title` | string | Yes | Job title |
| `phone` | string | Yes | Digits only, no formatting |
| `email` | string | Yes | |
| `url` | string | Yes | Full `https://` URL |
| `socials` | object | Yes | Keys are network names (`linkedin`, `instagram`, etc.), values are full URLs |
| `template_id` | string | Yes | Currently: `"digital-pass-minimal"` |
| `value_props` | array | Yes | Exactly 3 objects: `{ title, description }` |
| `portfolio` | array | No | 1–5 objects: `{ name, url }` |

**Example `cards.json`:**
```json
[
  {
    "id": "edgar-upflex",
    "name": "Edgar Gillies",
    "organization": "Upflex Digital",
    "title": "CEO",
    "phone": "9166598073",
    "email": "info@upflexdigital.com",
    "url": "https://upflexdigital.com",
    "socials": {
      "linkedin": "https://linkedin.com/in/eddie-upflex",
      "instagram": "https://instagram.com/eddie.upflex"
    },
    "template_id": "digital-pass-minimal",
    "value_props": [
      { "title": "AI-Driven Strategy", "description": "Leveraging cutting-edge AI to automate and scale business growth." },
      { "title": "Compelling Brand Identity", "description": "Crafting unique, high-fidelity digital personas that resonate." },
      { "title": "Maximized Digital Impact", "description": "Precision marketing solutions that drive conversion and engagement." }
    ],
    "portfolio": [
      { "name": "Alliance Direct Services", "url": "https://alliancedirectservices.com" },
      { "name": "Alopecia Hair Society", "url": "https://alopeciahairsociety.com" }
    ]
  }
]
```

## Quick Start
1. Copy the project from `/Users/eddie.upflex/.gemini/antigravity/scratch/digital-business-cards/` or scaffold fresh with Vite + React.
2. Run `npm install`.
3. Edit `src/data/cards.json` with the new client's data.
4. Run `npm run build` — must pass with 0 errors.
5. Run `npm run dev` and verify all interactions locally.
6. Confirm deployment with user, then: `npx vercel --prod --yes`.
7. Share the **Aliased** URL from the Vercel output.

**Live reference deployment:** `https://upflex-digital-cards.vercel.app`
