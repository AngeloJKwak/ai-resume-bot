---
title: ai-resume-bot
app_file: app.py
sdk: gradio
sdk_version: 5.34.2
---
# AI Resume Bot

A conversational AI that answers questions about my background, skills, and experience — grounded in his LinkedIn profile and a written summary, so it stays factual instead of improvising. Live at [huggingface.co/spaces/angelokwak/ai-resume-bot](https://huggingface.co/spaces/angelokwak/ai-resume-bot), and embedded on [angelokwak.com](https://angelokwak.com) under "Resume Bot."

## How it works

- On startup, the bot loads `me/linkedin.pdf` and `me/summary.txt` as its knowledge base and folds them into a system prompt.
- Chat runs on OpenAI's `gpt-4o-mini` via the Chat Completions API, answering as Angelo in the first person.
- Two function-calling tools let the model take action mid-conversation:
  - `record_user_details` — captures a visitor's email/name when they want to be contacted.
  - `record_unknown_question` — logs any question the bot couldn't answer, so gaps in the knowledge base are visible.
- Both tools push a notification via [Pushover](https://pushover.net) so Angelo gets notified in real time.

## Tech stack

Python · [Gradio](https://gradio.app) (chat UI) · [OpenAI API](https://platform.openai.com) · [pypdf](https://pypdf.readthedocs.io) (LinkedIn PDF parsing) · Pushover (notifications)

## Running locally

```bash
git clone https://github.com/AngeloJKwak/ai-resume-bot.git
cd ai-resume-bot
pip install -r requirements.txt
cp .env.example .env   # fill in the values below
python app.py
```

### Environment variables

| Variable | Required | Purpose |
|---|---|---|
| `OPENAI_API_KEY` | Yes | Authenticates chat completions |
| `PUSHOVER_TOKEN` | No | Enables push notifications for captured leads/unknown questions |
| `PUSHOVER_USER` | No | Pushover user key the notification is sent to |

Without the two Pushover variables, the bot still answers questions normally — it just can't notify Angelo when it records a lead or an unanswered question.

## Deployment

`main` on GitHub is the source of truth. Pushing to it triggers a GitHub Actions workflow ([.github/workflows/sync-to-hf.yml](.github/workflows/sync-to-hf.yml)) that validates the app imports cleanly, then force-pushes to this Hugging Face Space, which rebuilds automatically.
