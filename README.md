# flex-haegul-plugins

Personal Claude Code plugin marketplace.

## Usage

### Add marketplace

```shell
/plugin marketplace add flex-haegul/claude-code-marketplace
```

### Install a plugin

```shell
/plugin install <plugin-name>@flex-haegul-plugins
```

### Update marketplace

```shell
/plugin marketplace update
```

## Structure

```
.
├── .claude-plugin/
│   └── marketplace.json    # Marketplace catalog
├── plugins/                # All plugins (organized by category)
│   └── <category>/
│       └── <plugin-name>/
├── LICENSE
└── README.md
```

## Categories

| Category | Directory | Description |
|----------|-----------|-------------|
| Code Quality | `code-quality` | Code review, linting, formatting, security checks |
| Workflow | `workflow` | Git automation, CI/CD, deployment, project management |
| AI Tools | `ai-tools` | Custom prompts, AI agents, special-purpose skills |
| DevOps | `devops` | Infrastructure, monitoring, containers |
| Security | `security` | Security scanning, vulnerability analysis |
| Data | `data` | Data processing, analysis, visualization |

## Adding a Plugin

1. Create `plugins/<category>/<plugin-name>/` directory
2. Add `.claude-plugin/plugin.json` manifest
3. Add plugin source files (skills/, hooks/, agents/, etc.)
4. Add entry to `.claude-plugin/marketplace.json` `plugins` array
5. Validate with `claude plugin validate .` or `/plugin validate .`
6. Commit and push

## License

MIT
