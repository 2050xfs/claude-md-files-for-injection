---
name: restaurant-ai-engine
description: This skill should be used when the user wants to "run the restaurant engine", "find restaurant leads", "enhance food photos", "scrape restaurants", "generate food photos with AI", "restaurant outreach", or needs to identify restaurants with bad photography and replace them with AI-generated premium food images.
---

# Restaurant AI Refresh Engine

## Overview

Two flows that work together to find restaurants with bad food photography, enhance their images with AI, and create outreach-ready assets.

**Flow 1 — Prospecting:** Scrapes Google Maps → Gemini Vision filters food images → Uploads leads to Airtable  
**Flow 2 — Photo Editing:** Pulls Food leads from Airtable → Generates 2 premium shots with Nano Banana Pro via Gemini API → Uploads results back to Airtable

---

## Setup

### Environment Variables

Set these in `.agent/.env`:

```
GOOGLE_API_KEY=your_gemini_api_key
AIRTABLE_TOKEN=your_airtable_personal_access_token
AIRTABLE_BASE_ID=your_airtable_base_id
KIE_API_KEY=your_kie_api_key  # for optional Kling video
```

### Install Dependencies

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install playwright pyairtable requests python-dotenv google-genai pillow
playwright install chromium
```

### One-Time Airtable Setup

Creates the "Restaurant Leads" table with all required fields:

```bash
python tools/setup_leads_table.py
```

---

## Flow 1: Prospecting

Scrapes Google Maps, filters images with Gemini Vision, and uploads curated leads to Airtable.

```bash
python tools/rest_prospecting_flow.py --location "Sydney" --limit 5
```

**What it does:**
1. Searches Google Maps for restaurants in the given location
2. Extracts: name, website, Instagram, reviews, photos
3. Sends the top photo to Gemini 2.5 Flash — classifies as "Food" or "Non-Food"
4. Creates a record in Airtable "Restaurant Leads" with status and reference URL

**Arguments:**
- `--location` — City or area to target (e.g., "Italian restaurants in Brooklyn")
- `--limit` — Max number of restaurants to prospect (default: 3)

---

## Flow 2: Professional Photo Editing

For all Airtable leads with `Analysis Status = Food`, generates 2 premium AI food shots via Nano Banana Pro.

```bash
python tools/rest_photo_editor.py --limit 5
```

**What it does:**
1. Fetches all "Food" leads from Airtable without AI photos yet
2. Downloads the reference photo from `Reference Photo URL`
3. Sends to `nano-banana-pro-preview` via Gemini API with two distinct prompts:
   - **Photo 1:** High-end restaurant shot, perfect lighting, appetizing
   - **Photo 2:** Different camera angle (overhead or low), same dish
4. Uploads generated images to Catbox for public URLs
5. Attaches both URLs back to Airtable as `AI Enhanced Photo 1` and `AI Enhanced Photo 2`

**Arguments:**
- `--limit` — Max leads to process (default: all)

---

## Airtable Schema

**Table:** `Restaurant Leads`

| Field | Type | Purpose |
|---|---|---|
| Restaurant Name | Text | Business name |
| Website | URL | Restaurant website |
| Instagram Handle | URL | IG profile |
| Reviews | Text | Google rating |
| Google Maps Photos | Attachment | Raw scraped photos |
| Reference Photo URL | URL | Top photo URL (plain text for reliable API reads) |
| Analysis Status | Select | Pending / Food / Non-Food |
| AI Enhanced Photo 1 | Attachment | Premium food shot |
| AI Enhanced Photo 2 | Attachment | Angle-shifted shot |
| Video Promo | Attachment | (Optional) Kling video |

---

## Script Reference

| Script | Purpose |
|---|---|
| `tools/setup_leads_table.py` | One-time Airtable schema creation |
| `tools/rest_google_scraper.py` | Playwright-based Google Maps scraper |
| `tools/rest_filter_images.py` | Gemini Vision image classifier |
| `tools/rest_prospecting_flow.py` | **Flow 1 orchestrator** |
| `tools/rest_airtable.py` | Airtable helper for Restaurant Leads table |
| `tools/rest_photo_editor.py` | **Flow 2 orchestrator** |

---

## Notes

- The scraper uses headless Playwright (Chromium). Run `playwright install chromium` once before first use.
- Nano Banana Pro returns `inline_data` (raw image bytes). The photo editor extracts these and uploads to [Catbox](https://catbox.moe) for public permanent URLs.
- The filter uses `gemini-2.5-flash`. Ensure your API key has access to this model.
- Google Maps photo extraction depends on DOM structure — it may need selector updates if Google changes their UI.
