# Git Commit Command (Conventional Commit, 한국어)

## 개요
git diff를 분석하여 conventional commit 형식의 한국어 커밋 메시지를 자동 생성하고 커밋을 실행하는 커맨드.

## 실행 절차

### 1단계: 변경사항 확인
1. `git status`를 실행하여 현재 상태를 확인한다.
2. staged 변경사항이 없으면 `git add -A`로 모든 변경사항을 자동으로 staging한다.
3. 변경사항이 아예 없으면(clean working tree) 사용자에게 알리고 중단한다.

### 2단계: Diff 분석
1. `git diff --cached`로 staged 변경사항의 전체 diff를 가져온다.
2. `git diff --cached --stat`으로 변경 파일 목록과 통계를 가져온다.

### 3단계: 커밋 분할 판단
diff를 분석하여 논리적으로 분리되어야 하는 변경사항이 있는지 판단한다.

**분할이 필요한 경우의 예시:**
- 서로 관련 없는 기능 추가가 섞여 있을 때
- 버그 수정과 새 기능이 동시에 포함되어 있을 때
- 리팩토링과 기능 변경이 혼재할 때
- 설정 파일 변경과 코드 변경이 독립적일 때

**분할이 필요하다고 판단되면:**
- AskUserQuestion 도구를 사용하여 분할 계획을 사용자에게 제안한다.
- 각 커밋에 포함될 파일 목록과 커밋 메시지 요약을 보여준다.
- 의존성/논리적 순서를 고려하여 커밋 순서를 자동 정렬한다 (예: 인프라 → 로직 → 테스트).
- **반드시 "전체를 한 번에 커밋" 옵션도 함께 제공한다.**
- 사용자가 분할을 승인하면, 각 그룹별로 `git add <파일들>` → `git commit` 을 순차 실행한다.

**분할이 불필요하다고 판단되면:**
- 바로 4단계로 진행한다.

### 4단계: 커밋 메시지 생성
다음 규칙에 따라 커밋 메시지를 생성한다.

#### Conventional Commit 형식
```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Type (기본 타입만 사용)
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `docs`: 문서 변경
- `style`: 코드 스타일 변경 (포맷팅, 세미콜론 등, 동작 변경 없음)
- `refactor`: 리팩토링 (기능 변경 없음, 버그 수정 아님)
- `test`: 테스트 추가/수정
- `chore`: 빌드, 설정, 패키지 등 기타 변경

#### Scope
- 변경된 파일/디렉토리를 기반으로 자동 추론한다.
- 예: `src/auth/` 변경 → `auth`, `components/Button.tsx` 변경 → `button`
- 명확한 scope가 없으면 생략한다.

#### Subject
- **반드시 한국어로 작성한다.**
- 72자 이하로 제한한다.
- 마침표로 끝내지 않는다.
- 명령형이 아닌 설명형으로 작성한다.
- 예: `feat(auth): 로그인 페이지에 소셜 로그인 기능 추가`

#### Body
- **반드시 한국어로 작성한다.**
- 불릿 포인트(-) 형식으로 변경사항을 나열한다.
- 각 항목은 "무엇을" "왜" 변경했는지 설명한다.
- subject와 body 사이에 빈 줄을 넣는다.
- 예:
  ```
  - OAuth2 기반 Google, GitHub 소셜 로그인 연동
  - 기존 이메일 로그인과 소셜 계정 자동 연결 처리
  - 소셜 로그인 실패 시 에러 핸들링 추가
  ```

#### Footer
- BREAKING CHANGE가 있다고 판단되면 footer에 포함한다.
- BREAKING CHANGE 판단은 diff 내용을 종합적으로 분석하여 결정한다.
  - 공개 API 시그니처 변경, 함수/메서드 제거, 필수 파라미터 추가, 반환 타입 변경, export 제거 등을 고려한다.
- BREAKING CHANGE가 있으면 type 뒤에 `!`를 붙이고 footer에 상세 내용을 기술한다.
- 예:
  ```
  feat(api)!: 사용자 인증 API 응답 형식 변경

  - 기존 flat 구조에서 nested 구조로 응답 형식 변경
  - access_token 필드명을 accessToken으로 변경

  BREAKING CHANGE: 인증 API 응답의 JSON 구조가 변경되어 기존 클라이언트 코드 수정 필요
  ```
- issue 참조는 포함하지 않는다.

### 5단계: 커밋 실행
- 생성된 메시지로 `git commit`을 바로 실행한다 (사용자 확인 없이 자동 실행).
- Co-Authored-By 헤더는 포함하지 않는다.
- 커밋 성공 후 `git log --oneline -1`로 결과를 사용자에게 보여준다.

## 제외 사항
- merge commit, revert commit은 이 커맨드의 대상이 아니다.
- issue/ticket 번호 참조는 포함하지 않는다.

## 인자
이 커맨드는 `$ARGUMENTS`를 받지 않는다. 항상 현재 git 상태를 기반으로 동작한다.
