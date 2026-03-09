---
name: openclaw-nvidia-api
description: Configure NVIDIA NIM API models on OpenClaw. TRIGGER when user asks to add NVIDIA models, configure NVIDIA NIM API, troubleshoot NVIDIA 404 errors on OpenClaw, or mentions "openclaw nvidia", "integrate.api.nvidia.com", or NVIDIA NIM model setup.
version: 1.0.0
tools: Read, Bash, WebFetch, WebSearch
---

# OpenClaw NVIDIA NIM API Configuration

Help users correctly configure NVIDIA NIM API models on OpenClaw, avoiding the common Anthropic/OpenAI API format mismatch.

## When This Skill Applies

**TRIGGER when:**
- User asks to add NVIDIA NIM models to OpenClaw
- User gets HTTP 404 errors with NVIDIA API on OpenClaw
- User mentions `integrate.api.nvidia.com` or NVIDIA NIM
- User wants to use models like `moonshotai/kimi-k2.5` via NVIDIA

**DO NOT TRIGGER when:**
- User configures NVIDIA GPU drivers or CUDA
- User asks about NVIDIA hardware unrelated to API
- User uses other providers (dashscope, OpenRouter, etc.)

## Critical Knowledge: API Format Mismatch

NVIDIA NIM API (`integrate.api.nvidia.com`) uses **OpenAI-compatible format** (`/v1/chat/completions`), **NOT** Anthropic Messages format (`/v1/messages`).

| Endpoint | Format | NVIDIA Support |
|----------|--------|---------------|
| `/v1/chat/completions` | OpenAI Completions | Supported |
| `/v1/messages` | Anthropic Messages | **NOT supported (404)** |

**This is the #1 cause of NVIDIA 404 errors on OpenClaw.**

### OpenClaw Supported API Formats

| API Value | Protocol | Use For |
|-----------|----------|---------|
| `openai-completions` | OpenAI Chat Completions | NVIDIA NIM, vLLM, Ollama, most third-party providers |
| `openai-responses` | OpenAI Responses API | OpenAI native |
| `anthropic-messages` | Anthropic Messages API | Anthropic, DashScope Anthropic-compatible endpoints |
| `google-generative-ai` | Google Generative AI | Google Gemini |

## Correct Configuration

### Provider Config (in `~/.openclaw/openclaw.json`)

```json
{
  "models": {
    "providers": {
      "nvidia": {
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "apiKey": "nvapi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "api": "openai-completions",
        "models": [
          {
            "id": "moonshotai/kimi-k2.5",
            "name": "Kimi K2.5 (NVIDIA)",
            "contextWindow": 200000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

Key points:
- `api` MUST be `"openai-completions"` (NOT `"anthropic-messages"`)
- `baseUrl` should include `/v1`
- `apiKey` starts with `nvapi-`

### Step-by-Step Setup

#### Step 1: Add NVIDIA Provider via jq

```bash
jq '.models.providers.nvidia = {
  "baseUrl": "https://integrate.api.nvidia.com/v1",
  "apiKey": "nvapi-YOUR_API_KEY_HERE",
  "api": "openai-completions",
  "models": [
    {"id": "moonshotai/kimi-k2.5", "name": "Kimi K2.5 (NVIDIA)", "contextWindow": 200000, "maxTokens": 8192}
  ]
}' ~/.openclaw/openclaw.json > /tmp/openclaw_tmp.json && mv /tmp/openclaw_tmp.json ~/.openclaw/openclaw.json
```

#### Step 2: Set as Default Model

```bash
openclaw models set nvidia/moonshotai/kimi-k2.5
```

#### Step 3: Restart Gateway

```bash
openclaw gateway restart
```

#### Step 4: Verify

```bash
openclaw models list --all --provider nvidia
# Expected: nvidia/moonshotai/kimi-k2.5  text  195k  no  yes  default,configured
```

### Quick Test (curl)

```bash
# OpenAI format (should return 200)
curl -s -o /dev/null -w "%{http_code}" \
  https://integrate.api.nvidia.com/v1/chat/completions \
  -H "Authorization: Bearer nvapi-YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"moonshotai/kimi-k2.5","max_tokens":100,"messages":[{"role":"user","content":"hi"}]}'

# Anthropic format (returns 404 - DO NOT USE)
curl -s -o /dev/null -w "%{http_code}" \
  https://integrate.api.nvidia.com/v1/messages \
  -H "x-api-key: nvapi-YOUR_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"model":"moonshotai/kimi-k2.5","max_tokens":100,"messages":[{"role":"user","content":"hi"}]}'
```

## Troubleshooting

### HTTP 404: 404 page not found

**Cause:** `api` is set to `"anthropic-messages"` but NVIDIA only supports OpenAI format.

**Fix:**
```bash
jq '.models.providers.nvidia.api = "openai-completions"' \
  ~/.openclaw/openclaw.json > /tmp/oc_tmp.json && mv /tmp/oc_tmp.json ~/.openclaw/openclaw.json
openclaw gateway restart
```

### Model outputs raw tool_call XML to users

**Cause:** Some models (e.g., GLM-5) do not properly support OpenClaw's tool use protocol.

**Fix:** Switch to a model that supports tool use properly:
```bash
openclaw models set dashscope/kimi-k2.5
openclaw models set nvidia/moonshotai/kimi-k2.5
```

### Authentication error / 401

**Fix:** Get a new key from https://build.nvidia.com/ and update:
```bash
jq '.models.providers.nvidia.apiKey = "nvapi-NEW_KEY_HERE"' \
  ~/.openclaw/openclaw.json > /tmp/oc_tmp.json && mv /tmp/oc_tmp.json ~/.openclaw/openclaw.json
openclaw gateway restart
```

### baseUrl with or without /v1

**Always include `/v1` in the baseUrl.** NVIDIA NIM requires it.

## Available NVIDIA NIM Models

| Model ID | Description |
|----------|-------------|
| `moonshotai/kimi-k2.5` | Kimi K2.5 by Moonshot AI, 200k context |
| `deepseek-ai/deepseek-r1` | DeepSeek R1 reasoning model |
| `meta/llama-3.3-70b-instruct` | Meta Llama 3.3 70B |
| `google/gemma-2-27b-it` | Google Gemma 2 27B |

## Key Commands Reference

| Command | Description |
|---------|-------------|
| `openclaw models list --all --provider nvidia` | List NVIDIA models |
| `openclaw models set nvidia/<model-id>` | Set NVIDIA model as default |
| `openclaw models status` | Show current model config |
| `openclaw gateway restart` | Restart gateway after config change |
| `openclaw health` | Check overall system health |

## Workflow

1. **Confirm API key**: Ensure user has `nvapi-` prefixed key from https://build.nvidia.com/
2. **Set correct API format**: MUST use `"openai-completions"`, never `"anthropic-messages"`
3. **Set baseUrl**: `https://integrate.api.nvidia.com/v1` (with `/v1`)
4. **Add provider config**: Use jq to edit `~/.openclaw/openclaw.json`
5. **Set default model**: `openclaw models set nvidia/<model-id>`
6. **Restart gateway**: `openclaw gateway restart`
7. **Verify**: `openclaw models list --all --provider nvidia`
8. **Test**: Send a message via QQ/WhatsApp to confirm response
