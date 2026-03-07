---
name: keybindings-help
description: Use when the user wants to customize keyboard shortcuts, rebind keys, add chord bindings, or modify ~/.claude/keybindings.json. Examples - "rebind ctrl+s", "add a chord shortcut", "change the submit key", "customize keybindings".
version: 1.0.0
---

# Keybindings Help

Help users customize Claude Code keyboard shortcuts by editing `~/.claude/keybindings.json`.

## When This Skill Applies

This skill activates when the user:
- Wants to customize keyboard shortcuts
- Asks to rebind keys or change key mappings
- Wants to add chord bindings (multi-key sequences)
- Mentions `keybindings.json` or keyboard configuration
- Asks about available keybinding actions

## Keybindings File Location

The keybindings configuration file is located at:
```
~/.claude/keybindings.json
```

## Configuration Format

The file uses a JSON array of keybinding objects:

```json
[
  {
    "key": "ctrl+s",
    "command": "submit",
    "when": "inputFocused"
  },
  {
    "key": "ctrl+k ctrl+c",
    "command": "toggleComment",
    "when": "editorFocused"
  }
]
```

### Keybinding Object Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `key` | string | Yes | Key combination (e.g., `ctrl+s`, `alt+enter`, `ctrl+k ctrl+d`) |
| `command` | string | Yes | The action to execute |
| `when` | string | No | Context condition for when the binding is active |

### Key Modifiers

- `ctrl` — Control key
- `alt` — Alt/Option key
- `shift` — Shift key
- `meta` — Windows/Command key

### Chord Bindings

Chord bindings are multi-key sequences separated by spaces:
```json
{
  "key": "ctrl+k ctrl+s",
  "command": "saveAll"
}
```
The user presses `ctrl+k` first, then `ctrl+s` to trigger the action.

## Workflow

1. **Read** the current `~/.claude/keybindings.json` file (create if it doesn't exist)
2. **Understand** what the user wants to change
3. **Modify** the keybindings configuration
4. **Write** the updated file back
5. **Inform** the user that changes will take effect on next Claude Code restart

## Best Practices

- Always read the existing file first to avoid overwriting user customizations
- Validate that key combinations don't conflict with system shortcuts
- Use chord bindings for less common actions to avoid conflicts
- Keep the JSON well-formatted and readable
- Warn users about potential conflicts with existing bindings
