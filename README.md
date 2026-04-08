# Claude Code Harness

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
| `/compound` | 세션 학습 추출 → 목적별 저장소에 분산 저장 |
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

## Compound Engineering — 세션 학습 누적 전략

[Compound Engineering 아티클](https://news.hada.io/topic?id=28272)에서 지적된 Claude Code 품질 저하 문제에 대응하기 위한 전략.
CE 플러그인을 별도로 도입하지 않고, 기존 하네스(skills + rules + memory + CLAUDE.md)로 CE의 핵심 기능을 구현한다.

### 문제

- **adaptive thinking 감축** → 사고 깊이 저하 → "가장 간단한 수정" 패턴 급증
- **읽기:편집 비율 하락** → 코드를 안 읽고 바로 수정하는 행동
- **세션 간 학습 단절** → 같은 실수 반복

### 해결: `/compound` 커맨드

세션에서 학습한 내용을 **목적에 맞는 저장소에 분산 저장**한다.

| # | 추출 대상 | 저장 위치 | 경로 | 이유 |
|---|---|---|---|---|
| 1 | 해결한 버그 패턴 | 스킬 파일 | `~/.claude/skills/learned/*.md` | 프로젝트 무관 재사용 |
| 2 | 아키텍처 결정 | 프로젝트 CLAUDE.md | 각 프로젝트 `CLAUDE.md` | 매 세션 강제 로드 |
| 3 | 컨벤션 금지 규칙 | rules | `~/.claude/rules/{common,typescript}/*.md` | 매 세션 강제 로드 + 프로젝트 횡단 |
| 4 | 다음 작업 주의사항 | 메모리 + 포인터 | memory에 본문, CLAUDE.md에 포인터 | 시한 있는 정보 |

### `/learn`과의 차이

| | `/learn` | `/compound` |
|---|---|---|
| **범위** | 버그/디버깅 패턴에 집중 | 버그 + 아키텍처 + 컨벤션 + 주의사항 |
| **저장 위치** | `skills/learned/` 단일 | 항목별 분산 (skills, CLAUDE.md, rules, memory) |
| **목적** | 재사용 가능한 스킬 축적 | 세션 전체 학습을 프로젝트에 누적 |

### 4번 포인터 패턴

주의사항은 메모리에 저장하되, 프로젝트 CLAUDE.md에 포인터를 남겨 매 세션 인식되도록 한다.

```markdown
# 다음 작업 주의사항
- [250408 GooseTire 결제 플로우 race condition](~/.claude/projects/.../memory/caution-250408.md)
```

- 메모리만 저장하면 Claude가 참조하지 않을 수 있어 **포인터로 강제 노출**
- CLAUDE.md에 전문을 쓰면 비대해지므로 **본문은 메모리, 포인터는 1줄**
- 날짜 + 작업 컨텍스트를 포함해 정리 시 판단이 쉽도록 함

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
