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
| 디렉터리 구조 | 카테고리별 분리 (plugins/카테고리/플러그인/) | 플러그인이 늘어날 때 체계적 관리 가능 |
| pluginRoot | `./plugins` 설정 | source 경로 단순화 |
| 버전 관리 | 날짜 기반 (YYYY.MM 형식) | 개인 프로젝트에 적합한 직관적 버전 체계 |
| 초기 플러그인 | 없음 (별도 추가 예정) | 빈 구조만 스캐폴딩 |
| 초기 카테고리 | 최소한 (plugins/ 디렉터리만, 카테고리는 플러그인 추가 시 생성) | 불필요한 빈 디렉터리 방지 |
| 템플릿 | 포함하지 않음 | 필요 시 Claude Code가 도와줄 수 있으므로 |
| 자동화 스크립트 | 포함하지 않음 | 수동 관리 + Claude Code 활용 |
| 타겟 사용자 | 개인 → 점진적 공유 | 멀티 환경에서 동일 플러그인 세트 사용 |

## 디렉터리 구조

```
claude-code-marketplace/
├── .claude-plugin/
│   └── marketplace.json        # 마켓플레이스 카탈로그 (핵심 파일)
├── plugins/                    # 모든 플러그인의 루트 디렉터리
│   └── (카테고리별 디렉터리는 플러그인 추가 시 생성)
│       └── (플러그인 디렉터리)
│           ├── .claude-plugin/
│           │   └── plugin.json # 플러그인 매니페스트
│           └── (플러그인 소스 파일들)
├── LICENSE                     # MIT 라이선스
├── README.md                   # 프로젝트 설명 및 사용법
└── SPEC.md                     # 이 스펙 문서
```

### 카테고리 디렉터리 컨벤션 (추후 사용)

플러그인 추가 시 아래 카테고리 중 선택하여 디렉터리 생성:

| 카테고리 | 디렉터리명 | 설명 |
|----------|-----------|------|
| 코드 품질 | `code-quality` | 코드 리뷰, 린팅, 포매팅, 보안 검사 |
| 개발 워크플로우 | `workflow` | Git 자동화, CI/CD, 배포, 프로젝트 관리 |
| AI 도구 | `ai-tools` | 커스텀 프롬프트, AI 에이전트, 특수 목적 스킬 |
| DevOps | `devops` | 인프라, 모니터링, 컨테이너 관련 |
| 보안 | `security` | 보안 스캐닝, 취약점 분석, 컴플라이언스 |
| 데이터 | `data` | 데이터 처리, 분석, 시각화 |

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
  "plugins": []
}
```

### 플러그인 추가 시 항목 예시

```json
{
  "name": "example-plugin",
  "source": "code-quality/example-plugin",
  "description": "플러그인 설명",
  "version": "2026.02",
  "category": "code-quality",
  "keywords": ["example"]
}
```

> **참고**: `pluginRoot`가 `./plugins`로 설정되어 있으므로, source에서 `./plugins/` 접두사를 생략한다.

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

1. `plugins/<카테고리>/<플러그인명>/` 디렉터리 생성
2. `.claude-plugin/plugin.json` 매니페스트 작성
3. 플러그인 소스 파일 추가 (skills/, hooks/, agents/ 등)
4. 루트 `.claude-plugin/marketplace.json`의 `plugins` 배열에 항목 추가
5. `claude plugin validate .` 또는 `/plugin validate .`로 검증
6. 커밋 및 푸시

## 제약사항 및 주의사항

- **예약된 이름**: `claude-code-marketplace`, `claude-code-plugins`, `anthropic-marketplace` 등은 마켓플레이스 이름으로 사용 불가
- **파일 참조**: 플러그인은 자체 디렉터리 외부 파일을 `../` 경로로 참조 불가 (설치 시 캐시 디렉터리로 복사되기 때문)
- **경로 탐색**: source에 `..` 포함 불가
- **플러그인 이름**: kebab-case, 공백 없음
- **`${CLAUDE_PLUGIN_ROOT}`**: hooks, MCP server 설정에서 플러그인 설치 경로 참조 시 사용
