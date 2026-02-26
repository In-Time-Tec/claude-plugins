# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

An internal Claude Code plugin marketplace for In Time Tec. Teams add this marketplace with:

```shell
/plugin marketplace add in-time-tec/claude-plugins
```

Then install individual plugins:

```shell
/plugin install <plugin-name>@itt-plugins
```

## How It Works

This repo is a marketplace — a catalog of plugins defined in `.claude-plugin/marketplace.json`. Plugins can live directly in this repo (under `plugins/`) or reference external repos via GitHub/npm/git sources.

The marketplace name is `itt-plugins`. Plugin skills are namespaced: a plugin named `review-tools` with a skill `review` becomes `/review-tools:review`.

## Repository Structure

```
.claude-plugin/
  marketplace.json        # The marketplace catalog — lists all available plugins
plugins/
  <plugin-name>/          # Each plugin is a self-contained directory
    .claude-plugin/
      plugin.json         # Plugin manifest (name, version, description)
    skills/               # Slash commands and agent skills (SKILL.md files)
    agents/               # Subagent definitions (.md files)
    hooks/                # Event handlers (hooks.json)
    commands/             # Legacy commands (.md files)
    .mcp.json             # MCP server configs (optional)
    .lsp.json             # LSP server configs (optional)
    settings.json         # Default settings applied when plugin is enabled
    scripts/              # Supporting scripts for hooks/MCP servers
```

## Key Rules

- **Only `plugin.json` goes inside `.claude-plugin/`.** Skills, agents, hooks, commands — all at the plugin root.
- **Use `${CLAUDE_PLUGIN_ROOT}`** in hook commands and MCP server configs. Plugins are copied to `~/.claude/plugins/cache` on install, so absolute paths break.
- **No path traversal.** Plugins can't reference files outside their directory (`../` won't work after install). Use symlinks if sharing files between plugins.
- **Bump the version** in `plugin.json` whenever you change a plugin. Claude Code uses the version to detect updates — same version = no update for users.
- **Relative source paths** in `marketplace.json` must start with `./` and only work for git-based marketplace distribution.

## Adding a New Plugin

1. Create the plugin directory and manifest:
   ```bash
   mkdir -p plugins/<name>/.claude-plugin
   ```
2. Write `plugins/<name>/.claude-plugin/plugin.json` with at minimum `name` and `version`.
3. Add skills, agents, hooks, or MCP servers at the plugin root.
4. Add the plugin entry to `.claude-plugin/marketplace.json` in the `plugins` array.
5. Test locally:
   ```bash
   claude --plugin-dir ./plugins/<name>
   ```
6. Validate:
   ```bash
   claude plugin validate .
   ```

## Testing

```bash
# Test a single plugin in isolation
claude --plugin-dir ./plugins/<plugin-name>

# Test multiple plugins
claude --plugin-dir ./plugins/one --plugin-dir ./plugins/two

# Validate the entire marketplace
claude plugin validate .

# Test the full marketplace flow locally
# (from within Claude Code)
/plugin marketplace add ./path/to/this/repo
/plugin install <plugin-name>@itt-plugins
```

## Plugin Sources in marketplace.json

Plugins can come from multiple source types:

| Source | Format | When to use |
|--------|--------|-------------|
| Relative path | `"./plugins/name"` | Plugin lives in this repo |
| GitHub | `{"source": "github", "repo": "owner/repo"}` | Plugin in a separate GitHub repo |
| npm | `{"source": "npm", "package": "@scope/pkg"}` | Plugin published to npm |
| Git URL | `{"source": "url", "url": "https://...git"}` | GitLab, Bitbucket, self-hosted |

## Team Distribution

To auto-prompt teammates to install this marketplace, add to any project's `.claude/settings.json`:

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
    "some-plugin@itt-plugins": true
  }
}
```
