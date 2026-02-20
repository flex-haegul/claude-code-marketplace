# Claude Code Marketplace Spec

## 프로젝트 개요

**프로젝트명**: claude-code-marketplace
**마켓플레이스 이름**: `flex-haegul-plugins`
**저장소**: `flex-haegul/claude-code-marketplace` (Public)
**라이선스**: MIT

Claude Code 플러그인 마켓플레이스를 위한 정적 GitHub 저장소.
개인 용도로 시작하되, 추후 팀이나 커뮤니티에 점진적으로 공유 가능한 구조를 유지한다.

## 핵심 결정사항

| 항목 | 결정 | 근거 |
|------|------|------|
| 프로젝트 형태 | 정적 저장소 (marketplace.json + 플러그인 파일) | 개인 용도에 적합한 최소한의 구조 |
| 소스 관리 | 모노레포 (모든 플러그인을 이 저장소에 포함) | 단일 저장소에서 일관된 관리 |
| 디렉터리 구조 | 플러그인별 분리 (plugins/플러그인/) | 단순하고 직관적인 구조 |
| pluginRoot | `./plugins` 설정 | source 경로 단순화 |
| 버전 관리 | 날짜 기반 (YYYY.MM 형식) | 개인 프로젝트에 적합한 직관적 버전 체계 |
| 템플릿 | 포함하지 않음 | 필요 시 Claude Code가 도와줄 수 있으므로 |
| 자동화 스크립트 | 포함하지 않음 | 수동 관리 + Claude Code 활용 |
| 타겟 사용자 | 개인 → 점진적 공유 | 멀티 환경에서 동일 플러그인 세트 사용 |

## 디렉터리 구조

```
claude-code-marketplace/
├── .claude-plugin/
│   └── marketplace.json        # 마켓플레이스 카탈로그 (핵심 파일)
├── plugins/                    # 모든 플러그인의 루트 디렉터리
│   ├── git/                    # Git 워크플로우 자동화 플러그인
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── commands/
│   │       ├── git-commit.md
│   │       ├── github-pr.md
│   │       └── git-rebase-stack.md
│   └── plan/                   # 프로젝트 기획 플러그인
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── commands/
│           ├── deep-interview.md
│           └── fill-linear-ticket.md
├── LICENSE                     # MIT 라이선스
├── README.md                   # 프로젝트 설명 및 사용법
└── SPEC.md                     # 이 스펙 문서
```

## marketplace.json 스키마

```json
{
  "name": "flex-haegul-plugins",
  "owner": {
    "name": "flex-haegul"
  },
  "metadata": {
    "description": "Personal Claude Code plugin marketplace by flex-haegul",
    "version": "2026.02",
    "pluginRoot": "./plugins"
  },
  "plugins": [
    {
      "name": "git",
      "source": "./plugins/git",
      "description": "git diff/log를 분석하여 conventional commit 형식의 한국어 커밋 메시지, GitHub PR 자동 생성, stacked PR rebase 정리",
      "version": "2026.02",
      "keywords": ["git", "commit", "pull-request", "conventional-commit", "korean", "rebase", "stacked-pr"]
    },
    {
      "name": "plan",
      "source": "./plugins/plan",
      "description": "심층 인터뷰를 통해 프로젝트 스펙 문서를 자동 생성하고 Linear 티켓 정보를 채워줌",
      "version": "2026.02",
      "keywords": ["interview", "spec", "linear", "requirements", "planning"]
    }
  ]
}
```

### 플러그인 항목 필드

| 필드 | 필수 | 설명 |
|------|------|------|
| `name` | O | 플러그인 식별자 (kebab-case) |
| `source` | O | 플러그인 디렉터리 경로 (`./plugins/<name>` 형식) |
| `description` | O | 플러그인 설명 |
| `version` | O | 날짜 기반 버전 (YYYY.MM) |
| `keywords` | X | 검색용 키워드 배열 |

## 플러그인 목록

### git

git diff/log를 분석하여 conventional commit 형식의 한국어 커밋 메시지, GitHub PR 자동 생성, stacked PR rebase 정리.

| 커맨드 | 설명 |
|--------|------|
| `git-commit` | git diff 분석 → conventional commit 형식 한국어 커밋 메시지 자동 생성 및 커밋 실행. 커밋 분할 판단, BREAKING CHANGE 감지 포함. |
| `github-pr` | 현재 브랜치 변경사항 분석 → conventional commit 형식 한국어 제목으로 GitHub PR 자동 생성. `--draft` 지원, PR 템플릿 탐색, issue 번호 자동 추출. |
| `git-rebase-stack` | stacked PR의 `git rebase --onto --update-refs` 기반 자동 정리. 자연어 인자, 스택 topology 분석, conflict 처리, 선택적 push. |

### plan

심층 인터뷰를 통해 프로젝트 스펙 문서를 자동 생성하고 Linear 티켓 정보를 채워줌.

| 커맨드 | 설명 |
|--------|------|
| `deep-interview` | AskUserQuestion을 활용한 심층 인터뷰(기술 구현, UI/UX, tradeoffs 등) 후 스펙 문서 자동 생성. |
| `fill-linear-ticket` | Linear 티켓 URL을 받아 title/description/comments/linked issue를 분석하고, 인터뷰를 통해 누락 정보를 채움. |

## 사용법

### 마켓플레이스 추가

```shell
/plugin marketplace add flex-haegul/claude-code-marketplace
```

### 플러그인 설치

```shell
/plugin install <plugin-name>@flex-haegul-plugins
```

### 마켓플레이스 업데이트

```shell
/plugin marketplace update
```

## 플러그인 추가 가이드

새 플러그인을 추가할 때의 단계:

1. `plugins/<플러그인명>/` 디렉터리 생성
2. `.claude-plugin/plugin.json` 매니페스트 작성
3. 플러그인 소스 파일 추가 (commands/, hooks/, agents/ 등)
4. 루트 `.claude-plugin/marketplace.json`의 `plugins` 배열에 항목 추가
5. `claude plugin validate .` 또는 `/plugin validate .`로 검증
6. 커밋 및 푸시

## 제약사항 및 주의사항

- **예약된 이름**: `claude-code-marketplace`, `claude-code-plugins`, `anthropic-marketplace` 등은 마켓플레이스 이름으로 사용 불가
- **파일 참조**: 플러그인은 자체 디렉터리 외부 파일을 `../` 경로로 참조 불가 (설치 시 캐시 디렉터리로 복사되기 때문)
- **경로 탐색**: source에 `..` 포함 불가
- **플러그인 이름**: kebab-case, 공백 없음
- **`${CLAUDE_PLUGIN_ROOT}`**: hooks, MCP server 설정에서 플러그인 설치 경로 참조 시 사용
