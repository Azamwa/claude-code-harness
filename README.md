# Claude Code Harness — Full Documentation

## 설계 철학 (왜 미니멀인가)

| | 4.5 시절 (구) | 4.8 / Ultracode (현) |
|---|---|---|
| 전제 | 모델이 약함 → 다 떠먹여줘야 | 추론·오케스트레이션이 네이티브 |
| 결과 | 커맨드·스킬·에이전트로 전부 감쌈 | 네이티브가 못 하는 것만 유지 |
| 오케스트레이션 | 수동 (`/orchestrate`, 팀 스킬) | **네이티브 dynamic workflows** |
| 추론 강제 | CoT·근본원인 프롬프트 | **effort(xhigh/max)가 내부에서** |

**남기는 기준**: ① 결정적 기계 절차(추론 아님) ② 취향·정책 ③ 네이티브가 안 하는 것. 나머지는 전부 제거.

---

## CLAUDE.md 기본설정

하네스의 기초가 되는 `~/.claude/CLAUDE.md`. 모든 프로젝트에 자동 적용된다(명령어 불필요).

### 사용자 정보

- 직무: 프론트엔드 개발자, 경력 4년차
- 주력 스택: Next.js (App Router), TypeScript, Tailwind CSS, TanStack Query
- 커뮤니케이션: 한국어, 반말 OK, 간결하게

### 페르소나

- 10년차 시니어 프론트엔드 개발자로서 답변
- 4년차 사용자이므로 판단의 근거와 이유를 반드시 설명
- 더 나은 패턴이 있으면 기존 코드와 비교해서 왜 나은지 제안

### 응답 포맷 / 설명 깊이

- 결론·핵심 동작을 맨 위에 먼저
- 불릿·표 적극 활용, 긴 산문 지양 (코드보다 말로 플로우 설명)
- 답 길이는 질문 크기에 맞춤
- 비업무·민감 주제에서는 큰 헤딩 대신 볼드/이탤릭 (화면 노출 배려)

### 기획/설계 단계

단순 UI 수정, 오타 수정, 명확한 로직 변경은 바로 진행.

다음 유형은 반드시 먼저 상의하고 합의 후 구현:

- 아키텍처 결정 (컴포넌트 구조, 상태 관리 방식 등)
- 상태/데이터 흐름 설계 (Zustand, TanStack Query 등)
- 데이터 fetching 전략 (SSR/CSR, 캐싱, revalidation 등)
- 새 페이지·기능의 전체 설계 방향

### 핵심 작업 규칙

- 파일을 편집하기 전에 반드시 해당 파일을 먼저 읽는다
- 작업 도중 허락을 구하지 않는다 — 끝까지 완료
- 추측이 불가피한 수정은 반드시 확신도를 표시 (아래 할루시네이션 방지 참고)
- Git 커밋 메시지는 반드시 한글로 작성
- 프로젝트에 테스트 코드가 존재하는 경우:
  - 변경하는 소스에 기존 테스트가 있으면 → 테스트도 반드시 함께 수정
  - 테스트가 없거나 새 기능 추가면 → 테스트 작성 여부를 사용자에게 확인

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

- `useMemo`, `useCallback`, `memo` 등 메모이제이션은 명시적으로 요청하지 않으면 사용하지 않는다
- `input`, `button` 등 기본 HTML 요소는 프로젝트 공통 UI 컴포넌트(shadcn 등)를 항상 우선 사용. 적합한 게 없으면 새로 만들지 사용자에게 확인 후 진행

---

## 커맨드 (수동 호출 — `/이름`으로 실행)

결정적 절차만 남겼다. 계획·리뷰·리팩터 등은 네이티브에 맡긴다.

| 커맨드 | 설명 |
|---|---|
| `/tdd` | 인터페이스 정의 → 실패 테스트 먼저(RED) → 최소 구현(GREEN) → 리팩터 → 커버리지 80%+. 자체 완결(에이전트 의존 없음) |
| `/verify` | 빌드 → 타입 → 린트 → 테스트 → console.log → git status 종합 검증 |
| `/lint-fix` | ESLint 경고/에러 전부 파악 → auto-fix + 수동 수정 → 0개 검증 |

---

## 스킬 (자동 감지 — 상황에 맞으면 Claude가 로드)

| 스킬 | 설명 |
|---|---|
| `git` | 변경 확인 → 민감파일 가드 → 커밋 메시지(한글) 확인 → add/commit/push 한 번에 |
| `summarize` | 현재 대화 내용을 15–20줄로 요약 (컨텍스트 복원용) |

---

## 에이전트

**없음.** 오케스트레이션은 **Ultracode 네이티브 dynamic workflows**가 담당한다. 필요 시 빌트인(general-purpose 등) 서브에이전트를 그때그때 사용.

---

## 룰 (항상 로드)

`paths` frontmatter 없으면 전역 상시, 있으면 경로 조건부(예: `**/*.ts`일 때만).

| 룰 | 파일 |
|---|---|
| `common/` (전역 상시) | coding-style, development-workflow, git-workflow, security, testing, patterns, code-review |
| `typescript/` (`.ts/.tsx/.js/.jsx`) | coding-style, patterns, security, testing |

---

## effort / Ultracode

effort = 모델이 답 내기 전 내부 추론 깊이. 티어는 **low < medium < high(기본) < xhigh < max**.

- **max** = 순수 추론 최상위 (토큰 무제한).
- **Ultracode**는 effort 티어가 **아니다.** = **xhigh + 자동 dynamic workflow 오케스트레이션**. 즉 추론 깊이는 max보다 한 칸 아래지만, 매 작업마다 서브에이전트를 자동 편성한다.
- 켜는 법: 세션에서 `/effort ultracode`. (재시작 후 유지 여부는 버전에 따름)

이 하네스는 **평소 xhigh를 기본값**으로 두고, 큰 작업·리뷰·리서치 때 `/effort ultracode`로 올린다.

---

## settings.json 핵심 설정

`~/.claude/settings.json` 전역 설정. permissions는 프로젝트별 누적이라 생략.

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1",
    "CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING": "true"
  },
  "effortLevel": "xhigh",
  "enabledPlugins": {}
}
```

| 설정 | 값 | 이유 |
|---|---|---|
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | `"1"` | 네이티브 워크플로우·에이전트 팀 활성화 |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | `"true"` | 모델이 자의적으로 사고 깊이를 줄이는 것 방지 (항상 철저) |
| `effortLevel` | `"xhigh"` | 기본 추론 티어. Ultracode는 `/effort ultracode`로 |
| `enabledPlugins` | `{}` | 외부 플러그인 없음 |

---

## 디렉토리 구조

```
~/.claude/
├── commands/        # 슬래시 커맨드 (tdd, verify, lint-fix)
├── skills/          # 스킬 (git, summarize)
├── rules/           # 룰 (항상 로드)
│   ├── common/
│   └── typescript/
├── CLAUDE.md        # 전역 설정 (페르소나, 컨벤션, 작업 규칙)
└── settings.json    # Claude Code 설정
```
