# Period Pallete ✦
### understand. care. thrive.

A personalised women's health companion built with Streamlit, Supabase, and NVIDIA AI.

---

## What's in the app

| Page | What it does |
|------|-------------|
| 🔐 **Auth** | Email/password sign-up with SMTP verification code, login, password reset |
| 📊 **Dashboard** | Cycle day, phase, next period date, recent symptoms summary |
| 🌙 **Cycle Tracker** | Interactive calendar, log period/ovulation/spotting, phase timeline |
| ✨ **Symptoms** | Tap-to-log symptoms, sickness, and mood; stored in Supabase |
| 💫 **Skin Care** | Step-by-step cycle-synced skincare routine, skin score tracker |
| 📝 **Notes** | Create/edit/delete personal notes with category tags |
| 📈 **Insights** | Charts: top symptoms, mood distribution, skin score trend |
| 🤖 **AI Chat** | NVIDIA LLaMA-3.1 Nemotron powered chat with full conversation history |
| ⚙️ **Settings** | Profile, cycle length, language, password change, logout |

---

## Quick start (local)

### 1 — Prerequisites
- Python 3.10+
- A free [Supabase](https://supabase.com) account
- A [NVIDIA NIM API key](https://build.nvidia.com) (free tier available)
- *(Optional)* Gmail account with App Password for email verification

### 2 — Clone and install
```bash
git clone <your-repo>
cd Period Pallete
pip install -r requirements.txt
```

### 3 — Set up Supabase

1. Go to [supabase.com](https://supabase.com) → **New Project**
2. Once created, go to **SQL Editor** → paste the entire contents of `supabase_schema.sql` → **Run**
3. Go to **Project Settings → API** → copy:
   - **Project URL**
   - **anon / public key**

### 4 — Configure environment
```bash
cp .env.example .env
```
Edit `.env`:
```
SUPABASE_URL=https://xxxxxxxxxxxx.supabase.co
SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

NVIDIA_API_KEY=nvapi-...

# Optional — skip for dev mode (code shown on screen instead)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=you@gmail.com
SMTP_PASSWORD=xxxx xxxx xxxx xxxx   # Gmail App Password
SMTP_FROM=Period Pallete <you@gmail.com>
```

> **Gmail App Password**: Go to myaccount.google.com → Security → 2-Step Verification → App Passwords → generate one for "Mail".

### 5 — Run
```bash
streamlit run main.py
```
Open http://localhost:8501

---

## Deploy to Streamlit Cloud (free)

1. Push your code to a **GitHub repo** (make sure `.env` is in `.gitignore`)
2. Go to [share.streamlit.io](https://share.streamlit.io) → **New app**
3. Set:
   - Repository: your repo
   - Branch: main
   - Main file: `main.py`
4. In **Advanced settings → Secrets**, paste the contents of `.streamlit/secrets.toml.example` with your real values
5. Click **Deploy** — done!

> The app will be live at `https://your-app.streamlit.app`

---

## Deploy to a VPS / server

```bash
# Install
pip install -r requirements.txt

# Run with nohup (stays alive after SSH disconnect)
nohup streamlit run main.py --server.port 8501 --server.headless true &

# Or with systemd (recommended for production)
# Create /etc/systemd/system/Period Pallete.service
```

For HTTPS, put Nginx in front:
```nginx
server {
    listen 443 ssl;
    server_name Period Pallete.yourdomain.com;
    location / {
        proxy_pass http://localhost:8501;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

---

## NVIDIA API

The app uses **nvidia/llama-3.1-nemotron-ultra-253b-v1** via NVIDIA's OpenAI-compatible endpoint.

- Get a free key at https://build.nvidia.com
- Free tier includes generous credits for testing
- To switch models, edit `get_ai_response()` in `utils/shared.py`

Other good NVIDIA models:
- `meta/llama-3.3-70b-instruct` — faster, smaller
- `mistralai/mistral-large-2-instruct` — great for multilingual

---

## Project structure

```
Period Pallete/
├── main.py                   # Entry point (routes to auth or dashboard)
├── requirements.txt
├── supabase_schema.sql       # Run once in Supabase SQL editor
├── .env.example              # Copy to .env, fill in keys
├── .streamlit/
│   ├── config.toml           # Theme & server config
│   └── secrets.toml.example  # For Streamlit Cloud deploy
├── utils/
│   └── shared.py             # Design system, CSS, DB helpers, AI helper
└── pages/
    ├── Auth.py               # Login / Sign-up / OTP verification
    ├── Dashboard.py
    ├── Cycle_Tracker.py
    ├── Symptom_Tracker.py
    ├── Skin_Care.py
    ├── Notes.py
    ├── Insights.py
    └── Settings.py
```

---

## Key design decisions

- **Supabase** over FastAPI: no separate server to run — Supabase's Python client connects directly from Streamlit. Built-in auth, PostgreSQL, and row-level security (each user only sees their own data).
- **NVIDIA NIM** over Anthropic: uses OpenAI-compatible client (`openai` library), just with a different `base_url`. Easy to swap models.
- **Single CSS file** in `utils/shared.py`: all styling in one place via `inject_global_css()`, no scattered inline styles.
- **Mobile-first**: min-height 44px touch targets, responsive grid, hidden footer quote on small screens.
