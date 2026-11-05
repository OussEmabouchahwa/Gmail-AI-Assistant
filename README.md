# 📬 Gmail AI Assistant

A serverless AI agent that reads your Gmail, uses **Gemini** to pick out the emails that actually matter, and sends you a smart digest on **Telegram**, in Tunisian Derja (Arabizi) 🇹🇳.

Built with **Google Apps Script**. No server, no hosting cost.

## ✨ Features

- 🧠 **AI triage**: classifies emails as `urgent`, `high` or `normal` and ignores newsletters and promotions
- 📝 **Summaries** with deadlines, dates and recommended actions
- ☀️ **Daily digest** every morning (7 AM, Tunisia time)
- 💬 **Telegram commands** for on-demand reports
- 📥 **Multi-account**: forward your other Gmail accounts into one inbox
- 🛡️ **Prompt-injection protection**: email content is treated as untrusted data
- 🔁 **Resilient**: falls back to a plain list if Gemini fails, skips poison messages, no duplicate processing

## 🏗️ Architecture

```
Gmail 1 ─┐
Gmail 2 ─┼─ forwarding ─▶ Main Gmail + Apps Script ─▶ Gemini ─▶ Telegram
Gmail 3 ─┘                  (search, collect)         (triage)   (reports, commands)
```

Telegram is handled with **polling** (a trigger asks Telegram for new messages every minute) instead of a webhook, because Apps Script web apps answer with a redirect that Telegram webhooks handle poorly.

## 🤖 Commands

| Command | Description |
|---|---|
| `/emails` | Important unread emails |
| `/important` | Important emails from the last 7 days |
| `/today` | Important emails from the last 24 hours |
| `/digest` | Full report of the last 24 hours |
| `/search keyword` | Search the last 30 days |
| `/help` | List commands |

## 🚀 Setup

### 1. Prerequisites
- A Gmail account
- A Telegram bot token from [@BotFather](https://t.me/BotFather)
- Your Telegram chat ID (for example from [@userinfobot](https://t.me/userinfobot))
- A Gemini API key from [Google AI Studio](https://aistudio.google.com/)

### 2. Create the project
1. Go to [script.google.com](https://script.google.com) and create a new project.
2. Paste the content of `Code.gs`.
3. (Optional) In *Project Settings*, enable "Show appsscript.json" and use the provided manifest.

### 3. Add secrets
*Project Settings → Script Properties*:

| Property | Value |
|---|---|
| `GEMINI_API_KEY` | your Gemini key |
| `TELEGRAM_TOKEN` | your bot token |
| `TELEGRAM_CHAT_ID` | your chat ID |

> ⚠️ Never put keys in the code or commit them to Git.

### 4. Test, then activate
Run these in order from the Apps Script editor:

1. `testGmail()`: reads your inbox
2. `testGemini()`: checks the AI classification
3. `testTelegram()`: sends a test message
4. `setupPolling()`: installs the triggers (run **once**)
5. `checkSystem()`: verifies configuration and triggers

Then send `/start` to your bot.

### 5. Multiple Gmail accounts (optional)
In each secondary account: *Settings → Forwarding and POP/IMAP → Add a forwarding address* → your main Gmail, confirm the code, and enable forwarding.

Notes: only new emails are forwarded, and the original recipient is usually visible in the `To` field of the report.

## ⚙️ Configuration

Edit the `CONFIG` object at the top of `Code.gs`: number of emails, Gemini model, timezone, digest hour.

## 🔒 Security

- The bot only answers your own `TELEGRAM_CHAT_ID`.
- The Gmail scope is **read-only**. The script never sends, deletes or modifies emails.
- Email content is sent to the Gemini API for analysis. Do not use this with data you are not allowed to share with a third-party AI provider.

## ⚠️ Limitations

- Polling means replies arrive within about a minute, not instantly.
- AI classification can be wrong. Don't rely on it alone for critical emails.
- Apps Script has daily quotas (trigger runtime, URL fetch calls).

## 🗺️ Roadmap

- [ ] Natural-language questions ("what did HR send me this week?")
- [ ] Draft replies
- [ ] Calendar events from detected appointments

## 📄 License

MIT
