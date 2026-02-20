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
├── plugins/                # All plugins
│   ├── git/                # Git workflow automation
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── commands/
│   │       ├── git-commit.md
│   │       ├── github-pr.md
│   │       └── git-rebase-stack.md
│   └── plan/               # Project planning
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── commands/
│           ├── deep-interview.md
│           └── fill-linear-ticket.md
├── LICENSE
├── README.md
└── SPEC.md
```

## Plugins

### git

Git 워크플로우 자동화 플러그인. staged 변경사항의 diff를 분석하여 conventional commit 형식의 한국어 커밋 메시지를 자동 생성하고, PR 생성과 stacked PR rebase까지 처리한다. 커밋 분할 판단, BREAKING CHANGE 감지, PR 템플릿 탐색, 브랜치명에서 issue 번호 자동 추출 등을 지원한다.

| Command | Description |
|---------|-------------|
| `git-commit` | git diff 분석 후 conventional commit 형식의 한국어 커밋 메시지 자동 생성 및 커밋 실행. 논리적으로 분리되어야 하는 변경사항은 분할 커밋을 제안한다. |
| `github-pr` | 현재 브랜치의 커밋 히스토리와 diff를 분석하여 conventional commit 형식의 한국어 제목으로 GitHub PR을 자동 생성. `--draft` 지원, 미푸시 브랜치 자동 푸시. |
| `git-rebase-stack` | stacked PR 작업 중 `git rebase --onto --update-refs`를 활용한 스택 브랜치 자동 정리. 자연어로 의도를 전달하면 스택 topology를 분석하여 rebase를 실행한다. |

### plan

프로젝트 기획 지원 플러그인. AskUserQuestion을 활용한 심층 인터뷰를 통해 기술 구현, UI/UX, tradeoffs 등을 깊이 파고들어 스펙 문서를 자동 생성하거나, Linear 티켓의 누락된 정보를 채워준다.

| Command | Description |
|---------|-------------|
| `deep-interview` | 기술 구현, UI/UX, 우려사항, tradeoffs 등에 대해 심층 인터뷰를 진행한 후 스펙 문서를 자동 생성한다. |
| `fill-linear-ticket` | Linear 티켓 URL을 받아 title/description/comments/linked issue를 분석하고, 인터뷰를 통해 누락된 정보를 채운다. |

## Adding a Plugin

1. Create `plugins/<plugin-name>/` directory
2. Add `.claude-plugin/plugin.json` manifest
3. Add plugin source files (commands/, hooks/, agents/, etc.)
4. Add entry to `.claude-plugin/marketplace.json` `plugins` array
5. Validate with `claude plugin validate .` or `/plugin validate .`
6. Commit and push

## License

MIT
