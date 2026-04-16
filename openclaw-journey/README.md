# My OpenClaw Journey

A personal log of setting up OpenClaw — an AI assistant bot running on a Hostinger VPS with Telegram.

---

## What is OpenClaw?

OpenClaw is a self-hosted AI assistant that connects to chat platforms like Telegram. You run it on your own server and it uses AI model providers (like OpenRouter) to respond to messages.

---

## My Setup

| Item | Detail |
|------|--------|
| Server | Hostinger VPS |
| OS | Ubuntu (Linux) |
| Bot | @PhDaoeniialx_bot (Phoenix) |
| AI Provider | OpenRouter (free tier) |
| Platform | Telegram |

---

## Phase 1 — Installation

### Install OpenClaw
```bash
curl -fsSL --proto '=https' --tlsv1.2 https://openclaw.ai/install.sh | bash
```

### Fix PATH (required after install)
```bash
export PATH="/root/.local/bin:$PATH"
echo 'export PATH="/root/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

---

## Phase 2 — Initial Setup

### Run onboarding wizard
```bash
openclaw onboard --mode local
```

### Install and start gateway
```bash
openclaw gateway install
openclaw gateway
```

---

## Phase 3 — Telegram Bot Setup

1. Create a bot via [@BotFather](https://t.me/BotFather) on Telegram
2. Run `/newbot` and save the full token (format: `1234567890:AAxxxxxxxx`)
3. Add the token to OpenClaw config:

```bash
nano ~/.openclaw/openclaw.json
```

Find the `channels.telegram` section and set:
```json
"channels": {
  "telegram": {
    "enabled": true,
    "botToken": "YOUR_FULL_BOT_TOKEN"
  }
}
```

---

## Phase 4 — Connect OpenRouter API

### Run the configure wizard
```bash
openclaw configure --section model
```

- Select **OpenRouter (API key)**
- Paste your API key from [openrouter.ai](https://openrouter.ai)
- Select `openrouter/auto` as default model

---

## Phase 5 — Authorize Your Telegram Account

1. Send any message to your bot on Telegram
2. The bot will reply with a pairing code
3. On your server, approve yourself:

```bash
openclaw pairing approve telegram YOURCODE
```

---

## Phase 6 — Keep Bot Running 24/7

```bash
loginctl enable-linger root
systemctl --user enable openclaw-gateway.service
systemctl --user start openclaw-gateway.service
```

---

## Useful Commands

```bash
openclaw status                          # Check overall status
openclaw gateway                         # Start gateway manually
openclaw gateway stop                    # Stop gateway
openclaw logs --follow                   # Live logs
openclaw doctor                          # Diagnose issues
openclaw doctor --fix                    # Auto-fix issues
openclaw configure --section model       # Update API key or model
systemctl --user status openclaw-gateway.service  # Check service status
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `openclaw: command not found` | Run `export PATH="/root/.local/bin:$PATH"` |
| Telegram 404 error | Check bot token is full format `id:token` |
| HTTP 401 from OpenRouter | Re-run `openclaw configure --section model` |
| Bot not responding | Run `openclaw logs --follow` and check for errors |
| Gateway stops on reboot | Run Phase 6 commands above |
| `pairing required` error | Send message to bot, approve with pairing code |

---

## Lessons Learned

- The bot token must be the **full token** including the part after the colon (`8386869770:AAHvcxx...`), not just the numeric ID
- `openclaw config set` doesn't always work — editing `~/.openclaw/openclaw.json` directly or using the wizard is more reliable
- The `export OPENROUTER_API_KEY` command only lasts for the current terminal session — always use `openclaw configure` to save it permanently
- On Hostinger VPS, systemd user services need `loginctl enable-linger root` to survive reboots

---

## Next Steps

- [ ] Set up the web dashboard (SSH tunnel to `http://localhost:18789/`)
- [ ] Explore bot tools and commands
- [ ] Add memory/embedding provider for smarter responses
- [ ] Customize bot persona and behavior
