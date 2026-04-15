# VFS Global Login Automation

Playwright-based login automation script with Cloudflare-aware stealth techniques.  
Supports **headed** and **headless** modes. Credentials via environment variables.

---

## Setup

```bash
# 1. Install dependencies
pip install -r requirements.txt
playwright install chromium

# 2. Configure credentials
cp .env.example .env
# Edit .env — add your VFS_USERNAME and VFS_PASSWORD

# 3. (Optional) Edit config.json to adjust timeouts, viewport, etc.
```

---

## Running the Script

### Headed mode (browser visible — recommended for demo & CAPTCHA solving)
```bash
python login.py
```

### Headless mode (browser runs in background)
```bash
python login.py --headless
```

### Custom config file
```bash
python login.py --config custom.json
```

---

## Output

| Output | Location |
|---|---|
| Screenshots | `screenshots/` |
| Session logs | `logs/` |

Screenshots are auto-named with timestamps:
- `login_success_YYYYMMDD_HHMMSS.png`
- `login_failed_YYYYMMDD_HHMMSS.png`
- `login_unknown_YYYYMMDD_HHMMSS.png`
- `timeout_error_YYYYMMDD_HHMMSS.png`

---

## Cloudflare & CAPTCHA — Design Decision

VFS Global uses **Cloudflare Turnstile** bot protection.  
The script handles this with:

1. **Stealth context** — patches `navigator.webdriver`, mimics real browser headers/plugins/languages
2. **Warm-up visit** — visits homepage before login page (like a real user)
3. **`slow_mo`** — adds natural pacing between actions
4. **Manual CAPTCHA pause** — the script pauses and prompts you to solve the CAPTCHA, then continues automatically

> Programmatic CAPTCHA bypass is against VFS Global's Terms of Service.  
> The manual pause is the correct, ethical approach for a demo.

---

## Architecture

```
login.py
├── load_config()       → Reads config.json (settings only, never credentials)
├── load_credentials()  → Reads from .env / environment variables ONLY
├── create_stealth_context() → Stealth browser that avoids bot detection signals
├── run_login()         → Main flow: navigate → fill → CAPTCHA pause → detect result
└── main()              → CLI entry point (argparse)
```

---

## Demo Video Script (2–3 min)

1. Show `.env.example` — explain credentials never hardcoded
2. Show `config.json` — explain configurable settings
3. Run `python login.py` — walk through headed execution
4. Show CAPTCHA pause — explain *why* manual is the right choice
5. Show screenshot saved in `screenshots/`
6. Run `python login.py --headless` — explain headless use case (CI/CD pipelines)
7. Show `logs/` directory — timestamped session logs
