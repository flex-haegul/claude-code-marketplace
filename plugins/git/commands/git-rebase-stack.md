# Git Rebase Stack Command (Stacked PR 정리, 한국어)

## 개요
stacked PR 작업 중 base branch 변경, 중간 PR 머지/드롭, 전체 스택 동기화 등의 상황에서 `git rebase --onto <base> <commit> --update-refs`를 활용하여 스택 브랜치들을 자동으로 정리하는 커맨드.

## 인자
- `$ARGUMENTS`: 선택적. 자연어로 의도를 전달한다.
  - 예: `develop`, `step-1이 머지됐으니 정리해줘`, `feature/auth 스택 전체를 main 위로 rebase`
  - 인자가 없으면 현재 체크아웃된 브랜치를 기준으로 스택을 자동 감지한다.
  - base branch를 명시하면 자동 감지 결과를 오버라이드한다.

## 핵심 명령어

```bash
# 스택 rebase의 핵심 명령어
git rebase --onto <new-base> <old-base> --update-refs

# 예시: develop에 step-1이 머지된 후, step-2 이하 스택을 develop 위로 rebase
git rebase --onto develop step-1 --update-refs
```

`--update-refs` 플래그는 rebase 과정에서 스택 내 모든 중간 브랜치 ref를 자동으로 업데이트한다.

## 실행 절차

### 1단계: Working Directory 상태 확인

1. `git status`로 현재 상태를 확인한다.
2. uncommitted changes가 있으면 AskUserQuestion으로 다음 선택지를 제공한다:
   - `git stash`로 임시 저장 후 rebase 진행 (완료 후 자동 `git stash pop`)
   - `git commit`으로 먼저 커밋 후 진행
   - rebase를 중단
3. clean working tree가 확인되면 다음 단계로 진행한다.

### 2단계: Base Branch 감지

1. `$ARGUMENTS`에 base branch가 명시되어 있으면 해당 브랜치를 사용한다.
2. 명시되지 않았으면 자동 감지한다:
   - `gh api repos/{owner}/{repo} --jq .default_branch`로 레포의 기본 브랜치를 확인한다.
     - `{owner}`와 `{repo}`는 `git remote get-url origin`의 출력에서 추출한다.
   - 감지 실패 시 `git remote show origin`의 `HEAD branch` 출력으로 폴백한다.
   - 모두 실패 시 `main` → `develop` → `master` 순서로 폴백한다.
3. base branch를 `git fetch origin <base>`로 최신 상태로 업데이트한다.

### 3단계: 스택 구조 분석

Git topology를 기반으로 스택 구조를 파악한다.

1. `git log --oneline --graph --all --decorate`로 브랜치 관계를 시각적으로 분석한다.
2. `git branch --list`로 로컬 브랜치 목록을 가져온다.
3. 현재 브랜치를 기준으로 부모-자식 관계를 추적한다:
   - `git merge-base <branch-a> <branch-b>`로 브랜치 간 분기점을 확인한다.
   - `git log --oneline <base>..<branch>`로 각 브랜치의 커밋 범위를 파악한다.
4. 필요시 GitHub MCP `list_pull_requests`로 PR 체인 관계를 참고하여 스택 순서를 보정한다.

**스택 topology 판단:**
- linear chain(A→B→C)인지, fork/diamond 구조인지 분석한다.
- fork 구조가 감지되면 각 경로를 독립적으로 처리할 수 있는지 판단하고, 불가능하면 사용자에게 상황을 설명하고 처리 방법을 AskUserQuestion으로 확인한다.

**영향 범위 판단:**
- 스택 구조와 `$ARGUMENTS`의 의도를 종합하여 어떤 브랜치들을 rebase 대상에 포함할지 자동 판단한다.
- 예: 중간 PR이 머지된 경우 → 해당 브랜치 이후의 모든 브랜치를 대상으로 포함
- 예: base가 업데이트된 경우 → 전체 스택을 대상으로 포함

### 4단계: Rebase 계획 수립 및 안전성 판단

rebase 대상, 커밋 수, 스택 깊이, conflict 가능성 등을 종합하여 위험도를 판단한다.

**위험도가 낮다고 판단되면** (예: 브랜치 1-2개, 커밋 수 적음, conflict 가능성 낮음):
- 간단한 요약만 출력하고 바로 실행한다.

