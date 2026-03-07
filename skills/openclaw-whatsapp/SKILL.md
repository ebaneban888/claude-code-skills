---
name: openclaw-whatsapp
description: Set up WhatsApp channel on OpenClaw. TRIGGER when user asks to install, configure, or troubleshoot WhatsApp on OpenClaw, or mentions "openclaw whatsapp", "openclaw channels whatsapp", or WhatsApp QR link setup.
version: 1.0.0
tools: Read, Bash, WebFetch, WebSearch
---

# OpenClaw WhatsApp Channel Setup

Help users install and configure the WhatsApp channel on OpenClaw (2026.3.x) via the built-in stock plugin and QR code linking.

## When This Skill Applies

**TRIGGER when:**
- User asks to set up WhatsApp on OpenClaw
- User mentions OpenClaw WhatsApp channel, QR link, or Baileys
- User encounters errors with `openclaw channels` related to WhatsApp
- User wants to link WhatsApp to an AI assistant on a server

**DO NOT TRIGGER when:**
- User asks about WhatsApp Cloud API (Meta Business Platform)
- User asks about WhatsApp unrelated to OpenClaw
- General messaging or chatbot tasks not involving OpenClaw

## Prerequisites

- OpenClaw 2026.3.x installed on a Linux server
- SSH access to the server
- A phone with WhatsApp installed (use a secondary number to avoid ban risk)

## Step-by-Step Setup

### Step 1: Enable the Built-in WhatsApp Plugin

OpenClaw ships with a stock WhatsApp plugin (`@openclaw/whatsapp`) that is **disabled by default**. Do NOT install `whatsapp-baileys` from npm — it lacks the required `openclaw.extensions` field and will fail.

Enable the stock plugin via config:

```bash
openclaw config set plugins.entries.whatsapp.enabled true
```

Verify the plugin is loaded:

```bash
openclaw plugins list | grep whatsapp
# Expected: Status = "loaded"
```

### Step 2: Add the WhatsApp Channel

```bash
openclaw channels add --channel whatsapp
# Output: Added WhatsApp account "default".
```

Optional — allow group messages (default policy is `allowlist` with an empty list, which drops all group messages):

```bash
openclaw config set channels.whatsapp.groupPolicy open
```

### Step 3: Install and Start the Gateway

Install as a systemd service (recommended for persistence):

```bash
openclaw gateway install
```

Start the gateway:

```bash
# Via systemd (if installed)
export XDG_RUNTIME_DIR=/run/user/$(id -u)
systemctl --user start openclaw-gateway.service

# Or manually in foreground
openclaw gateway start --foreground

# Or use screen/tmux for persistence
screen -S openclaw
openclaw gateway start --foreground
# Ctrl+A D to detach
```

Verify the gateway is running:

```bash
systemctl --user status openclaw-gateway.service
# Or
openclaw channels status
```

### Step 4: Link WhatsApp via QR Code

**This step must be done interactively** — the QR code is displayed in the terminal and must be scanned with a phone.

```bash
openclaw channels login --channel whatsapp
```

On the phone:
1. Open **WhatsApp** → **Settings** → **Linked Devices** → **Link a Device**
2. Scan the QR code displayed in the terminal
3. Wait for "Linked successfully" confirmation

### Step 5: Verify

```bash
openclaw channels status
# Expected: WhatsApp default: enabled, configured, linked, running, connected
```

## Troubleshooting

### `Unknown channel: whatsapp`

The WhatsApp plugin is not enabled. Run:

```bash
openclaw config set plugins.entries.whatsapp.enabled true
```

Then restart the gateway.

### `whatsapp-baileys` install fails with `missing openclaw.extensions`

Do NOT use the external npm package. Use the built-in stock plugin instead (Step 1 above).

### QR code not displaying or garbled

- Use a web-based terminal (e.g., Alibaba Cloud ECS web console) for clearer QR rendering
- Increase terminal font size and window width
- Ensure terminal supports UTF-8

### WhatsApp linked but not responding

```bash
# Check logs
openclaw channels logs --tail 50

# Remove old linked devices on phone, then re-link
openclaw channels logout --channel whatsapp
openclaw channels login --channel whatsapp
```

### Group messages silently dropped

Default `groupPolicy` is `allowlist` with an empty list. Fix:

```bash
openclaw config set channels.whatsapp.groupPolicy open
```

### Gateway stops after SSH disconnect

Install as systemd service:

```bash
openclaw gateway install
export XDG_RUNTIME_DIR=/run/user/$(id -u)
systemctl --user start openclaw-gateway.service
systemctl --user enable openclaw-gateway.service
```

Or use `screen`/`tmux`.

### Ban risk warning

QR-based linking uses an unofficial reverse-engineered WhatsApp Web protocol (Baileys). Use a **secondary phone number** (eSIM) to avoid risking your primary account. For zero-risk integration, use the official WhatsApp Cloud API via Meta Business Platform (requires developer account and paid verification).

## Key Commands Reference

| Command | Description |
|---------|-------------|
| `openclaw config set plugins.entries.whatsapp.enabled true` | Enable WhatsApp plugin |
| `openclaw plugins list` | List all plugins and their status |
| `openclaw channels add --channel whatsapp` | Add WhatsApp channel account |
| `openclaw channels login --channel whatsapp` | Link WhatsApp via QR code |
| `openclaw channels status` | Check channel connection status |
| `openclaw channels logout --channel whatsapp` | Unlink WhatsApp session |
| `openclaw channels logs --tail 50` | View recent channel logs |
| `openclaw gateway install` | Install gateway as systemd service |
| `openclaw gateway restart` | Restart the gateway |
| `openclaw config set channels.whatsapp.groupPolicy open` | Allow group messages |

## Workflow

When helping users set up WhatsApp on OpenClaw:

1. **Check OpenClaw version**: Ensure 2026.3.x is installed (`openclaw --version`)
2. **Enable plugin**: Set `plugins.entries.whatsapp.enabled` to `true` in config
3. **Verify plugin loaded**: Check `openclaw plugins list` shows `loaded`
4. **Add channel**: Run `openclaw channels add --channel whatsapp`
5. **Start gateway**: Install and start via systemd or foreground
6. **Guide QR login**: Instruct user to run `openclaw channels login --channel whatsapp` interactively
7. **Verify connection**: Check `openclaw channels status` shows `connected`
8. **Configure policies**: Set group policy and allowlists as needed
