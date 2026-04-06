---
name: Creative Cloner
description: This skill should be used when the user wants to "clone a video", "recreate this video style", "analyze this video", "clone this content", "extract the scene from this video", "match this video format", or needs to reverse-engineer a video's style and recreate it with a different subject using Kie.ai and Gemini.
---

# Creative Cloner Workflow

## Overview
This skill provides a fully automated, end-to-end pipeline for "creative cloning"—analyzing a reference video, extracting its key scenes (camera angles, lighting, actions), generating custom AI image replacements, bringing them to life with AI video generation, tracking all metadata in an Airtable database, and stitching the final clips together into a finished video.

## Folder Structure
- `inputs/`: Source materials (reference video to clone, reference images of the subject/character to inject).
- `outputs/`: Generated assets (YAML analysis files, generated `scene_*.mp4` clips, final merged video).
- `tools/`: The Python scripts driving each step of the pipeline.
- `.agent/`: Hidden Agent configuration folder.
  - `.env`: Holds all API keys (`GEMINI_API_KEY`, `AIRTABLE_TOKEN`, `AIRTABLE_BASE_ID`, `KIE_API_KEY`).
  - `skills/`: Contains this `SKILL.md` documentation.
  - `workflows/`: Reusable workflow definitions.
- `node_modules/`: Contains `ffmpeg-static` used for video concatenation.

## Critical Rules
*   **COST WARNING:** Both `nano-banana-pro` and `kling-2.6/image-to-video` incur real costs on Kie.ai. **Always warn the user before running batch generations.** If generation returns a `402` error ("Credits insufficient"), the user must top up their Kie.ai balance before continuing.
*   **API KEY SECURITY:** Never log or save API keys in Git or public text files. Read them strictly from `.agent/.env`.
*   **FFMPEG DEPENDENCY:** The combine script uses the bundled `ffmpeg-static` package (`node_modules/ffmpeg-static/ffmpeg`). Direct system `brew` installs are unnecessary; rely on the local Node artifact.
*   **POLLING TIMEOUTS:** Kling 2.6 takes 2–4 minutes. The polling logic in `generate_videos.py` allows up to 10 minutes (60 attempts * 10s) before timing out. Do not abort early.

## Step-by-Step Process

1.  **Analyze Video (`tools/analyze_video.py`)**
    *   Uploads a reference `.mp4` video (from `inputs/`) to Gemini API.
    *   Uses `gemini-2.5-flash` to extract a scene-by-scene breakdown.
    *   Saves the analysis as YAML in `outputs/`.
2.  **Generate Prompts (Agent Task)**
    *   Based on the YAML analysis, the AI Agent creates detailed `start_image_prompt` and `video_prompt` strings, incorporating the user's specific subject/product replacing the original actors.
3.  **Setup Tracking Database (`tools/setup_airtable.py`)**
    *   Creates a "Scenes" table in Airtable if it doesn't exist.
    *   Logs the generated prompts to the table under the specified "Project Name".
4.  **Generate Starting Frames (`tools/generate_images.py`)**
    *   Uploads local reference images to CatBox to get public URLs.
    *   Calls Kie.ai `google/nano-banana-edit` model with the `start_image_prompt`.
    *   Polls for completion, extracts the generated image URL, and logs it to the `start_image` attachment field in Airtable.
5.  **Generate Videos (`tools/generate_videos.py`)**
    *   Fetches the `video_prompt` and `start_image` URL from Airtable.
    *   Calls Kie.ai `kling-2.6/image-to-video` to animate the frame.
    *   Polls for 2-4 minutes, logs the video URL to `scene_video` in Airtable, and downloads it to `outputs/`.
6.  **Combine & Overlay (`tools/combine_all.py`)**
    *   Uses local `ffmpeg-static` to concatenate all downloaded `scene_*.mp4` clips.
    *   Optionally adds an audio track (`--music`) with a 2-second fade-out.
    *   *(If `--project` is passed)* Uploads the final merged mp4 to CatBox and logs the URL to the `final_video` Airtable field.