**위험도가 높다고 판단되면** (예: 브랜치 3개 이상, 커밋 수 많음, conflict 가능성 있음):
- 다음 정보를 포함한 미리보기를 사용자에게 보여준다:
  - 현재 스택 구조 (브랜치 그래프)
  - rebase 실행 순서
  - 영향받는 브랜치 목록과 각 브랜치의 커밋 수
  - 예상되는 `git rebase --onto` 명령어
- AskUserQuestion으로 진행 여부를 확인한다.

**Rollback 준비:**
- 스택 크기와 복잡도에 따라 backup 브랜치 생성 여부를 자동 판단한다.
- backup이 필요하다고 판단되면 각 브랜치에 대해 `git branch <branch-name>-backup-rebase` 형태로 백업을 생성한다.
- 단순한 스택이면 `git reflog` 기반 복구 안내로 대체한다.

### 5단계: Rebase 실행

1. 스택의 가장 아래(base에 가까운) 브랜치부터 순서대로 처리한다.
2. rebase 대상 브랜치로 체크아웃한다:
   ```bash
   git checkout <target-branch>
   ```
3. rebase를 실행한다:
   ```bash
   git rebase --onto <new-base> <old-base> --update-refs
   ```
4. `--update-refs`가 스택 내 중간 브랜치 ref를 자동 업데이트하므로, 단일 rebase 명령으로 전체 스택이 정리된다.

### 6단계: Conflict 처리

rebase 중 conflict가 발생하면:

1. conflict가 발생한 파일과 내용을 분석한다.
2. 자동 해결을 시도한다:
   - diff 분석을 통해 의미적으로 올바른 해결 방법을 판단한다.
   - 자동 해결이 가능하면 resolve 후 `git add <파일>` → `git rebase --continue`로 진행한다.
3. 자동 해결이 불가능하면:
   - conflict 내용을 사용자에게 보여준다.
   - 스택의 나머지 브랜치 상황을 고려하여 전체 abort vs 부분 적용을 판단한다:
     - 이미 성공한 브랜치가 독립적으로 유효하면 부분 적용을 제안한다.
     - 의존성이 강하면 전체 abort를 권장한다.
   - AskUserQuestion으로 사용자에게 선택지를 제공한다.

### 7단계: 결과 검증 및 보고

rebase 완료 후:

1. `git log --oneline --graph --all --decorate`로 최종 스택 상태를 확인한다.
2. 각 브랜치가 올바른 위치에 있는지 검증한다.
3. 결과 보고의 상세도를 자동 판단한다:
   - **단순한 경우**: 성공 메시지 + push 필요 브랜치 목록
   - **복잡한 경우**: rebase 전/후 비교, 변경된 커밋 수, skip된 커밋, 각 브랜치 상태
4. backup 브랜치가 생성되었고 rebase가 성공했으면, backup 브랜치 정리 여부를 안내한다.

### 8단계: 선택적 Push

1. rebase로 영향받은 브랜치 중 원격에 push된 적 있는 브랜치 목록을 보여준다.
2. AskUserQuestion으로 push 여부를 확인한다:
   - 전체 push
   - 선택적 push (브랜치별 선택)
   - push하지 않음
3. push를 선택하면 상황에 따라 `--force-with-lease` 또는 `--force`를 자동 판단하여 실행한다:
   - 기본적으로 `--force-with-lease`를 사용한다.
   - `--force-with-lease` 실패 시 원인을 분석하여 `--force` 사용 여부를 판단하고, 필요하면 사용자에게 확인한다.

## Ground Rules
- 모든 안내, 질문, 결과 보고는 **한국어**로 출력한다.
- `Co-Authored-By` 헤더는 포함하지 않는다.
- git 로컬 명령어(`git status`, `git log`, `git rebase` 등)는 Bash로 실행한다.
- GitHub 관련 정보 조회가 필요하면 GitHub MCP 도구를 우선 사용하고, 실패 시 `gh` CLI로 폴백한다.

## 제외 사항
- GitHub PR의 base branch 변경은 이 커맨드의 범위 밖이다.
- rebase 후 자동 테스트 실행은 하지 않는다.
- reviewer, label 등 PR 메타데이터 변경은 하지 않는다.
