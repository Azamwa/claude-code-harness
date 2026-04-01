# Claude Code Skills

Claude Code에서 사용 중인 커맨드, 스킬, 에이전트, 룰 구성 문서.

기반: [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) + 커스텀 스킬

---

## 커맨드 (수동 호출 — `/이름`으로 실행)

| 커맨드 | 설명 |
|--------|------|
| `/plan` | 요구사항 분석 → 리스크 평가 → 단계별 구현 계획. 확인 전까지 코드 작성 안 함 |
| `/tdd` | 테스트 먼저 작성 → 구현 → 커버리지 80%+ 확인 |
| `/code-review` | 로컬 변경사항 또는 GitHub PR 코드 리뷰 |
| `/build-fix` | 빌드/타입 에러 자동 해결 |
| `/e2e` | Playwright E2E 테스트 생성 및 실행 |
| `/learn` | 현재 세션에서 재사용 가능한 패턴 추출 → 스킬로 저장 |
| `/orchestrate` | 에이전트 체이닝 자동 실행 (feature/bugfix/refactor/security/custom) |
| `/verify` | 빌드 → 타입 → 린트 → 테스트 → 보안 → diff 리뷰 종합 검증 |
| `/refactor-clean` | 데드코드 탐지 → 테스트 확인 → 안전 삭제 |

---

## 스킬 (자동 감지 — 상황에 맞으면 Claude가 알아서 로드)

### ECC 스킬

| 스킬 | 설명 |
|------|------|
| `frontend-patterns` | React/Next.js 패턴, 상태 관리, 성능 최적화 |
| `nextjs-turbopack` | Next.js 16+ Turbopack 관련 지식 |
| `coding-standards` | TypeScript/React/Node.js 코딩 표준 |
| `tdd-workflow` | TDD 강제 가이드 (RED → GREEN → REFACTOR) |
| `security-review` | 인증, 사용자 입력, API, 결제 등 보안 코드 작성 시 체크리스트 |
| `search-first` | 코딩 전 기존 라이브러리/패턴 탐색 |
| `continuous-learning` | 세션에서 패턴 자동 추출 → 스킬로 저장 |
| `design-system` | 디자인 시스템 생성/감사, 스타일링 PR 리뷰 |
| `codebase-onboarding` | 새 프로젝트 구조 분석 → 온보딩 가이드 + CLAUDE.md 자동 생성 |
| `verification-loop` | 빌드 → 타입 → 린트 → 테스트 → 보안 → diff 6단계 검증 |
| `context-budget` | 에이전트/스킬/MCP 토큰 소비 진단 및 최적화 추천 |

### 커스텀 스킬

| 스킬 | 설명 |
|------|------|
| `workflow` | 워크플로우 모드 — 기획 → 코딩 → 리뷰 → 수정 → 확인 |
| `colosseum` | 워크플로우 모드 — 기획 → 코딩 → 팀 리뷰(토론) → 수정 → 확인 |
| `war-room` | 버그 디버깅 — 여러 에이전트 협업으로 원인 분석 → 해결 → 검증 |
| `git` | git add → commit → push 한번에 처리 |
| `summarize` | 현재 대화 내용 요약 출력 |

---

## 에이전트

### 판단/분석 (코드 작성 X)

| 에이전트 | 역할 | 모델 |
|----------|------|------|
| `planner` | 기능 기획, 의존성 파악, 단계별 계획 수립 | **Opus** |
| `architect` | 시스템 설계, 확장성, 기술 의사결정 | **Opus** |
| `code-reviewer` | 코드 품질, 보안, 유지보수성 리뷰 | **Opus** |
| `security-reviewer` | OWASP Top 10, 시크릿 탐지, 인젝션/SSRF 체크 | **Opus** |

### 실행/작성 (코드 작성 O)

| 에이전트 | 역할 | 모델 |
|----------|------|------|
| `tdd-guide` | 테스트 먼저 작성 → 구현 → 커버리지 확인 | **Sonnet** |
| `build-error-resolver` | 빌드/타입 에러 최소 수정으로 해결 | **Sonnet** |
| `e2e-runner` | Playwright E2E 테스트 생성/실행/아티팩트 관리 | **Sonnet** |
| `refactor-cleaner` | 데드코드 분석(knip, depcheck) → 안전 삭제 | **Sonnet** |
| `doc-updater` | 코드맵/README/가이드 문서 업데이트 | **Sonnet** |

---

## 룰 (항상 로드)

| 룰 | 설명 |
|----|------|
| `common/` | 코딩 스타일, 개발 워크플로우, Git 규칙, 보안, 테스트, 에이전트 운영 |
| `typescript/` | TypeScript 전용 코딩 스타일, 패턴, 보안, 테스트 |

---

## 디렉토리 구조

```
~/.claude/
├── agents/          # 에이전트 (서브에이전트로 호출)
├── commands/        # 슬래시 커맨드 (/이름으로 직접 호출)
├── skills/          # 스킬 (상황 맞으면 자동 로드 + /이름 호출 가능)
├── rules/           # 룰 (항상 로드)
│   ├── common/
│   └── typescript/
├── CLAUDE.md        # 전역 설정 (페르소나, 컨벤션, 작업 규칙)
└── settings.json    # Claude Code 설정
```
