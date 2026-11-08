# Prep Buddy: LLM chatbot with context management

A multi-turn chatbot backed by the **Google Gemini API**. Prep Buddy is a placement-preparation coach for CS students (mock interviews, concept explanations, resume help).

## Features

- **Backend endpoint**: `POST /api/chat` sends the conversation to Gemini and returns the reply.
- **Multi-turn memory**: history is stored per `conversation_id` and sent with every request.
- **Context window management**: once a conversation exceeds `MAX_MESSAGES` (12), the oldest messages are summarised into a running summary (added to the system prompt) and only the last `KEEP_RECENT` (6) messages are kept verbatim. If the summary call fails, it falls back to plain truncation.
- **System prompt**: defined persona, scope and style rules (see `SYSTEM_PROMPT` in `app.py`).
- **Error handling**: timeouts, rate limits, bad API key, network failures and empty replies produce clear messages in the UI. Failed turns are not added to history. Timeouts and 5xx errors are retried once.
- **Secrets**: the API key is read from the `GEMINI_API_KEY` environment variable (`.env`, which is git-ignored).

## Run it

```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env        # then put your key in .env (free key: https://aistudio.google.com/apikey)
python app.py
```

Open http://127.0.0.1:5000

## Tests

```bash
pytest -q
```

The tests use a fake LLM (no API key needed) and cover multi-turn memory, summarisation, the truncation fallback, failure rollback, and API validation.

## Project layout

| File | Purpose |
|---|---|
| `app.py` | Flask backend, Gemini calls, history and context management |
| `index.html` | Chat UI |
| `test_context.py` | Tests |
| `.env.example` | Template for environment variables |

## Notes

- Conversations live in server memory, so they reset when the server restarts. A database would be the next step for persistence.
- To switch models, set `GEMINI_MODEL` in `.env`.
