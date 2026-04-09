# 🔬 Multi-Agent Market Research Platform

A premium AI research tool that runs **dual-model research** (Claude + Gemini), passes the merged output through a **3-agent LLM Council**, and synthesises a **confidence-scored final report** — exportable as PDF, DOCX, and PPTX.

---

## Architecture

```
User Query
    │
    ├─► Claude Opus  ─┐
    │                  ├─► Merge (Haiku) ─► LLM Council ─► Synthesis ─► Export
    └─► Gemini 1.5 Pro─┘     (conflicts)   (3 agents)      (Opus)      PDF/DOCX/PPTX
```

**Council agents:** The Skeptic · Financial Analyst · Industry Strategist

---

## Quick Start

### 1. Clone & install

```bash
git clone <your-repo>
cd market_research_platform

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt
```

### 2. Set API keys

Option A — environment variables (recommended for production):
```bash
cp .env.example .env
# Edit .env with your keys
export ANTHROPIC_API_KEY=sk-ant-...
export GOOGLE_API_KEY=AIza...
```

Option B — enter keys directly in the app sidebar at runtime. No file needed.

### 3. Run

```bash
streamlit run app.py
```

Opens at **http://localhost:8501**

---

## Hosting Options

### Streamlit Community Cloud (free, easiest)
1. Push this folder to a GitHub repo
2. Go to [share.streamlit.io](https://share.streamlit.io) → **New app**
3. Point to `app.py`
4. Add `ANTHROPIC_API_KEY` and `GOOGLE_API_KEY` in **Secrets** (Settings → Secrets):
   ```toml
   ANTHROPIC_API_KEY = "sk-ant-..."
   GOOGLE_API_KEY = "AIza..."
   ```

### Railway / Render / Fly.io
```bash
# Procfile content (Railway / Render)
web: streamlit run app.py --server.port $PORT --server.address 0.0.0.0
```
Set `ANTHROPIC_API_KEY` and `GOOGLE_API_KEY` as environment variables in your hosting dashboard.

### Docker
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8501
CMD ["streamlit", "run", "app.py", "--server.address", "0.0.0.0"]
```

```bash
docker build -t market-research .
docker run -p 8501:8501 \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  -e GOOGLE_API_KEY=AIza... \
  market-research
```

---

## Files

| File | Purpose |
|------|---------|
| `app.py` | Main Streamlit application (all pipeline logic + UI) |
| `requirements.txt` | Python dependencies |
| `.env.example` | Template for API keys |
| `.streamlit/config.toml` | UI theme + server config |

---

## Cost Per Report (~estimate)

| Step | Model | Est. Cost |
|------|-------|-----------|
| Research | Claude Opus + Gemini 1.5 Pro | ~$0.32 |
| Merge | Claude Haiku | ~$0.02 |
| Council × 3 | Claude Opus × 3 | ~$0.38 |
| Synthesis | Claude Opus | ~$0.26 |
| **Total** | | **~$0.98** |

> Tip: Disable one or two council agents in the sidebar to reduce cost.

---

## Notes

- **Google API key is optional** — if not provided, Gemini research is skipped and Claude output is used for both legs of the merge.
- All keys entered in the sidebar are stored only in Streamlit `session_state` and never logged or persisted.
