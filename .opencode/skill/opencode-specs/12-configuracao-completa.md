# Configuracao Completa do OpenCode

## Formato do Arquivo

O OpenCode suporta **JSON** e **JSONC** (JSON com comentarios).

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  // Comentarios sao permitidos em JSONC
  "theme": "opencode",
  "model": "anthropic/claude-sonnet-4-5"
}
```

## Locais de Configuracao

| Prioridade | Local | Uso |
|------------|-------|-----|
| 1 | `~/.config/opencode/opencode.json` | Global (themes, providers, keybinds) |
| 2 | `./opencode.json` | Projeto (modes, rules especificos) |
| 3 | `$OPENCODE_CONFIG` | Custom path (override) |
| 4 | `$OPENCODE_CONFIG_DIR` | Custom directory |

**Nota**: Configs sao **mesclados**, nao substituidos.

## Schema Oficial

```
https://opencode.ai/config.json
```

## Opcoes de Configuracao

### TUI
```json
{
  "tui": {
    "scroll_speed": 3,
    "scroll_acceleration": { "enabled": true },
    "diff_style": "auto"
  }
}
```

### Server
```json
{
  "server": {
    "port": 4096,
    "hostname": "0.0.0.0",
    "mdns": true,
    "cors": ["http://localhost:5173"]
  }
}
```

### Tools
```json
{
  "tools": {
    "write": true,
    "bash": true,
    "edit": false
  }
}
```

### Models
```json
{
  "provider": {
    "anthropic": {
      "options": {
        "timeout": 600000,
        "setCacheKey": true
      }
    }
  },
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5"
}
```

### Themes
```json
{
  "theme": "opencode"
}
```

### Agents
```json
{
  "agent": {
    "code-reviewer": {
      "description": "Reviews code",
      "model": "anthropic/claude-sonnet-4-5",
      "tools": { "write": false }
    }
  },
  "default_agent": "build"
}
```

### Sharing
```json
{
  "share": "manual"
}
```
Valores: `"manual"`, `"auto"`, `"disabled"`

### Commands
```json
{
  "command": {
    "test": {
      "template": "Run the full test suite...",
      "description": "Run tests with coverage",
      "agent": "build"
    }
  }
}
```

### Keybinds
```json
{
  "keybinds": {}
}
```

### Autoupdate
```json
{
  "autoupdate": true
}
```
Valores: `true`, `false`, `"notify"`

### Formatters
```json
{
  "formatter": {
    "prettier": { "disabled": true },
    "custom": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "extensions": [".js", ".ts"]
    }
  }
}
```

### Permissions
```json
{
  "permission": {
    "edit": "ask",
    "bash": "ask",
    "webfetch": "allow"
  }
}
```

### Compaction
```json
{
  "compaction": {
    "auto": true,
    "prune": true
  }
}
```

### Watcher
```json
{
  "watcher": {
    "ignore": ["node_modules/**", "dist/**"]
  }
}
```

### MCP Servers
```json
{
  "mcp": {}
}
```

### Plugins
```json
{
  "plugin": ["opencode-helicone-session"]
}
```

### Instructions
```json
{
  "instructions": ["CONTRIBUTING.md", "docs/*.md"]
}
```

### Providers
```json
{
  "disabled_providers": ["openai"],
  "enabled_providers": ["anthropic"]
}
```

## Variaveis

### Variaveis de Ambiente
```json
{
  "model": "{env:OPENCODE_MODEL}"
}
```

### Arquivos
```json
{
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{file:~/.secrets/openai-key}"
      }
    }
  }
}
```

## Exemplo Completo

```json
{
  "$schema": "https://opencode.ai/config.json",
  "theme": "opencode",
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5",
  "autoupdate": true,
  "share": "manual",
  "default_agent": "build",
  "permission": {
    "edit": "allow",
    "bash": "ask"
  },
  "tools": {
    "write": true,
    "edit": true,
    "bash": true
  },
  "agent": {
    "review": {
      "description": "Code reviewer",
      "mode": "subagent",
      "tools": { "write": false }
    }
  },
  "instructions": ["AGENTS.md"]
}
```
