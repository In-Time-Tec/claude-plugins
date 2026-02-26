# ITT Claude Plugins

Internal Claude Code plugin marketplace for In Time Tec.

## Quick Start

```shell
# 1. Add the marketplace (one-time setup)
/plugin marketplace add in-time-tec/claude-plugins

# 2. Browse available plugins
/plugin list @itt-plugins

# 3. Install a plugin
/plugin install <plugin-name>@itt-plugins
```

## Available Plugins

| Plugin | Description | Skills | Commands |
|--------|-------------|--------|----------|
| [itt-frontend](./plugins/itt-frontend/) | Brand guidelines, tech stack, and frontend tooling | `itt-brand`, `itt-stack` | `/itt-frontend:scaffold`, `/itt-frontend:review` |

## Auto-Install for Your Team

Add this to any project's `.claude/settings.json` to auto-prompt teammates:

```json
{
  "extraKnownMarketplaces": {
    "itt-plugins": {
      "source": {
        "source": "github",
        "repo": "in-time-tec/claude-plugins"
      }
    }
  },
  "enabledPlugins": {
    "itt-frontend@itt-plugins": true
  }
}
```

When a teammate opens the project in Claude Code, they'll be prompted to install the marketplace and enabled plugins.

## Contributing

### Adding a Plugin

1. Create the plugin directory:
   ```bash
   mkdir -p plugins/<name>/.claude-plugin
   ```

2. Write `plugins/<name>/.claude-plugin/plugin.json`:
   ```json
   {
     "name": "<name>",
     "version": "1.0.0",
     "description": "What this plugin does"
   }
   ```

3. Add skills (`skills/`), commands (`commands/`), agents (`agents/`), hooks (`hooks/`), or MCP servers (`.mcp.json`) at the plugin root.

4. Register it in `.claude-plugin/marketplace.json`:
   ```json
   {
     "name": "<name>",
     "description": "What this plugin does",
     "source": "./plugins/<name>"
   }
   ```

5. Test locally:
   ```bash
   claude --plugin-dir ./plugins/<name>
   ```

6. Validate:
   ```bash
   claude plugin validate .
   ```

### Key Rules

- Only `plugin.json` goes inside `.claude-plugin/`. Everything else goes at the plugin root.
- Use `${CLAUDE_PLUGIN_ROOT}` in hook commands and MCP configs — plugins are copied on install, so absolute paths break.
- Bump the version in `plugin.json` whenever you change a plugin.
- See [CLAUDE.md](./CLAUDE.md) for full development guide.

## Official Docs

- [Create plugins](https://code.claude.com/docs/en/plugins) — tutorials and practical usage for building plugins
- [Plugins reference](https://code.claude.com/docs/en/plugins-reference) — complete technical specs, schemas, CLI commands, and debugging
