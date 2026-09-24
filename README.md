# 📰 My News — AI-Powered Morning Brief

A self-hosted news digest automation built with **n8n**, **Docker**, and **Gemini AI**. It pulls the latest news from Morocco, World, and Tech sources, uses Gemini to filter, merge, and summarize the most important stories, and delivers a clean, formatted briefing straight to a Discord channel.

---

## What it does

1. Fetches the latest articles from 3 RSS feeds (Morocco, World, Tech)
2. Merges and formats them into a single text block
3. Sends that text to Gemini, which selects the top 3 stories per category, removes duplicates, and writes concise summaries
4. Sanitizes the AI output (fixes characters that can break Discord messages)
5. Delivers a polished embed message to a Discord channel via webhook

No frontend. No backend. No database. Just a workflow you run whenever you want your daily briefing.

---

## Tech Stack

| Piece | Tool |
|---|---|
| Automation / orchestration | [n8n](https://n8n.io) |
| Containerization | Docker + Docker Compose |
| AI summarization | Google Gemini API |
| News sources | RSS feeds |
| Delivery | Discord Webhook |
| Trigger | Manual (click to run) |

---

## Workflow Overview

```
Manual Trigger
      │
      ├──► RSS Read (Morocco)
      ├──► RSS Read (World)
      └──► RSS Read (Tech)
                │
                ▼
              Merge
                │
                ▼
      Format Articles for AI (Code)
                │
                ▼
              Gemini
       (filter, rank, summarize)
                │
                ▼
     Sanitize Gemini Output (Code)
                │
                ▼
         Discord (Webhook)
```

---

## Prerequisites

Before you start, make sure you have:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- A free [Google Gemini API key](https://aistudio.google.com/) (from Google AI Studio)
- A Discord server you own or manage, with a [Webhook URL](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks) set up for the channel you want the briefing sent to

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/my-news.git
cd my-news
```

### 2. Start n8n with Docker

```bash
docker compose up -d
```

Once it's running, open **http://localhost:5678** in your browser. On first run, n8n will ask you to create a local account — this is just for your own login and stays on your machine.

### 3. Import the workflow

1. Inside n8n, click **Workflows** → **Import from File**
2. Select `workflows/My workflow.json` from this repo
3. The full pipeline (RSS → Merge → Gemini → Discord) will load onto the canvas

### 4. Add your credentials

This project stores credentials inside n8n itself (not in a `.env` file), so you'll set them up directly in the UI:

**Gemini:**
1. Click on the **Gemini** node
2. Under Credentials, click **"Create New Credential"**
3. Paste your Gemini API key from Google AI Studio
4. Save

**Discord:**
1. Click on the **Discord** node
2. Under **Credential for Discord Webhook**, click **"Set up credential"**
3. Paste your Discord webhook URL
4. Save

### 5. Run it

Click **Execute Workflow** (top right of the canvas). Within a few seconds, a formatted news briefing should land in your Discord channel.

---

## Customizing

- **Change news sources:** edit the URL in any RSS Read node. Make sure to test the feed URL in your browser first — it should show raw XML, not a webpage.
- **Change how many stories per category:** edit the prompt inside the Gemini node.
- **Change the schedule:** this project uses a Manual Trigger by default so it works out of the box for anyone self-hosting it. If you want it to run automatically every morning, swap the Manual Trigger node for a **Schedule Trigger** node — just keep in mind your Docker container needs to be running at the scheduled time for it to fire.

---

## Notes on API Keys

- Your Gemini API key is free (with usage limits) from [Google AI Studio](https://aistudio.google.com/)
- Keys don't expire, but Google can revoke them if abused
- Never share your API key or commit it to a public repo — since this project stores credentials inside n8n's local database (not in code), your keys never touch this repository

---

## Why some Code nodes exist

You'll notice two Code nodes in the workflow that aren't just "extra steps":

- **Format Articles for AI** — converts the raw merged RSS items into a clean text block Gemini can actually read (without this, Gemini receives `[object Object]` instead of real article text)
- **Sanitize Gemini Output** — strips curly quotes and backticks from Gemini's response, which can otherwise break the Discord API request

Both were added after running into real issues during development — kept here because they make the workflow reliably reproducible for anyone else running it.

---

## License

Copyright © 2026 Mohamed Amine Benbouselham.

My News is available for personal, educational, research, and other non-commercial use. Commercial use requires prior written permission from the copyright holder.

See [LICENSE](LICENSE) for the full terms.