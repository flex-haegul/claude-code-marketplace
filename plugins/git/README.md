# git

Git 워크플로우 자동화 플러그인. staged 변경사항의 diff를 분석하여 conventional commit 형식의 한국어 커밋 메시지를 자동 생성하고, PR 생성과 stacked PR rebase까지 처리한다.

## 커맨드

| 커맨드 | 설명 |
|--------|------|
| `git-commit` | git diff 분석 → conventional commit 형식 한국어 커밋 메시지 자동 생성 및 커밋 실행 |
| `github-pr` | 현재 브랜치 변경사항 분석 → conventional commit 형식 한국어 제목으로 GitHub PR 자동 생성 |
| `git-rebase-stack` | stacked PR의 `git rebase --onto --update-refs` 기반 스택 브랜치 자동 정리 |

## 사용 예시

### git-commit
`/commit` — staged 변경사항이 없으면 자동으로 전체 staging 후, diff를 분석하여 커밋 메시지를 생성한다. 논리적으로 분리되어야 하는 변경사항이 있으면 커밋 분할을 제안한다.

### github-pr
`/pr` — 현재 브랜치의 커밋 히스토리와 diff를 분석하여 PR을 생성한다. 미푸시 브랜치는 자동으로 push하고, 브랜치명에서 issue 번호를 추출하여 연동한다.
`/pr --draft` — draft PR로 생성한다.

### git-rebase-stack
`/rebase-stack` — 현재 브랜치 기준으로 스택을 자동 감지하여 rebase를 실행한다.
`/rebase-stack step-1이 머지됐으니 정리해줘` — 자연어로 의도를 전달하면 스택 topology를 분석하여 적절한 rebase를 실행한다.
