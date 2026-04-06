# Talking Head Video — API Reference

## fal.ai Image Generation

**Endpoint:** `POST https://fal.run/fal-ai/nano-banana-pro`
**Auth:** `Authorization: Key [FAL_KEY]` (read from `.agent/.env`)
**Model:** Always `fal-ai/nano-banana-pro` — do NOT substitute.

```json
{
  "prompt": "[IMAGE_PROMPT]",
  "negative_prompt": "[AVATAR_NEGATIVE_PROMPT]",
  "image_size": "portrait_9_16",
  "num_images": 1
}
```

Response: `images[0].url` → store as `[GENERATED_IMAGE_URL]`

**CRITICAL:** Always embed image inline with `![AVATAR_NAME](GENERATED_IMAGE_URL)` — never paste URL as text.

---

## fal.ai Video Generation

**Endpoints by model:**

| Model | Endpoint |
|---|---|
| Kling 3.0 Pro | `fal-ai/kling-video/v3/pro/image-to-video` |
| Veo 3.1 | `fal-ai/veo3/image-to-video` |
| Sora 2 | `fal-ai/sora/image-to-video` |

**Auth:** `Authorization: Key [FAL_KEY]` (read from `.agent/.env`)

```json
{
  "prompt": "[VIDEO_PROMPT]",
  "image_url": "[GENERATED_IMAGE_URL]",
  "aspect_ratio": "9:16",
  "duration": "[VIDEO_DURATION]",
  "enable_audio": true
}
```

**Cost rates:**
| Mode | Rate |
|---|---|
| Audio OFF | $0.112/sec |
| Audio ON | $0.168/sec |
| Audio ON + Voice Control | $0.196/sec |

---

## Airtable — Projects Table

**Create record:**
```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects
Authorization: Bearer [AIRTABLE_API_KEY]
```

| Field | Value |
|---|---|
| Ad Name | `[AVATAR_NAME] — [ENVIRONMENT_NAME] — [date]` |
| Product Name | (updated after video brief) |
| Image Prompt | `[IMAGE_PROMPT]` |
| Image Generator | `fal.ai` |
| Image Status | `Generated` |
| Generated Image 1 | `[GENERATED_IMAGE_URL]` |

**Update record (Step 11):**
```
PATCH https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/Projects/[AIRTABLE_RECORD_ID]
Authorization: Bearer [AIRTABLE_API_KEY]
```

| Field | Value |
|---|---|
| Video Prompt | `[VIDEO_PROMPT]` |
| Video Generator | `[VIDEO_MODEL]` |
| Video Status | `Pending` |
| Video Generation 1 | `[GENERATED_VIDEO_URL]` |

---

## Airtable — AI Avatars Table

Check if record exists first. Update if yes, create if no.

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Avatars
Authorization: Bearer [AIRTABLE_API_KEY]
```

| Field | Value |
|---|---|
| Name | `[AVATAR_NAME]` |
| Age | `[AGE as number]` |
| Gender | `[GENDER]` |
| Hero Image | `[GENERATED_IMAGE_URL]` |
| Description | Brief appearance/clothing/vibe/setting |
| Status | `Active` |

---

## Airtable — AI Environments Table

Check if record exists first. Update if yes, create if no.

```
POST https://api.airtable.com/v0/{AIRTABLE_BASE_ID}/AI%20Environments
Authorization: Bearer [AIRTABLE_API_KEY]
```

| Field | Value |
|---|---|
| Name | `[ENVIRONMENT_NAME]` |
| Vibe | One-word vibe (luxury, urban, cozy…) |
| Visual Description | 1-2 sentence summary |
| Lighting Notes | From `[ENVIRONMENT_PROMPT]` |
| Best For | Content type this suits |
| Image Prompt Block | `[ENVIRONMENT_PROMPT]` |
| Status | `Active` |

---

## Key Defaults

| Setting | Default |
|---|---|
| Image model | `fal-ai/nano-banana-pro` |
| Video model | Kling 3.0 Pro |
| Aspect ratio | 9:16 vertical |
| Resolution | 2K |
| Airtable table | Projects |

---

## File Path Reference

| Asset | Path |
|---|---|
| Hooks library | `/viral-hooks-skill/references/hooks-library.md` |
| Avatar skills | `/skills/ai-avatars/[name]/SKILL.md` |
| Custom avatar skill | `/skills/ai-avatars/custom/SKILL.md` |
| Environment skills | `/skills/ai-environments/[name]/SKILL.md` |
| Custom environment skill | `/skills/ai-environments/custom/SKILL.md` |
