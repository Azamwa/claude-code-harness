# Claude Code Harness

Claude Code에서 사용 중인 커맨드, 스킬, 에이전트, 룰 구성 문서.

기반: [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) + 커스텀 스킬

---

## CLAUDE.md 기본설정

하네스의 기초가 되는 `~/.claude/CLAUDE.md` 규칙. 모든 프로젝트에 적용된다.

### 사용자 정보

- 직무: 프론트엔드 개발자, 경력 4년차
- 주력 스택: Next.js (App Router), TypeScript, Tailwind CSS, TanStack Query
- 커뮤니케이션: 한국어, 반말 OK, 간결하게

### 페르소나

- 10년차 시니어 프론트엔드 개발자로서 답변
- 4년차 사용자이므로 판단의 근거와 이유를 반드시 설명
- 더 나은 패턴이 있으면 기존 코드와 비교해서 왜 나은지 제안

### 기획/설계 단계

단순 UI 수정, 오타 수정, 명확한 로직 변경은 바로 진행.

다음 작업 유형에서는 반드시 사용자와 먼저 상의하고, 방향이 합의된 후에 구현을 시작:

- 아키텍처 결정 (컴포넌트 구조, 상태 관리 방식 선택 등)
- 상태/데이터 흐름 설계 (Zustand, TanStack Query 등)
- 데이터 fetching 전략 (SSR/CSR, 캐싱, revalidation 방식 등)
- 새 페이지·기능의 전체 설계 방향

### 핵심 작업 규칙

- 버그 원인 분석 시 판단 근거와 고려한 대안을 먼저 설명한 뒤 진행 (CoT)
- "가장 단순한 수정" 방식으로 접근하지 않는다 — 항상 근본 원인을 파악하고 올바른 해결책을 적용
- 파일을 편집하기 전에 반드시 해당 파일을 먼저 읽는다
- 작업 도중 허락을 구하지 않는다 — 끝까지 완료
- 추측이 불가피한 수정은 반드시 확신도를 표시 (아래 할루시네이션 방지 참고)
- Git 커밋 메시지는 반드시 한글로 작성
- 프로젝트에 테스트 코드가 존재하는 경우:
  - 변경하는 소스에 기존 테스트가 있으면 → 테스트도 반드시 함께 수정
  - 변경하는 소스에 테스트가 없거나, 새 기능을 추가하는 경우 → 테스트 작성 여부를 사용자에게 확인

### 할루시네이션 방지

**확신도 표시** — 버그 분석, 아키텍처 제안, 추측성 답변에는 반드시 확신도를 붙인다:

| 확신도 | 의미 |
|---|---|
| [5] | 코드/문서에서 직접 확인한 사실 |
| [4] | 높은 확신, 근거 있음 |
| [3] | 합리적 추론이지만 검증 필요 |
| [2] | 추측, 다른 가능성 있음 |
| [1] | 거의 모름 |

**외부 정보 참조** — URL을 직접 제시하지 않는다 (할루시네이션 위험). 대신 검색 키워드를 제시:

- O: "Next.js app router middleware redirect 공식문서 참고"
- X: `https://nextjs.org/docs/...` (존재하지 않을 수 있음)

### 코드 컨벤션

- `useMemo`, `useCallback`, `memo` 등 메모이제이션은 사용자가 명시적으로 요청하지 않으면 사용하지 않는다
- `input`, `button` 등 기본 HTML 요소는 프로젝트의 공통 UI 컴포넌트(shadcn 등)를 항상 우선 사용한다. 적합한 공통 컴포넌트가 없으면 새로 만들지 사용자에게 확인 후 진행

---

## 커맨드 (수동 호출 — `/이름`으로 실행)

