# claude-code-skills

A collection of Claude Code skills for common development workflows.

## Skills

| Skill | Description |
|-------|-------------|
| **keybindings-help** | Customize keyboard shortcuts, rebind keys, add chord bindings, modify `~/.claude/keybindings.json` |
| **simplify** | Review changed code for reuse, quality, and efficiency, then fix any issues found |
| **claude-api** | Build apps with the Claude API or Anthropic SDK |
| **openclaw-whatsapp** | Set up WhatsApp channel on OpenClaw via built-in plugin and QR code linking |
| **openclaw-nvidia-api** | Configure NVIDIA NIM API models on OpenClaw, fix API format mismatch (OpenAI vs Anthropic) |
| **ollama-install** | Install and configure Ollama on Linux servers and local machines, manage models and API |

## Installation

```bash
claude plugin add ebaneban888/claude-code-skills
```

## Usage

These skills are automatically invoked by Claude based on context:

- **keybindings-help**: Triggered when you mention keyboard shortcuts, keybindings, or rebinding keys
- **simplify**: Triggered when reviewing recently modified code for quality improvements
- **claude-api**: Triggered when working with Claude API, Anthropic SDK, or Agent SDK
- **openclaw-whatsapp**: Triggered when setting up or troubleshooting WhatsApp on OpenClaw
- **openclaw-nvidia-api**: Triggered when configuring NVIDIA NIM API, troubleshooting 404 errors, or adding NVIDIA models to OpenClaw
- **ollama-install**: Triggered when asking to install Ollama, run LLM models, or configure Ollama API

## Project Structure

```
claude-code-skills/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── keybindings-help/
│   │   └── SKILL.md
│   ├── simplify/
│   │   └── SKILL.md
│   ├── claude-api/
│   │   └── SKILL.md
│   ├── openclaw-whatsapp/
│   │   └── SKILL.md
│   ├── openclaw-nvidia-api/
│   │   └── SKILL.md
│   └── ollama-install/
│       └── SKILL.md
└── README.md
```