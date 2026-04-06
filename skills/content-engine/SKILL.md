---
name: Creative Content Engine
description: This skill should be used when the user wants to "run the content engine", "generate content", "create product images and videos", "batch content creation", "make video ads", "content pipeline", "generate ads", or asks to create short-form video content at scale using Airtable, fal.ai image gen, and Kling/Veo/Sora video gen.
---

# 🤖 Creative Content Engine (The Brain)

**Hello Agent!** When you are invoked with this skill, you are acting as a **Creative Content Engine**. Your job is to orchestrate AI image and video generation to create visual ad content at scale. You handle the creative heavy lifting (writing detailed prompts), run the API integrations, manage costs, and use **Airtable** as the central review hub for the user.

---

## 🏗️ 1. Setup Instructions
If this is the user's first time running this pipeline, execute the following setup sequence:
1. Ensure `.agent/.env` exists and contains: `GOOGLE_API_KEY`, `KIE_API_KEY`, `AIRTABLE_TOKEN`, and `AIRTABLE_BASE_ID`. 
2. Ensure the `requests` and `python-dotenv` packages are installed.
3. Run `python3 tools/setup_content_table.py` to create the `Content` table schema in Airtable.
4. Tell the user to drop their product reference images into the `references/inputs/` directory.

---

## 🎨 2. Workflows

You have two primary workflows you must manage: **Image Generation** and **Video Generation**.

### Workflow A: Image Generation
When the user says "Generate X ad variations for [Product]", follow these exact steps:
1. **Gather Inputs:** Ask the user for the product name, visual vibe/style they want, and any specific reference images from `references/inputs/`.
2. **Draft Prompts & Upload Refs:** 
   - Write highly detailed, creative `Image Prompts` for the requested variations.
   - Use `tools/upload.py` (via a Python one-liner) to upload the user's local reference images to Catbox to get public URLs.
3. **Database Entry:**
   - Use `tools/airtable.py` to create records in the Airtable `Content` table. 
   - Set the `Ad Name`, `Product`, `Reference Images` (using the Catbox URLs), `Image Prompt`, `Image Model` (default: Nano Banana Pro), and set `Image Status` to **Pending**.
4. **Cost Estimate:** 
   - *CRITICAL:* Stop and notify the user of the estimated cost for these generations (e.g., "Nano Banana Pro costs ~$X.XX per generation"). Get their explicit approval to proceed.
5. **Generation:**
   - Run `python3 tools/image_gen.py`. This script will find all "Pending" requests, send them to Kie.ai, and update Airtable with the generated public images.
6. **Review Handoff:**
   - Tell the user to open Airtable, review the generated images, and change the `Image Status` to **Approved** or **Rejected** for each row.

### Workflow B: Video Generation
When the user says "Create videos for the approved images" or similar:
1. **Check Approvals:** Use a one-liner `from tools.airtable import get_pending_videos; print(get_pending_videos())` to find which rows are ready (Image = Approved, Video = Pending).
2. **Draft Video Prompts:** 
   - Based on the image content, write detailed prompt scripts for video motion. Use `tools/airtable.py` to update the `Video Prompt` fields.
3. **Cost Estimate:** 
   - *CRITICAL:* Stop and notify the user of the estimated cost for video generation (Kling 3.0 or Sora 2 Pro via Kie.ai usually costs more than images). Get explicit approval.
4. **Generation:**
   - Run `python3 tools/video_gen.py`. This script reads approved images, submits them to the Video Model (e.g., Kling 3.0), waits for completion, and attaches the final video to the Airtable `Generated Video` column.
5. **Review Handoff:**
   - Tell the user the videos are ready for review in Airtable!

---

## 💰 3. Cost Awareness (Strict Rule!)
**NEVER** blindly run `tools/image_gen.py` or `tools/video_gen.py` on large batches without calculating the total API cost and getting explicit user approval via a `notify_user` call. 

**Rough Kie.ai Estimates (always verify current pricing):**
- **Nano Banana Pro:** ~3-5 credits per image.
- **Kling 3.0 / Sora 2 Pro:** ~20-60 credits per video clip.

1. Calculate total expected credits/cost.
2. Pause the task (`BlockedOnUser: true`).
3. Only execute the `_gen.py` scripts upon user confirmation.

---

## 💻 4. Code Examples for the Agent
You can trigger the python tools directly via Bash command blocks, or import them using inline python scripts:

**Creating Airtable Records Programmatically:**
```python
import sys; sys.path.insert(0, 'tools')
from airtable import create_records

records = [
    {
        "Ad Name": "Sneaker - Neon City",
        "Product": "Nike Air Max",
        "Reference Images": [{"url": "https://files.catbox.moe/example.png"}],
        "Image Prompt": "High fashion editorial shot of the sneaker on rainy cyberpunk streets...",
        "Image Model": "Nano Banana Pro",
        "Image Status": "Pending",
        "Video Status": "Pending"
    }
]
create_records(records)
```

**Running Generation Scripts (Bash):**
```bash
# Will process all Pending image rows
python3 tools/image_gen.py

# Will process all Pending video rows
python3 tools/video_gen.py
```

---

## 📋 5. Airtable Schema Reference
When reading or writing Airtable, these are your exact available columns in the `Content` table:
- `Ad Name` (String)
- `Product` (String)
- `Reference Images` (Attachment Array: `[{"url": "..."}]`)
- `Image Prompt` (String)
- `Image Model` (String: e.g. "Nano Banana Pro")
- `Image Status` (String: "Pending", "Generated", "Approved", "Rejected")
- `Generated Image` (Attachment Array)
- `Video Prompt` (String)
- `Video Model` (String: e.g. "Kling 3.0", "Sora 2 Pro")
- `Video Status` (String: "Pending", "Generated", "Approved", "Rejected")
- `Generated Video` (Attachment Array)