| 커맨드            | 설명                                                                                                        |
| ----------------- | ----------------------------------------------------------------------------------------------------------- |
| `/plan`           | 요구사항 분석 → 리스크 평가 → 단계별 구현 계획. 확인 전까지 코드 작성 안 함                                 |
| `/tdd`            | 테스트 먼저 작성 → 구현 → 커버리지 80%+ 확인                                                                |
| `/code-review`    | 로컬 변경사항 또는 GitHub PR 코드 리뷰 (codex 병렬 레인 기본 ON, `--no-codex`로 opt-out)                    |
| `/build-fix`      | 빌드/타입 에러 자동 해결                                                                                    |
| `/lint-fix`       | ESLint 경고/에러 전부 파악 → auto-fix + 수동 수정 → 0개 검증                                                |
| `/e2e`            | Playwright E2E 테스트 생성 및 실행                                                                          |
| `/learn`          | 현재 세션에서 재사용 가능한 패턴 추출 → 스킬로 저장                                                         |
| `/compound`       | 버그패턴·아키텍처·컨벤션·주의사항을 목적별로 분산 저장 → [상세](#compound-engineering--세션-학습-누적-전략) |
| `/orchestrate`    | 에이전트 체이닝 자동 실행 (feature/bugfix/refactor/security/**review-parallel**/custom)                     |
| `/verify`         | 빌드 → 타입 → 린트 → 테스트 → 보안 → diff 리뷰 종합 검증                                                    |
| `/refactor-clean` | 데드코드 탐지 → 테스트 확인 → 안전 삭제                                                                     |

---

## 스킬 (자동 감지 — 상황에 맞으면 Claude가 알아서 로드)

### ECC 스킬

| 스킬                  | 설명                                                         |
| --------------------- | ------------------------------------------------------------ |
| `frontend-patterns`   | React/Next.js 패턴, 상태 관리, 성능 최적화                   |
| `nextjs-turbopack`    | Next.js 16+ Turbopack 관련 지식                              |
| `coding-standards`    | TypeScript/React/Node.js 코딩 표준                           |
| `tdd-workflow`        | TDD 강제 가이드 (RED → GREEN → REFACTOR)                     |
| `security-review`     | 인증, 사용자 입력, API, 결제 등 보안 코드 작성 시 체크리스트 |
| `search-first`        | 코딩 전 기존 라이브러리/패턴 탐색                            |
| `continuous-learning` | 세션에서 패턴 자동 추출 → 스킬로 저장                        |
| `design-system`       | 디자인 시스템 생성/감사, 스타일링 PR 리뷰                    |
| `codebase-onboarding` | 새 프로젝트 구조 분석 → 온보딩 가이드 + CLAUDE.md 자동 생성  |
| `verification-loop`   | 빌드 → 타입 → 린트 → 테스트 → 보안 → diff 6단계 검증         |
| `context-budget`      | 에이전트/스킬/MCP 토큰 소비 진단 및 최적화 추천              |

### 커스텀 스킬

| 스킬        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| `workflow`  | 워크플로우 모드 — 기획 → 코딩 → 리뷰 → 수정 → 확인           |
| `colosseum` | 워크플로우 모드 — 기획 → 코딩 → 팀 리뷰(토론) → 수정 → 확인  |
| `war-room`  | 버그 디버깅 — 여러 에이전트 협업으로 원인 분석 → 해결 → 검증 |
| `git`       | git add → commit → push 한번에 처리                          |
| `summarize` | 현재 대화 내용 요약 출력                                     |

---

## 에이전트

### 판단/분석 (코드 작성 X)

| 에이전트            | 역할                                        | 모델     |
| ------------------- | ------------------------------------------- | -------- |
| `planner`           | 기능 기획, 의존성 파악, 단계별 계획 수립    | **Opus** |
| `architect`         | 시스템 설계, 확장성, 기술 ㅋ의사결정        | **Opus** |
| `code-reviewer`     | 코드 품질, 보안, 유지보수성 리뷰            | **Opus** |
| `security-reviewer` | OWASP Top 10, 시크릿 탐지, 인젝션/SSRF 체크 | **Opus** |

### 실행/작성 (코드 작성 O)

| 에이전트               | 역할                                          | 모델       |
| ---------------------- | --------------------------------------------- | ---------- |
| `tdd-guide`            | 테스트 먼저 작성 → 구현 → 커버리지 확인       | **Sonnet** |
| `build-error-resolver` | 빌드/타입 에러 최소 수정으로 해결             | **Sonnet** |
| `e2e-runner`           | Playwright E2E 테스트 생성/실행/아티팩트 관리 | **Sonnet** |
| `refactor-cleaner`     | 데드코드 분석(knip, depcheck) → 안전 삭제     | **Sonnet** |
| `doc-updater`          | 코드맵/README/가이드 문서 업데이트            | **Sonnet** |

---

## 룰 (항상 로드)

| 룰            | 설명                                                                |
| ------------- | ------------------------------------------------------------------- |
| `common/`     | 코딩 스타일, 개발 워크플로우, Git 규칙, 보안, 테스트, 에이전트 운영 |
| `typescript/` | TypeScript 전용 코딩 스타일, 패턴, 보안, 테스트                     |

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

| #   | 추출 대상          | 저장 위치          | 경로                                       | 이유                              |
| --- | ------------------ | ------------------ | ------------------------------------------ | --------------------------------- |
| 1   | 해결한 버그 패턴   | 스킬 파일          | `~/.claude/skills/learned/*.md`            | 프로젝트 무관 재사용              |
| 2   | 아키텍처 결정      | 프로젝트 CLAUDE.md | 각 프로젝트 `CLAUDE.md`                    | 매 세션 강제 로드                 |
| 3   | 컨벤션 금지 규칙   | rules              | `~/.claude/rules/{common,typescript}/*.md` | 매 세션 강제 로드 + 프로젝트 횡단 |
| 4   | 다음 작업 주의사항 | 메모리 + 포인터    | memory에 본문, CLAUDE.md에 포인터          | 시한 있는 정보                    |

### `/learn`과의 차이

|               | `/learn`                | `/compound`                                    |
| ------------- | ----------------------- | ---------------------------------------------- |
| **범위**      | 버그/디버깅 패턴에 집중 | 버그 + 아키텍처 + 컨벤션 + 주의사항            |
| **저장 위치** | `skills/learned/` 단일  | 항목별 분산 (skills, CLAUDE.md, rules, memory) |
| **목적**      | 재사용 가능한 스킬 축적 | 세션 전체 학습을 프로젝트에 누적               |

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

## Codex 병렬 리뷰

OpenAI Codex CLI 플러그인을 활용한 2-lane / 3-way 병렬 리뷰. Claude 리뷰 단독으로는 놓치기 쉬운 이슈를 다른 관점의 리뷰와 교차 검증한다.

### 작동 방식

- Claude 리뷰와 Codex 리뷰를 **백그라운드로 병렬 실행** → 총 소요 시간은 `max(Claude, Codex)`
- Codex 출력은 **verbatim**으로 리포트에 포함 (요약·수정·머지 금지)
- 최종 판단(BLOCK/REQUEST CHANGES/APPROVE)은 **Claude 기준**, Codex는 advisory — 검증 없이 결정 변경 금지
- Codex 미응답 시 Claude 리뷰 완료 후 **90초 타임아웃** 후 컷오프
- 플러그인 미설치 시 자동 fallback → Claude 단독 리뷰

### 사용법

| 커맨드                                     | 동작                                                        |
| ------------------------------------------ | ----------------------------------------------------------- |
| `/code-review`                             | Claude + Codex 2-lane 리뷰 (기본 ON)                        |
| `/code-review --no-codex`                  | Claude 단독 리뷰                                            |
| `/orchestrate review-parallel`             | 3-way 병렬 (code-reviewer + security-reviewer + codex)      |
| `/orchestrate review-parallel --base main` | main 대비 브랜치 diff로 3-way 병렬                          |

### 설계 원칙

- **Codex는 advisory** — Codex가 CRITICAL을 외쳐도 Claude가 코드를 직접 검증해 Claude findings로 승격시키지 않는 한 Decision은 바뀌지 않는다. 맹신하지 않기 위한 안전장치.
- **Verbatim 보존** — 요약/패러프레이즈 시 정보가 왜곡될 수 있어 `/codex:review` 원문을 그대로 리포트에 포함.

---

## settings.json 핵심 설정

`~/.claude/settings.json`에서 관리하는 전역 설정. permissions는 프로젝트별로 누적되므로 생략.

```json
{
  "env": {
    "CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING": "true",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1",
    "CLAUDE_CODE_EFFORT_LEVEL": "max"
  },
  "effortLevel": "high",
  "enabledPlugins": {
    "codex@openai-codex": true
  },
  "extraKnownMarketplaces": {
    "openai-codex": {
      "source": { "source": "github", "repo": "openai/codex-plugin-cc" }
    }
  }
}
```

| 설정                                    | 값                 | 이유                                                            |
| --------------------------------------- | ------------------ | --------------------------------------------------------------- |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | `"true"`           | 모델이 자의적으로 사고 깊이를 줄이는 것 방지                    |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS`  | `"1"`              | 에이전트 팀 기능 활성화                                         |
| `CLAUDE_CODE_EFFORT_LEVEL`              | `"max"`            | 항상 최대 사고 깊이로 동작 (env가 effortLevel보다 우선)         |
| `effortLevel`                           | `"high"`           | settings.json 허용값은 low/medium/high만 지원. max는 env로 설정 |
| `enabledPlugins`                        | codex@openai-codex | Codex 병렬 리뷰 플러그인 활성화 → [상세](#codex-병렬-리뷰)      |
| `extraKnownMarketplaces`                | openai-codex       | Codex 플러그인 마켓플레이스 등록 (github.com/openai/codex-plugin-cc) |

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
├── plugins/         # 플러그인 (codex 등, settings.json의 enabledPlugins로 활성화)
├── CLAUDE.md        # 전역 설정 (페르소나, 컨벤션, 작업 규칙)
└── settings.json    # Claude Code 설정
```
