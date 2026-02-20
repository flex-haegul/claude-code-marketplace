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
│   │   ├── commands/
│   │   │   ├── git-commit.md
│   │   │   ├── github-pr.md
│   │   │   └── git-rebase-stack.md
│   │   └── README.md
│   └── plan/                   # 프로젝트 기획 플러그인
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   ├── deep-interview.md
│       │   └── fill-linear-ticket.md
│       └── README.md
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

## 제약사항 및 주의사항

- **예약된 이름**: `claude-code-marketplace`, `claude-code-plugins`, `anthropic-marketplace` 등은 마켓플레이스 이름으로 사용 불가
- **파일 참조**: 플러그인은 자체 디렉터리 외부 파일을 `../` 경로로 참조 불가 (설치 시 캐시 디렉터리로 복사되기 때문)
- **경로 탐색**: source에 `..` 포함 불가
- **플러그인 이름**: kebab-case, 공백 없음
- **`${CLAUDE_PLUGIN_ROOT}`**: hooks, MCP server 설정에서 플러그인 설치 경로 참조 시 사용
