# GitHub PR Command (Conventional Commit, 한국어)

## 개요
현재 브랜치의 변경사항을 분석하여 conventional commit 형식의 한국어 제목으로 GitHub Pull Request를 자동 생성하는 커맨드.

## 인자
- `$ARGUMENTS`: 선택적. `--draft` 플래그를 전달하면 draft PR로 생성한다.

## 도구 우선순위
- GitHub 관련 작업(PR 조회, PR 생성, 레포 정보 조회 등)은 **GitHub MCP 도구를 우선 사용**한다.
- MCP 도구가 실패하거나 사용 불가능한 경우에만 `gh` CLI로 폴백한다.
- git 로컬 명령어(`git status`, `git log`, `git diff` 등)는 Bash로 실행한다.

## 실행 절차

### 1단계: 사전 검증

1. `git status`로 현재 상태를 확인한다.
2. 현재 브랜치가 기본 브랜치(main/master)인 경우 에러로 중단한다.
3. **커밋되지 않은 변경사항이 있으면** 사용자에게 경고 메시지를 출력하되, 계속 진행한다.
4. 리모트에 현재 브랜치가 푸시되어 있는지 확인한다.
   - `git ls-remote --heads origin <현재 브랜치명>`으로 리모트 브랜치 존재 여부를 확인한다.
   - 리모트 브랜치가 없거나, `git log @{upstream}..HEAD --oneline`으로 푸시되지 않은 로컬 커밋이 있으면 `git push -u origin <현재 브랜치명>`을 실행하여 자동으로 푸시한다.
   - 푸시 실패 시 에러 메시지를 출력하고 중단한다.
5. GitHub MCP `list_pull_requests` 도구로 현재 브랜치에서 이미 열린 PR이 있는지 확인한다.
   - 열린 PR이 있으면 **경고 메시지와 기존 PR URL을 출력**하고, 사용자에게 계속 진행할지 AskUserQuestion으로 확인한다.

### 2단계: 기본 브랜치 감지

1. GitHub MCP `get_file_contents` 또는 로컬 `git remote show origin`으로 레포의 기본 브랜치를 확인한다.
2. 감지 실패 시 `main` → `master` 순서로 폴백한다.

### 3단계: 변경사항 분석

1. `git log <base>..HEAD --oneline`으로 커밋 히스토리를 가져온다.
2. `git diff <base>...HEAD --stat`으로 변경 파일 통계를 가져온다.
3. `git diff <base>...HEAD`로 전체 diff를 가져온다.

### 4단계: PR 제목 생성 (Conventional Commit 형식)

```
<type>(<scope>): <한국어 설명>
```

#### Type (기본 타입만 사용)
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 변경
- `style`: 코드 스타일 변경 (포맷팅, 세미콜론 등, 동작 변경 없음)
- `refactor`: 리팩토링 (기능 변경 없음, 버그 수정 아님)
- `test`: 테스트 추가/수정
- `chore`: 빌드, 설정, 패키지 등 기타 변경

커밋 히스토리와 diff를 종합 분석하여 가장 적합한 type을 선택한다.

#### Scope
- 변경된 파일/디렉토리를 기반으로 자동 추론한다.
- 예: `src/auth/` 변경 → `auth`, `components/Button.tsx` 변경 → `button`
- 명확한 scope가 없거나 변경 범위가 넓으면 생략한다.

#### Description
- **반드시 한국어로 작성한다.**
- 72자 이하로 제한한다.
- 마침표로 끝내지 않는다.
- 전체 PR의 핵심 변경사항을 하나의 문장으로 요약한다.

### 5단계: PR Body 생성

#### 템플릿 탐색
1. 레포지토리에서 PR 템플릿을 탐색한다:
   - `.github/pull_request_template.md`
   - `.github/PULL_REQUEST_TEMPLATE/` 디렉토리 내 파일
   - `pull_request_template.md` (루트)
2. 템플릿이 발견되면 해당 템플릿의 형식에 맞춰 내용을 채운다.

#### 기본 템플릿 (레포에 템플릿이 없을 때)
```markdown
## Summary
<!-- PR의 핵심 변경사항을 1-3줄로 요약 -->

## Changes
<!-- 주요 변경사항을 불릿 포인트로 나열 -->
-
```

- Summary와 Changes는 커밋 히스토리와 diff를 분석하여 **한국어로** 자동 작성한다.

#### Issue 연동
- 브랜치명에서 issue 번호 패턴을 자동 추출한다.
  - 지원 패턴: `feature/123-description`, `fix/GH-456`, `issue-789`, `PROJ-123` 등
- issue 번호가 발견되면 body 하단에 `Closes #123` 또는 해당 이슈 참조를 추가한다.

### 6단계: PR 생성

1. GitHub MCP `create_pull_request` 도구로 PR을 생성한다.
   - `title`: 4단계에서 생성한 conventional commit 형식 제목
   - `body`: 5단계에서 생성한 body
   - `base`: 2단계에서 감지한 기본 브랜치
   - `head`: 현재 브랜치명
   - `$ARGUMENTS`에 `--draft`가 포함되어 있으면 `draft: true`로 설정한다.
   - MCP 도구 실패 시 `gh pr create` CLI로 폴백한다.
2. 생성된 PR URL을 사용자에게 출력한다.

## 제외 사항
- breaking change '!' 표시는 자동 추가하지 않는다.
- reviewer, label 자동 지정은 하지 않는다.
- PR 생성 전 사용자 확인(preview)은 하지 않는다.
