---
name: claude-api
description: Build apps with the Claude API or Anthropic SDK. TRIGGER when code imports `anthropic`, `@anthropic-ai/sdk`, or `claude_agent_sdk`, or user asks to use Claude API, Anthropic SDKs, or Agent SDK. DO NOT TRIGGER when code imports `openai` or other AI SDK, general programming, or ML/data-science tasks.
version: 1.0.0
tools: Read, Glob, Grep, Bash, WebFetch, WebSearch
---

# Claude API

Help users build applications with the Claude API, Anthropic SDK, and Agent SDK.

## When This Skill Applies

**TRIGGER when:**
- Code imports `anthropic`, `@anthropic-ai/sdk`, or `claude_agent_sdk`
- User asks to use Claude API, Anthropic SDKs, or Agent SDK
- User wants to build AI-powered applications using Claude

**DO NOT TRIGGER when:**
- Code imports `openai` or other AI SDKs
- General programming tasks unrelated to Claude/Anthropic
- ML/data-science tasks

## SDK Installation

### Python
```bash
pip install anthropic
```

### TypeScript/JavaScript
```bash
npm install @anthropic-ai/sdk
```

### Agent SDK (Python)
```bash
pip install claude-agent-sdk
```

## Quick Start Examples

### Python — Basic Message

```python
import anthropic

client = anthropic.Anthropic()  # uses ANTHROPIC_API_KEY env var

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Hello, Claude!"}
    ]
)
print(message.content[0].text)
```

### TypeScript — Basic Message

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();  // uses ANTHROPIC_API_KEY env var

const message = await client.messages.create({
    model: "claude-sonnet-4-20250514",
    max_tokens: 1024,
    messages: [
        { role: "user", content: "Hello, Claude!" }
    ],
});
console.log(message.content[0].text);
```

### Python — Streaming

```python
import anthropic

client = anthropic.Anthropic()

with client.messages.stream(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Tell me a story"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

## Key API Features

### Tool Use (Function Calling)

```python
import anthropic

client = anthropic.Anthropic()

tools = [
    {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City and state, e.g. San Francisco, CA"
                }
            },
            "required": ["location"]
        }
    }
]

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}]
)
```

### Vision (Image Input)

```python
import anthropic
import base64

client = anthropic.Anthropic()

with open("image.png", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {
                "type": "image",
                "source": {
                    "type": "base64",
                    "media_type": "image/png",
                    "data": image_data,
                },
            },
            {
                "type": "text",
                "text": "Describe this image."
            }
        ],
    }],
)
```

### System Prompts

```python
message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    system="You are a helpful coding assistant.",
    messages=[{"role": "user", "content": "Write a Python function"}]
)
```

## Available Models

| Model | ID | Best For |
|-------|----|----------|
| Claude Opus 4.6 | `claude-opus-4-6` | Complex tasks, deep reasoning |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | Balanced performance and speed |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | Fast, cost-effective tasks |

## Best Practices

1. **Always use environment variables** for API keys (`ANTHROPIC_API_KEY`)
2. **Handle rate limits** with exponential backoff
3. **Use streaming** for long responses to improve UX
4. **Set appropriate max_tokens** to control costs
5. **Use the latest model IDs** for best performance
6. **Leverage tool use** for structured outputs and external integrations

## Workflow

When helping users build Claude API applications:

1. **Detect context**: Check imports and existing code for SDK usage
2. **Use latest docs**: Fetch up-to-date documentation when needed
3. **Follow SDK patterns**: Use idiomatic SDK patterns, not raw HTTP
4. **Include error handling**: Add proper error handling for API calls
5. **Test iteratively**: Help users test and debug their API integrations
