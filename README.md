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

## Plugins

- [git](plugins/git/README.md) - Git workflow automation (commit, PR, stacked PR rebase)
- [plan](plugins/plan/README.md) - Project planning support (deep interview, Linear ticket)

See [SPEC.md](SPEC.md) for directory structure and design decisions.

## Adding a Plugin

1. Create `plugins/<plugin-name>/` directory
2. Add `.claude-plugin/plugin.json` manifest
3. Add plugin source files (commands/, hooks/, agents/, etc.)
4. Add entry to `.claude-plugin/marketplace.json` `plugins` array
5. Validate with `claude plugin validate .` or `/plugin validate .`
6. Commit and push

## License

MIT