## Available Tools

| Tool | Usage | Description |
| :--- | :--- | :--- |
| `analyze_video.py` | `python3 tools/analyze_video.py inputs/ref.mp4 outputs/analysis.yaml` | Analyzes source video via Gemini 2.5 Flash and extracts scene metadata. |
| `setup_airtable.py` | `python3 tools/setup_airtable.py` | Initializes Airtable schema and seeds initial prompt data. |
| `generate_images.py` | `python3 tools/generate_images.py "Project Name" "Scene Name"` | Creates Kie.ai static image, logs to Airtable `start_image`. |
| `generate_videos.py` | `python3 tools/generate_videos.py "Project Name" "Scene Name"` | Creates Kie.ai Kling video from the image, logs to Airtable `scene_video`. |
| `combine_all.py` | `python3 tools/combine_all.py out/v1.mp4 out/v2.mp4 --music track.mp3 --output final.mp4 --project "Project Name"` | Concatenates video clips, overlays audio, logs final output to Airtable. |

## API / Integration Details

### Kie.ai (Video & Image Generation)
*   **Auth:** `Authorization: Bearer <KIE_API_KEY>`
*   **Endpoints:**
    *   Create Task: `POST https://api.kie.ai/api/v1/jobs/createTask`
    *   Poll Task: `GET https://api.kie.ai/api/v1/jobs/recordInfo?taskId=<ID>`
*   **Image Model Payload (`google/nano-banana-edit`):**
    ```json
    {
      "model": "google/nano-banana-edit",
      "input": {
        "prompt": "Subject description here...",
        "image_urls": ["https://catbox.moe/..."]
      }
    }
    ```
*   **Video Model Payload (`kling-2.6/image-to-video`):**
    ```json
    {
      "model": "kling-2.6/image-to-video",
      "input": {
        "prompt": "Motion description here...",
        "image_urls": ["https://airtable.com/..."],
        "duration": "5",
        "sound": false
      }
    }
    ```

### CatBox (Temporary Public Image Hosting)
*   Used because Kie.ai requires public URLs for input images.
*   **Endpoint:** `POST https://catbox.moe/user/api.php`
*   No API key required.
*   **Form Data:** `reqtype=fileupload`, `fileToUpload=@local_file.png`
*   Returns: Plain text URL.

### Airtable
*   **Auth:** `Authorization: Bearer <AIRTABLE_TOKEN>`
*   **Base ID:** From `.env` file (`AIRTABLE_BASE_ID`).

## Database / Storage Schema

**Table Name:** `Scenes`
| Field Name | Type | Purpose |
| :--- | :--- | :--- |
| Project Name | Single line text | Groups scenes for a project. |
| Scene | Single line text | Identifier (e.g., "Scene 1 - Intro"). |
| start_image_prompt | Long text | Prompt used for NanoBanana Pro. |
| video_prompt | Long text | Prompt used for Kling 2.6. |
| start_image | Attachment | Kie.ai generated static image array. |
| scene_video | Attachment | Kie.ai generated video array. |
| final_video | Attachment | Combined video array (logged by `combine_all.py`). |

## Quick Start
1. Ensure `.agent/.env` has `GEMINI_API_KEY`, `AIRTABLE_TOKEN`, `AIRTABLE_BASE_ID`, and `KIE_API_KEY`.
2. Ensure `node_modules/ffmpeg-static` is installed (`npm install ffmpeg-static`).
3. Run `analyze_video.py` on your source mp4.
4. Have the Agent write prompts based on the YAML output.
5. Create Airtable records via `setup_airtable.py`.
6. Run `generate_images.py "ProjectName" "SceneName"` for each scene.
7. Run `generate_videos.py "ProjectName" "SceneName"` for each scene.
8. Run `combine_all.py` on the downloaded MP4s inside `outputs/` to generate the final deliverable.
