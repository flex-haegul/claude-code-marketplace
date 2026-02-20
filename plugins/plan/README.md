# plan

프로젝트 기획 지원 플러그인. AskUserQuestion을 활용한 심층 인터뷰를 통해 스펙 문서를 자동 생성하거나, Linear 티켓의 누락된 정보를 채워준다.

## 커맨드

| 커맨드 | 설명 |
|--------|------|
| `deep-interview` | 심층 인터뷰 후 스펙 문서 자동 생성 |
| `fill-linear-ticket` | Linear 티켓 분석 후 인터뷰를 통해 누락 정보 보완 |

## 사용 예시

### deep-interview
`/plan:deep-interview <요청사항>` — 기술 구현, UI/UX, 우려사항, tradeoffs 등에 대해 심층 인터뷰를 진행한 후 스펙 문서를 자동 생성한다.

### fill-linear-ticket
`/plan:fill-linear-ticket <Linear 티켓 URL>` — 티켓의 title, description, comments, linked issue를 분석하고, 인터뷰를 통해 누락된 정보를 채운다.
