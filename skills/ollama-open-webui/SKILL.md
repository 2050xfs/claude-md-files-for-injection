---
name: ollama-open-webui
description: >
  Deploy a local ChatGPT-like interface using Ollama + Open WebUI via Docker.
  Runs any open-source LLM (Llama 2, Code Llama, Mistral, etc.) fully on your
  machine — no API keys, no data leaving your device. Extracted from the YouTube
  tutorial: https://www.youtube.com/watch?v=qgMbU_H-fbQ
allowed-tools: Read, Bash, Write, Edit
---

# Ollama + Open WebUI — Local LLM Stack

## Overview

This skill deploys a **fully local** ChatGPT-equivalent on your machine.

| Component | Role |
|-----------|------|
| **Ollama** | Runs LLMs locally (model runner, API server on port `11434`) |
| **Open WebUI** | A polished web interface (ChatGPT-like, runs on port `3000`) |
| **Docker** | Containerizes Open WebUI for easy deployment |

**The key trick:** Open WebUI runs inside Docker, but Ollama runs on the host. The
`extra_hosts: host.docker.internal:host-gateway` entry in `docker-compose.yml` is
what allows the container to reach the host's Ollama service at port `11434`.

---

## Prerequisites

| Requirement | Check | Install |
|-------------|-------|---------|
| macOS 12+ or Linux | `uname -a` | — |
| [Homebrew](https://brew.sh) (macOS) | `which brew` | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| [Ollama](https://ollama.ai) | `which ollama` | `brew install ollama` (macOS) or `curl https://ollama.ai/install.sh \| sh` (Linux) |
| [Docker Desktop](https://www.docker.com/products/docker-desktop/) | `docker --version` | Download from docker.com |
| Git | `git --version` | `brew install git` |

---

## Step-by-Step Setup

### 1. Install Ollama

**macOS (Homebrew):**
```bash
brew install ollama
```

**Linux:**
```bash
curl https://ollama.ai/install.sh | sh
```

Start the Ollama background service:
```bash
# macOS — starts automatically via brew services
brew services start ollama

# Linux
ollama serve &
```

### 2. Pull a model

```bash
# General purpose — Llama 2 (7B, ~4GB download)
ollama run llama2

# Coding — Code Llama
ollama run codellama

# Fast & lightweight — Mistral 7B
ollama run mistral

# Quit the interactive chat when done
# Press Ctrl+D or type /bye
```

Verify Ollama is running:
```bash
curl http://localhost:11434/api/tags
# Should return JSON list of installed models
```

### 3. Clone Open WebUI

```bash
git clone https://github.com/open-webui/open-webui.git
cd open-webui
```

> **Note:** The original repo at `github.com/jmorganca/open-webui` has moved to
> `github.com/open-webui/open-webui`. Always use the current URL above.

### 4. Configure environment

```bash
cp .env.example .env
```

Open `.env` and review — no changes required for default local setup.

### 5. Configure Docker Compose for host-to-container networking

The **critical** change: edit `docker-compose.yml` to add `extra_hosts` so the
container can reach Ollama on your host machine.

**Open the file:**
```bash
nano docker-compose.yml
# or: code docker-compose.yml
```

**Add `extra_hosts` under the `open-webui` service:**

```yaml
services:
  open-webui:
    build:
      context: .
      args:
        OLLAMA_BASE_URL: '/ollama'
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - 3000:8080
    environment:
      - 'WEBUI_SECRET_KEY='
    # ADD THIS BLOCK ↓
    extra_hosts:
      - "host.docker.internal:host-gateway"
    # ADD THIS BLOCK ↑
    volumes:
      - open-webui:/app/backend/data
    restart: unless-stopped

volumes:
  open-webui: {}
```

### 6. Build and Start Open WebUI

```bash
docker-compose up -d --build
```

- First run takes 5–15 minutes while the Docker image builds.
- `-d` runs in detached (background) mode.

Watch logs if needed:
```bash
docker-compose logs -f open-webui
```

### 7. Connect Open WebUI to Ollama

1. Open your browser: **http://localhost:3000**
2. Create an account (local only, stored in the Docker volume)
3. Go to **Settings → Connection**
4. Set **Ollama Base URL** to:
   ```
   http://host.docker.internal:11434
   ```
5. Click **Save** — Open WebUI will now list your installed models

### 8. Start chatting

- Select a model from the dropdown (e.g., `llama2:latest`)
- Chat just like ChatGPT — fully offline, fully private

---

## Management Commands

```bash
# Stop the stack
docker-compose down

# Restart
docker-compose up -d

# Update Open WebUI image
docker-compose pull && docker-compose up -d --build

# List installed Ollama models
ollama list

# Remove a model
ollama rm llama2

# See running containers
docker ps
```

---

## Troubleshooting

| Problem | Likely Cause | Fix |
|---------|--------------|-----|
| "Connection refused" at localhost:3000 | Container still building | Wait, run `docker ps` to check |
| "Could not connect to Ollama" in UI | Wrong base URL | Set to `http://host.docker.internal:11434`, not `localhost` |
| Models not listed in UI | Ollama not running | Run `ollama serve` or `brew services start ollama` |
| Docker build fails | Docker daemon not running | Open Docker Desktop app first |
| Slow responses | Model too large for RAM | Use smaller model: `ollama run mistral` or `ollama run phi` |

---

## Model Recommendations

| Model | Size | Best For |
|-------|------|----------|
| `mistral` | 4.1GB | Fast, general purpose — best starter |
| `llama2` | 3.8GB | General chat |
| `codellama` | 3.8GB | Coding assistance |
| `phi` | 1.7GB | Very fast, limited RAM machines |
| `llama2:13b` | 7.3GB | Higher quality, needs 16GB RAM |
| `mixtral` | 26GB | Best quality, needs 32GB RAM |

---

## Architecture

```
┌─────────────────────────────────────────┐
│                 HOST                     │
│                                          │
│   Ollama (port 11434)                   │
│   ├── llama2:latest                     │
│   ├── codellama:latest                  │
│   └── mistral:latest                    │
│                                          │
│   ┌──────────────────────────────────┐  │
│   │      Docker Container            │  │
│   │                                  │  │
│   │   Open WebUI (port 8080→3000)   │  │
│   │   ↕ host.docker.internal:11434  │  │
│   └──────────────────────────────────┘  │
└─────────────────────────────────────────┘

Browser → http://localhost:3000 → Open WebUI → Ollama → LLM
```

---

## Source

**Video:** https://www.youtube.com/watch?v=qgMbU_H-fbQ
**Extracted:** 2026-04-04 via video-to-action skill + Gemini analysis
**Open WebUI repo:** https://github.com/open-webui/open-webui
**Ollama:** https://ollama.ai
